# Understanding the AI Attack Surface (Deep Dive)

---

## Part A: The four-lens model

Every attack surface can be broken into four questions:

```
 ┌────────────┐   ┌────────────┐   ┌────────────┐   ┌────────────┐
 │ 1. WAYS IN │   │ 2. WAYS OUT│   │ 3. POWERS  │   │ 4. PRIZES  │
 │ (inputs)   │   │ (outputs)  │   │ (capability│   │ (assets)   │
 │            │   │            │   │            │   │            │
 │ prompts,   │   │ responses, │   │ tools, API │   │ data, model│
 │ files,     │   │ rendered   │   │ calls, code│   │ keys,      │
 │ docs, web, │   │ links, API │   │ execution, │   │ money,     │
 │ feedback   │   │ calls, logs│   │ permissions│   │ reputation │
 └─────┬──────┘   └─────┬──────┘   └─────┬──────┘   └─────┬──────┘
       │                │                │                │
       └────────────────┴───────┬────────┴────────────────┘
                                ▼
             Attacker enters through (1), steers (3),
             reaches (4), and often leaves through (2)
```

| Lens | Ask | If the answer is "a lot" |
|---|---|---|
| Ways in | How many places can outsiders put content? | Bigger surface |
| Ways out | Where can data or actions leave? | Easier exfiltration |
| Powers | What can the AI do without a human? | Bigger blast radius |
| Prizes | What valuable things can it reach? | Higher motivation |

A system with **many ways in but no powers and no prizes** is low risk. A system with **one way in, big powers, and big prizes** can still be critical.

---

## Part B: The layered stack view

The 12 surfaces map onto layers, like floors of a building. An attacker can enter at any floor and move up or down.

```
 ┌───────────────────────────────────────────────────────────┐
 │ L7  PEOPLE        users, admins, labelers, insiders       │ phishing, insider
 ├───────────────────────────────────────────────────────────┤
 │ L6  INTERFACE     chat UI, API, voice, plugins' UI        │ prompt injection
 ├───────────────────────────────────────────────────────────┤
 │ L5  ORCHESTRATION prompts, agent logic, RAG pipeline,     │ context manipulation,
 │                   memory, guardrails                      │ guardrail bypass
 ├───────────────────────────────────────────────────────────┤
 │ L4  MODEL         weights, fine-tunes, inference engine   │ extraction, jailbreak,
 │                                                           │ adversarial inputs
 ├───────────────────────────────────────────────────────────┤
 │ L3  DATA          training sets, vector DB, logs, feedback│ poisoning, leakage
 ├───────────────────────────────────────────────────────────┤
 │ L2  INFRASTRUCTURE cloud, GPUs, containers, secrets, net  │ misconfig, key theft
 ├───────────────────────────────────────────────────────────┤
 │ L1  SUPPLY CHAIN  models, libraries, datasets, MCP/plugins│ malicious packages,
 │                                                           │ trojaned models
 └───────────────────────────────────────────────────────────┘

   Attackers rarely stay on one floor.
   Example: L1 (bad library) → L2 (steal cloud key) → L3 (read data)
```

**Teaching point:** you can compromise an AI system **without ever touching the model**. Lower layers (supply chain, infrastructure) are often the easiest.

---

## Part C: How to read an architecture diagram like an attacker

**Rule: every arrow is a potential attack surface, and every box is a potential target.**

Take a plain diagram:

```
 [User] ──a──► [Backend] ──b──► [LLM] ──c──► [Email Tool]
                  ▲  │                         
                  │  └──d──► [Vector DB]
                  e
              [Web fetch]
```

Now annotate each arrow with three questions: *Who controls the data on this arrow? Is it trusted? What happens if it is malicious?*

| Arrow | Carries | Controlled by | Trust level | If malicious |
|---|---|---|---|---|
| a | User prompt | Any user | Untrusted | Direct injection |
| b | Prompt + context to model | Backend | Mixed (contains untrusted parts) | Poisoned context |
| c | Tool call request | The model (influenced by attacker) | Should be untrusted | Unauthorized email |
| d | Retrieved documents | Whoever wrote the docs | Often wrongly trusted | Indirect injection |
| e | Web page content | Anyone on the internet | Untrusted | Indirect injection |

```
 THE KEY TRICK:
 Find arrows where UNTRUSTED data flows INTO something powerful.
 Those are your highest-priority attack paths.

 untrusted source ═══► [LLM] ═══► powerful tool
   (arrows a, d, e)                  (arrow c)
```

This idea leads directly into the next topic, **trust boundaries**: the lines between "I trust this" and "I don't."

---

## Part D: Attack surface by type of AI system

Different products have very different surfaces.

```
 CHATBOT        RAG APP         CODING ASSISTANT     AI AGENT         MULTI-AGENT
 ┌──┐           ┌──┐            ┌──┐                 ┌──┐             ┌──┐ ┌──┐
 │U │           │U │            │Dev│                │U │             │A1│↔│A2│
 └┬─┘           └┬─┘            └┬─┘                 └┬─┘             └┬─┘ └┬─┘
  ▼              ▼               ▼                    ▼                ▼    ▼
 LLM         LLM+VectorDB     LLM + repo +          LLM + many        tools, memory,
                              terminal + web        tools + memory     each other
```

| System | Main ways in | Main powers | Typical worst case |
|---|---|---|---|
| **Plain chatbot** | User prompt | None (text only) | Leaked prompt, harmful text |
| **RAG app** | Prompt + documents | Reads company data | Cross-user data leak, indirect injection |
| **Coding assistant** | Prompt + repo files + web docs + dependencies | Edits code, runs commands | Malicious code written or run, secret theft |
| **Email/calendar agent** | Incoming emails, invites | Reads and sends mail, edits calendar | Silent data exfiltration |
| **Browser agent** | Every web page it visits | Clicks, logs in, buys | Account takeover, unauthorized purchases |
| **Multi-agent system** | Other agents' messages | Combined powers of all agents | Injection spreading agent to agent |
| **Model-serving (API product)** | Anyone's API calls | Compute, the model | Model theft, cost abuse |

**Pattern:** the more an AI **reads untrusted content** and the more it **can act**, the more dangerous it becomes.

```
            HIGH
 powers  │          ● Browser agent
 (what   │      ● Email agent    ● Multi-agent
 it can  │   ● Coding assistant
 do)     │ ● RAG app
         │● Chatbot
            LOW ───────────────────────────► HIGH
              untrusted content it reads
                       
   Top-right corner = the most dangerous combination
```

---

## Part E: Surface across the lifecycle (time dimension)

A surface also changes **over time**. The same component is exposed differently at each phase.

```
              BUILD            TRAIN           DEPLOY           RUN
 DATA         scraping,        poisoning       n/a              feedback loops,
              labeling                                          RAG ingestion
 MODEL        choosing a       backdoors       model file       extraction,
              base model                       theft            jailbreaks
 CODE/LIBS    dependencies     training        container        runtime injection
                               scripts         images
 INFRA        dev laptops,     GPU clusters,   API gateway,     endpoints, cost
              CI/CD            keys            cloud config     abuse
 PEOPLE       developers       labelers        admins           end users
```

Each cell is a place to ask, "What could go wrong here, and who could reach it?" Many teams only defend the **Run** column and forget Build and Train.

---

## Part F: Measuring exposure (how big is the surface, really?)

Not every surface is equal. Score each one on four factors.

```
 EXPOSURE = REACHABILITY × CONTROL × PRIVILEGE × IMPACT

 REACHABILITY  Who can reach this door?
               internet-anonymous (3) > logged-in user (2) > internal only (1)

 CONTROL       How much can the attacker shape the input?
               free text/files (3) > limited fields (2) > fixed values (1)

 PRIVILEGE     What can the AI do after receiving it?
               act with write access (3) > read only (2) > text only (1)

 IMPACT        How bad if it works?
               money, data, safety (3) > annoyance (2) > negligible (1)
```

**Example scoring:**

| Surface | Reach | Control | Privilege | Impact | Score (max 81) |
|---|---|---|---|---|---|
| Public chat box, text only bot | 3 | 3 | 1 | 1 | **9** (low) |
| Support tickets feeding RAG, bot can refund | 3 | 3 | 3 | 3 | **81** (critical) |
| Admin-only prompt editor | 1 | 3 | 2 | 2 | **12** (low-medium) |

This is a teaching simplification, not an industry standard. Its value is forcing you to **rank** surfaces instead of treating all equally. (Proper risk assessment comes in a later topic.)

---

## Part G: Worked example, an email assistant agent

**System:** "AI assistant that reads your inbox, summarizes, and drafts or sends replies."

```
 Anyone on        ┌─────────┐      ┌─────────────────┐     ┌──────────┐
 the internet ───►│  INBOX  │─────►│  AI ASSISTANT   │────►│ Send mail│
 (emails)         └─────────┘      │  (LLM + prompt) │     └──────────┘
                                   │                 │     ┌──────────┐
 You (the user) ─────────────────► │                 │────►│ Read     │
                                   └─────────────────┘     │ contacts │
                                                           │ & files  │
                                                           └──────────┘
```

**Apply the four lenses:**

| Lens | Answer |
|---|---|
| Ways in | Your prompts **and every email anyone sends you** |
| Ways out | Sent emails, links in summaries |
| Powers | Read inbox, read files, send mail |
| Prizes | Private emails, contacts, attachments, ability to impersonate you |

**Attack path (conceptual):**

```
 1. Attacker emails you (no hacking, anyone can email anyone)
        │
        ▼
 2. Email contains hidden text: instructions aimed at the AI
        │
        ▼
 3. You ask: "Summarize my inbox today"
        │
        ▼
 4. Assistant reads the email, treats hidden text as instructions
        │
        ▼
 5. Assistant uses its POWERS (read files + send mail)
        │
        ▼
 6. IMPACT: private data sent to attacker, or messages sent as you
```

**Why this is the perfect teaching case:**
- The attacker never touched the app.
- The user did nothing unusual.
- The danger came from **untrusted input + powerful tools**, exactly the top-right corner of the chart in Part D.

**Defensive mapping (what reduces this surface):**

| Weakness | Reduction |
|---|---|
| Reads all email as trusted | Treat email content as untrusted data, limit its influence |
| Can send mail freely | Require user approval before sending |
| Can read all files | Scope access to only what the task needs |
| No visibility | Log tool calls and alert on unusual ones |

---

## Part H: Common misunderstandings

| Myth | Reality |
|---|---|
| "Attack surface = the chat box." | The chat box is one of twelve. Indirect input, tools, and supply chain often matter more. |
| "If I harden the model, I'm safe." | Attacks often bypass the model entirely (keys, buckets, libraries). |
| "Internal tools are safe." | Internal AI still reads emails, tickets, and documents that outsiders can write to. |
| "A bigger model is more secure." | Capability does not equal robustness. A smarter model can also follow smarter malicious instructions. |
| "No tools means no risk." | Lower risk, not zero: data leakage, misinformation, and output-handling bugs still exist. |
| "We can list the surface once." | Every new plugin, data source, or feature changes it. Re-map regularly. |

---

## Five-sentence recap

1. Use four lenses: **ways in, ways out, powers, prizes**.
2. The stack has layers (supply chain → infrastructure → data → model → orchestration → interface → people), and attackers move between them.
3. In any architecture diagram, **every arrow is a surface**; prioritize arrows where untrusted data flows into something powerful.
4. Risk rises with **untrusted content read × capability to act**.
5. Rank surfaces by reachability, control, privilege, and impact instead of treating them equally.

## Self-check questions
1. What are the four lenses, and which one decides blast radius?
2. Why can an AI system be compromised without touching the model?
3. In the email agent, which lens exposed the "ways in" that the user never thought about?
4. Why is a browser agent riskier than a plain chatbot?
5. Score a surface of your choice with the four-factor method.

## Practice exercise
Pick a system from Part D. Draw its architecture, label every arrow with *who controls it* and *trusted or untrusted*, circle the arrows where untrusted data enters something powerful, then write one attack path.

---

Next up is **AI Trust Boundaries**. It builds directly on Part C, where we draw the lines between trusted and untrusted components and see which crossings are dangerous. Say the word and I'll start. I can also bundle everything so far into one Word or Markdown teaching document if you'd like.
