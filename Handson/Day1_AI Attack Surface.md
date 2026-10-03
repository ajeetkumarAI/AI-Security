# Topic: AI Attack Surface

---

## Part A: What is an attack surface? (the basics)

An **attack surface** is every point where an attacker can interact with, influence, or extract something from a system.

```
        THE HOUSE ANALOGY

   🪟 window      🚪 front door     🚪 back door
      │               │                │
      └───────────────┼────────────────┘
                      ▼
              ┌──────────────┐
              │   YOUR HOUSE │   ← every door/window = attack surface
              │  (the asset) │   ← more doors = more places to attack
              └──────────────┘   ← closing unused doors = "attack surface reduction"
```

Three terms people mix up:

```
 ATTACK SURFACE ──► the DOORS that exist
                    (chat box, API, uploaded files, vector DB...)

 ATTACK VECTOR  ──► the METHOD used on a door
                    (malicious prompt, poisoned PDF, stolen API key)

 ATTACK PATH    ──► the full ROUTE from entry to impact
                    (upload PDF → hidden instruction → tool call → data leak)
```

| Term | Question it answers | AI example |
|---|---|---|
| Surface | Where can I touch it? | The "upload document" feature |
| Vector | How do I use that? | PDF with hidden instructions |
| Path | What chain gets me to impact? | PDF → LLM obeys → emails data out |

**Why AI has a bigger surface than normal software:** a normal app has code, a database, and a network. An AI app has all of that plus **data, a model, prompts, retrieval, tools, and a natural-language interface that accepts nearly any input.**

---

## Part B: The master map (12 surfaces)

Memorize this diagram. Every AI attack surface lives on it.

```
 ┌─────────────────────────── DEVELOPMENT / TRAINING SIDE ───────────────────────────┐
 │                                                                                    │
 │  ⑤ Training data ──► ⑥ Supply chain ──► Training ──► ④ MODEL FILE (weights)        │
 │     (scraped, user,     (model hubs,       pipeline       │                         │
 │      third-party)        libraries,                       │                         │
 │                          datasets)                        │                         │
 └───────────────────────────────────────────────────────────┼─────────────────────────┘
                                                             │ deployed
 ┌─────────────────────────── RUNTIME / APPLICATION SIDE ────┼─────────────────────────┐
 │                                                           ▼                         │
 │  ① USER ──► [Chat UI] ──► [Backend / Orchestrator] ──► ④ [ LLM ]                    │
 │    input                        │    ▲        ▲              │                      │
 │                                 │    │        │              │                      │
 │              ③ System prompt ───┘    │        │              ▼                      │
 │                                      │        │      ⑦ [Tools / Plugins / Agents]   │
 │  ② INDIRECT INPUT                    │        │         email, DB, files, web, code │
 │  (docs, web, emails) ──► ⑧ [Vector DB / Memory]                   │                 │
 │                                                                   ▼                 │
 │                                                          ⑩ OUTPUT ──► [Browser/     │
 │                                                              (rendered, executed)   │
 │                                                                       downstream]   │
 │  ⑨ INFRASTRUCTURE: API gateway, cloud, GPUs, secrets/keys, networking               │
 │  ⑪ HUMANS: users, admins, labelers, developers                                      │
 │  ⑫ MONITORING / FEEDBACK / LOGS: thumbs-up data, retraining, stored chats           │
 └─────────────────────────────────────────────────────────────────────────────────────┘
```

| # | Surface | One-line summary |
|---|---|---|
| ① | Direct user input | What users type or upload |
| ② | Indirect input | Content the model reads that the *user didn't type* |
| ③ | System prompt/context | Hidden rules and secrets in the prompt |
| ④ | Model | Weights, behavior, API responses |
| ⑤ | Training data | What the model learned from |
| ⑥ | Supply chain | Everything you download and trust |
| ⑦ | Tools/agents | What the model can *do* |
| ⑧ | Vector DB/memory | Stored knowledge and conversation memory |
| ⑨ | Infrastructure | Classic cloud/API/identity layer |
| ⑩ | Output | What happens to the model's response |
| ⑪ | Humans | Social and insider angle |
| ⑫ | Feedback/logs | Data loops back into the system |

---

## Part C: Each surface in detail

### Surface ① Direct user input

Anything the user can send to the model.

```
  ATTACKER                                    
     │  text, files, images, audio, URLs      
     ▼                                        
 ┌─────────┐   ┌──────────┐   ┌─────────┐   
 │ Chat UI │──►│ Backend  │──►│  LLM    │   
 └─────────┘   └──────────┘   └─────────┘   
   inputs accepted:                          
   • free text      → prompt injection, jailbreaks       
   • images         → hidden text inside image           
   • audio          → hidden commands in sound           
   • files (PDF/CSV)→ malicious content, parser bugs     
   • very long text → cost abuse, context overflow       
```

**Why it matters:** natural language has no fixed format, so you can't simply validate it like a "numbers only" field. Multimodal inputs (images, audio) multiply the surface.

---

### Surface ② Indirect input (the most underrated surface)

The model reads content the user never typed. The attacker only needs to **plant content somewhere the model will read it.**

```
 DIRECT:     Attacker ──────────────────────────────► LLM
 
 INDIRECT:   Attacker ──plants──► [Web page / email / PDF / wiki / ticket]
                                         │
                       Victim user asks a normal question
                                         │
                                         ▼
                              [Backend fetches that content]
                                         │
                                         ▼
                                       [ LLM ] ← reads hidden instructions
                                                  as if they were legitimate
```

**Where indirect content comes from:**

| Source | Who can write to it? |
|---|---|
| Public web pages | Anyone |
| Incoming emails | Anyone who can email you |
| Shared documents / wikis | Any collaborator |
| Support tickets / reviews / comments | Customers |
| Tool outputs (API responses) | The API owner, or whoever controls what it returns |
| Code repositories | Contributors |

**Key insight:** the victim does nothing wrong. The attacker is not even in the conversation.

---

### Surface ③ System prompt and context window

The hidden instructions plus everything packed into the context.

```
 ┌──────────────── CONTEXT WINDOW (what the LLM sees) ───────────────┐
 │ [System prompt]      rules, persona, sometimes SECRETS ⚠          │
 │ [Conversation history]                                            │
 │ [Retrieved documents]  ← from ②, ⑧                                │
 │ [Tool results]         ← from ⑦                                   │
 │ [Current user msg]     ← from ①                                   │
 └───────────────────────────────────────────────────────────────────┘
       Attack goals: LEAK it, OVERRIDE it, or FLOOD it
```

**Common mistake:** developers put API keys, internal URLs, or business logic in the system prompt, assuming it's hidden. Treat the system prompt as **extractable**.

---

### Surface ④ The model itself

```
              ┌──────────────────────────┐
  Attacker ──►│ Model (API or weights)   │
              └──────────────────────────┘
   What an attacker can target:
   • Behavior     → adversarial inputs, jailbreaks
   • Knowledge    → extract memorized training data
   • The model    → steal it by querying many times (extraction)
   • Weights file → theft from storage or a misconfigured bucket
   • Membership   → "was this person's data used in training?"
```

The model is both an **asset** (valuable IP) and a **surface** (its behavior can be manipulated).

---

### Surface ⑤ Training and fine-tuning data

```
 [Web scrape] ─┐
 [User data]  ─┼──► [Dataset] ──► [Training] ──► Model
 [3rd party]  ─┘        ▲
                        │
              Attacker injects here
              (poisoning, backdoor triggers, bad labels)
```

Rule: **anywhere data enters the training pipeline is a door**, including user feedback used for fine-tuning.

---

### Surface ⑥ AI supply chain

You rarely build everything yourself. Each download is trust you've extended.

```
 YOUR APP
   ├── Pre-trained model   ◄── model hub (anyone can upload)
   ├── Python libraries    ◄── package registry (typosquatting, malicious updates)
   ├── Datasets            ◄── public dataset sites
   ├── Plugins / MCP servers ◄── third-party tool providers
   ├── Prompt templates    ◄── community repos
   └── Base container image ◄── image registry

   If ANY one is malicious, it runs INSIDE your trust boundary.
```

**Classic example:** some model file formats (like pickle) can execute code when loaded, so "just downloading a model" can mean running an attacker's code.

---

### Surface ⑦ Tools, plugins, and agents (the impact multiplier)

This surface decides **how bad things get**. A model that only talks is low-impact. A model that acts is high-impact.

```
                         ┌──► [Read files]       
                         ├──► [Send email]       
 [ LLM / Agent ] ───────►├──► [Query database]   
   (decides what         ├──► [Run code]         
    to call)             ├──► [Browse web]       
                         └──► [Call internal APIs]
                        
   Attack goal: make the model call a tool the attacker wants,
   with parameters the attacker chose, using the USER's permissions.
```

Risks to remember:
- **Excessive permissions:** the agent has admin rights it doesn't need.
- **Confused deputy:** the agent uses *its* privileges on behalf of an *attacker's* instruction.
- **Tool chaining:** read-secret tool + send-email tool = exfiltration.

---

### Surface ⑧ Vector database and memory

```
 [Documents] ──embed──► [ VECTOR DB ] ◄──search── [Backend] ──► LLM
                              ▲
                              │
         Attack points:       │
         • Write access (poison documents that will be retrieved)
         • Read access (retrieve documents the user shouldn't see)
         • No per-user access control (everyone searches everything)
         • Persistent memory poisoned once, affects future sessions
```

A very common real-world flaw: the vector DB holds **everyone's** documents, and retrieval doesn't check *who is asking*.

---

### Surface ⑨ Infrastructure (classic security still applies)

```
 Internet ──► [API Gateway] ──► [App servers] ──► [Model endpoint]
                  │                 │                  │
              weak auth         leaked keys        exposed inference
              no rate limit     env variables      server, no auth
                                in repo/logs       open GPU dashboards
```

Many "AI hacks" are really **ordinary misconfigurations**: an exposed endpoint, a leaked API key, an open storage bucket. Don't skip the basics.

---

### Surface ⑩ Output handling

What happens **after** the model responds.

```
 [ LLM output ] ──┬──► Rendered in a browser ──► XSS, malicious links,
                  │                              image tags that leak data
                  ├──► Passed to a shell/DB   ──► command/SQL injection
                  ├──► Fed to another agent   ──► injection spreads
                  └──► Shown as "truth"       ──► misinformation, over-trust
```

**Rule:** treat model output as **untrusted input** to whatever consumes it next.

---

### Surface ⑪ Humans

```
 Users     → tricked by convincing AI output, or by fake AI tools
 Admins    → phished for cloud/model credentials
 Labelers  → can be bribed or fooled into bad labels
 Insiders  → direct access to data, weights, prompts
```

---

### Surface ⑫ Monitoring, feedback, and logs

```
 [User chats] ──► [Logs / Analytics] ──► may store SENSITIVE data
      │
      └─ 👍/👎 feedback ──► [Retraining set] ──► poisoning route
 
 Attack points: log access, feedback manipulation, analytics vendors
```

---

## Part D: Direct vs indirect, and who the attacker is

### D1. Access level changes the surface

```
 ANONYMOUS INTERNET USER      sees: public chat, public API, website
        ▼ (log in)
 AUTHENTICATED USER           sees: more features, uploads, history, tools
        ▼ (more privilege)
 PARTNER / CUSTOMER ADMIN     sees: bulk data, integrations
        ▼
 INSIDER / DEVELOPER          sees: prompts, training data, weights, keys
```

The **same app has a different attack surface for each attacker type.** Always ask: "attack surface *from whose position*?"

### D2. Direct vs indirect side-by-side

```
 DIRECT                                   INDIRECT
 Attacker talks to the AI                 Attacker poisons what the AI reads
 Attacker = the user                      Attacker ≠ the victim user
 Visible in chat logs                     Hidden in documents/web/email
 Victim: usually the attacker or app      Victim: other users, the org
 Easier to detect and rate-limit          Harder to trace to a source
```

---

## Part E: Attack surface grows with capability

This is the most important teaching diagram. Each added feature adds surfaces.

```
 LEVEL 1: Simple chatbot                     Surfaces: ① ③ ④ ⑨
 ┌──────┐   ┌─────┐
 │ User │──►│ LLM │                          Worst case: bad text, leaked prompt
 └──────┘   └─────┘

 LEVEL 2: + RAG (knowledge base)             Surfaces: + ② ⑧
 ┌──────┐   ┌─────┐   ┌───────────┐
 │ User │──►│ LLM │◄──│ Vector DB │          Worst case: data leakage across users,
 └──────┘   └─────┘   └───────────┘          indirect injection

 LEVEL 3: + Tools                            Surfaces: + ⑦ ⑩
 ┌──────┐   ┌─────┐   ┌───────────┐
 │ User │──►│ LLM │◄──│ Vector DB │          Worst case: unauthorized actions,
 └──────┘   └──┬──┘   └───────────┘          data exfiltration
               ▼
          ┌─────────┐
          │  Tools  │
          └─────────┘

 LEVEL 4: Autonomous agents / multi-agent    Surfaces: + agent-to-agent,
 ┌─────┐◄──►┌─────┐◄──►┌─────┐               memory, long-running tasks
 │Agent│    │Agent│    │Agent│               Worst case: chain reactions, silent
 └──┬──┘    └──┬──┘    └──┬──┘               compromise, persistent access
    └──── tools, memory, internet ────┘
```

```
   Capability  ─────────────────────────────────►  increases
   Attack surface ──────────────────────────────►  increases
   Blast radius (damage if it goes wrong) ──────►  increases
```

---

## Part F: Worked example, a customer-support AI

**System:** a company chatbot that answers questions from internal docs and can look up orders and issue refunds.

```
  Customer ──► [Web chat] ──► [Backend] ──► [LLM]
                                │   ▲          │
                  ┌─────────────┘   │          ├──► [Order lookup API]
                  ▼                 │          └──► [Refund API]
            [Vector DB: help docs,  │
             past tickets] ─────────┘
                  ▲
                  │ staff + customers write tickets
              [Ticket system]
```

**Surface inventory:**

| # | Surface in this app | Possible issue (what a tester checks) |
|---|---|---|
| ① | Chat box | Can I make it ignore its rules? |
| ② | Tickets written by customers feed the knowledge base | Can a customer plant instructions that later affect *other* customers? |
| ③ | System prompt | Does it contain secrets or refund limits that can be extracted? |
| ⑦ | Refund API | Is there a cap, or does the model decide alone? Does it verify the order belongs to *this* customer? |
| ⑧ | Vector DB | Do past tickets of other customers show up in retrieval? |
| ⑩ | Chat renders markdown | Can output include a link or image that leaks data? |
| ⑨ | API keys | Are order/refund API keys scoped, or full admin? |
| ⑫ | Chats logged | Who can read logs containing customer data? |

**One attack path through it:**

```
 Attacker submits a support ticket containing hidden instructions
        │
        ▼
 Ticket is indexed into Vector DB
        │
        ▼
 Another customer asks a normal question → ticket is retrieved
        │
        ▼
 LLM reads hidden instructions as if trusted
        │
        ▼
 LLM calls Refund API with attacker-chosen details
        │
        ▼
 IMPACT: financial loss, caused by a surface the attacker never "chatted" with
```

This path crosses surfaces **② → ⑧ → ③ (context) → ⑦**. Real attacks chain surfaces, which is why we map them all.

---

## Part G: How an offensive tester enumerates the surface (method)

```
 ┌─────────────┐   ┌─────────────┐   ┌─────────────┐   ┌─────────────┐   ┌─────────────┐
 │ 1. DRAW THE │──►│ 2. LIST ALL │──►│ 3. MARK     │──►│ 4. RANK BY  │──►│ 5. TEST THE │
 │ ARCHITECTURE│   │ INPUTS AND  │   │ WHO CONTROLS│   │ IMPACT      │   │ TOP PATHS   │
 │ (data flow) │   │ OUTPUTS     │   │ EACH INPUT  │   │ (tools, data│   │             │
 │             │   │             │   │             │   │  reachable) │   │             │
 └─────────────┘   └─────────────┘   └─────────────┘   └─────────────┘   └─────────────┘
```

**Recon questions to ask the team (or discover yourself):**

1. What **inputs** does the model receive, and from whom? (users, docs, web, tools)
2. Which of those can an **outsider** write to?
3. What is in the **system prompt**? Any secrets?
4. What **tools** can the model call, and with whose permissions?
5. What **data** can retrieval reach? Is access checked per user?
6. Where does the **output** go? (browser, database, another system)
7. Which **models, libraries, and datasets** were downloaded from outside?
8. What is **logged**, and does feedback re-enter training?

**Printable checklist:**

```
 □ Inputs mapped (direct)         □ Tools + permissions listed
 □ Inputs mapped (indirect)       □ Vector DB access control checked
 □ System prompt reviewed         □ Output consumers identified
 □ Model source + format known    □ Supply chain inventory done
 □ Infra: keys, endpoints, auth   □ Logging + feedback loops understood
```

---

## Part H: Attack surface reduction (the defender's view)

```
 BEFORE                                   AFTER
 [LLM] ──► admin-level tools              [LLM] ──► minimal, scoped tools
 [Vector DB] ──► no user checks           [Vector DB] ──► per-user filtering
 [Output] ──► rendered raw                [Output] ──► sanitized, no auto-links
 [Model] ──► from unknown upload          [Model] ──► verified, safe format
 [Prompt] ──► contains secrets            [Prompt] ──► no secrets, secrets in vault
```

| Principle | Meaning for AI |
|---|---|
| Least privilege | Give the agent only the tools and scopes it needs |
| Remove unused doors | Disable plugins, features, and endpoints nobody uses |
| Segregate untrusted content | Mark and limit what retrieved/external text can influence |
| Human approval for high-impact actions | Refunds, deletes, sends need confirmation |
| Validate outputs | Never pass raw model output to a shell, DB, or browser |
| Verify the supply chain | Pin versions, scan models, trusted sources only |

---

## Five-sentence recap

1. An attack surface is every point where an attacker can interact with or influence the system; a vector is the method; a path is the full chain.
2. AI adds new surfaces on top of classic ones: prompts, indirect content, models, training data, retrieval, and tools.
3. **Indirect input (②)** is the most underrated surface, because the victim does nothing wrong.
4. **Tools and agents (⑦)** decide the blast radius; more capability means more risk.
5. Offensive testing starts by drawing the architecture, listing inputs, marking who controls them, and ranking by impact.

## Self-check questions
1. What is the difference between an attack surface, a vector, and a path?
2. Why is indirect input harder to defend against than direct input?
3. Why is a RAG chatbot riskier than a plain chatbot, and an agent riskier still?
4. Why should the system prompt never contain secrets?
5. In the support-bot example, which surfaces did the attack path cross?

## Practice exercise for your learners
Pick any AI product (a coding assistant, a resume screener, an email summarizer). Draw its architecture, number the surfaces using the ①–⑫ map, and write **one attack path** from an outsider to an impact.

---
