# Topic 1 Offensive Security and AI/ML Security Fundamentals

---

## Part A: What is "offensive" security?

**Offensive security** means attacking your own systems (with permission) before a real attacker does, to find weaknesses and prove their impact.

### A1. The three teams, visualized

```
                  YOUR ORGANIZATION (the target)
        ┌──────────────────────────────────────────────┐
        │   Internet → Firewall → App → Database       │
        │                                              │
        │   🛡 BLUE TEAM watches logs, alerts,         │
        │      blocks attacks, responds                │
        └──────────────────────────────────────────────┘
              ▲                                ▲
              │ attacks                        │ defends
              │                                │
     ┌────────┴────────┐              ┌────────┴────────┐
     │  🔴 RED TEAM    │              │  🔵 BLUE TEAM   │
     │  Acts like a    │              │  Detects and    │
     │  real attacker  │              │  responds       │
     └────────┬────────┘              └────────┬────────┘
              │                                │
              └───────────┬────────────────────┘
                          ▼
                ┌───────────────────┐
                │  🟣 PURPLE TEAM   │
                │  Shares findings: │
                │  "We got in via X │
                │   did you see it?"│
                └───────────────────┘
                          │
                          ▼
              Better detection + stronger defense
```

| Team | Role | Mindset |
|---|---|---|
| **Red** | Simulates a real attacker | "How can I break in and what can I reach?" |
| **Blue** | Detects, defends, responds | "How do I stop and spot this?" |
| **Purple** | Red and blue working together | "Let's attack, then improve detection together" |

### A2. The three offensive activities (wide → deep)

```
 BREADTH (how much is covered)
   ▲
   │  ┌──────────────────────────────────────────────┐
   │  │ 1. VULNERABILITY ASSESSMENT                  │
   │  │    Scan everything, list weaknesses.         │
   │  │    Automated. "Here are 200 possible issues" │
   │  └──────────────────────────────────────────────┘
   │        ┌──────────────────────────────────┐
   │        │ 2. PENETRATION TESTING           │
   │        │    Exploit selected weaknesses   │
   │        │    to prove impact. Scoped and   │
   │        │    time-boxed.                   │
   │        │    "I got admin on this server"  │
   │        └──────────────────────────────────┘
   │              ┌────────────────────────┐
   │              │ 3. RED TEAMING         │
   │              │    Goal-based, stealthy│
   │              │    "Steal the customer │
   │              │     database without   │
   │              │     being caught"      │
   │              └────────────────────────┘
   └────────────────────────────────────────────────► DEPTH / REALISM
```

| Activity | Question it answers | Example |
|---|---|---|
| Vulnerability assessment | What weaknesses exist? | Scanner finds an outdated library |
| Penetration test | Can the weakness actually be exploited? | Use that library flaw to get a shell |
| Red team | Can a realistic attacker reach our crown jewels without being stopped? | Phish an employee → pivot → reach the customer DB |

### A3. The key rule: authorization

```
 ┌───────────────┐   ┌───────────────┐   ┌──────────────────┐
 │ 1. SCOPE      │──►│ 2. RULES OF   │──►│ 3. WRITTEN       │
 │ What can I    │   │ ENGAGEMENT    │   │ PERMISSION       │
 │ test? What    │   │ When? How     │   │ Signed by owner  │
 │ is off-limits?│   │ hard? Who to  │   │ ("get-out-of-    │
 │               │   │ call if I     │   │  jail" letter)   │
 │               │   │ break it?     │   │                  │
 └───────────────┘   └───────────────┘   └────────┬─────────┘
                                                  │
                                                  ▼
 ┌───────────────┐   ┌───────────────┐   ┌──────────────────┐
 │ 6. RETEST     │◄──│ 5. REPORT     │◄──│ 4. TEST          │
 │ Confirm fixes │   │ Findings,     │   │ Attack within    │
 │ work          │   │ impact, and   │   │ scope only       │
 │               │   │ how to fix    │   │                  │
 └───────────────┘   └───────────────┘   └──────────────────┘

  No steps 1 to 3  =  it's a crime, not a test.
```

### A4. Offensive security applied to AI (preview)

```
   AI RED TEAMER                         TARGET AI APPLICATION
  ┌──────────────┐                 ┌─────────────────────────────────┐
  │ Crafts       │   prompts,      │  Chat UI → Backend → LLM        │
  │ malicious    │   documents,    │               │         │       │
  │ inputs       │ ──────────────► │               ▼         ▼       │
  │              │                 │          Vector DB    Tools     │
  └──────────────┘                 └─────────────────────────────────┘
         ▲                                          │
         │         observes behavior,               │
         └──── leaked data, unsafe actions ◄────────┘
                           │
                           ▼
                 Report to blue team → add guardrails,
                 filters, permissions → retest
```

**Why it matters for AI:** AI systems are new, poorly understood, and widely deployed. Defenders can't protect what they haven't tried to break.

---

## Part B: The CIA Triad, applied to AI

```
                    CONFIDENTIALITY
                  "Only the right people
                    see the data"
                         /\
                        /  \
                       /    \
                      /  AI  \
                     / SYSTEM \
                    /__________\
          INTEGRITY              AVAILABILITY
   "Data and behavior        "System works when
    can't be tampered"            needed"
```

**Where each one breaks in an AI app:**

```
 Attacker ──► [ Chat UI ] ──► [ Backend ] ──► [ LLM ] ──► [ Vector DB / Data ]

 C ✗  Attacker asks LLM ───────────────────────┐
      "Repeat your system prompt"              ▼
      → system prompt / customer data LEAKS  (Confidentiality)

 I ✗  Attacker poisons training data or docs ──► [ Data ]
      → model gives wrong or malicious answers  (Integrity)

 A ✗  Attacker floods expensive requests ──► [ LLM ]
      → outage + huge cloud bill                (Availability)
```

**Vocabulary diagram (how the terms connect):**

```
 THREAT ─────exploits────► VULNERABILITY ─────exposes────► ASSET
 (attacker, event)         (weak auth, no input         (model, data,
                            filtering)                   prompts, keys)
       │                          │                           │
       └────── together create ───┴──────► RISK = likelihood × impact

 ATTACK SURFACE = every door the threat can knock on
 EXPLOIT        = the technique used to walk through the door
```

---

## Part C: AI and ML basics (just enough to secure it)

### C1. The terminology ladder

```
┌───────────────────────────────────────────────────┐
│ Artificial Intelligence (AI)                      │
│  machines doing "intelligent" tasks               │
│  ┌─────────────────────────────────────────────┐  │
│  │ Machine Learning (ML)                       │  │
│  │  learns patterns from data                  │  │
│  │  ┌───────────────────────────────────────┐  │  │
│  │  │ Deep Learning (DL)                    │  │  │
│  │  │  multi-layer neural networks          │  │  │
│  │  │  ┌─────────────────────────────────┐  │  │  │
│  │  │  │ Generative AI / LLMs            │  │  │  │
│  │  │  │  generate text, images, code    │  │  │  │
│  │  │  └─────────────────────────────────┘  │  │  │
│  │  └───────────────────────────────────────┘  │  │
│  └─────────────────────────────────────────────┘  │
└───────────────────────────────────────────────────┘
```

### C2. Traditional software vs ML (the big idea)

```
 TRADITIONAL SOFTWARE
 ┌──────────────┐
 │ Rules (code) │──┐
 └──────────────┘  ├──► [ Program ] ──► Answers
 ┌──────────────┐  │
 │ Data         │──┘
 └──────────────┘

 MACHINE LEARNING
 ┌──────────────┐
 │ Data         │──┐
 └──────────────┘  ├──► [ Training ] ──► MODEL (the learned "rules")
 ┌──────────────┐  │
 │ Answers      │──┘
 └──────────────┘

 ⚠ Security takeaway: DATA BECOMES CODE.
   Control the data → control the behavior.
```

### C3. Training vs inference

```
 TRAINING (done once, expensive)            INFERENCE (done constantly)

 [ Huge dataset ]                           [ User input ]
        │                                          │
        ▼                                          ▼
 [ Training process ] ──► [ MODEL FILE ] ──► [ Model ] ──► [ Output ]
   (GPUs, code,            (weights =         (deployed
    libraries)              numbers)           behind an API)

 Attacks here:                              Attacks here:
 poisoning, backdoors,                      prompt injection, adversarial
 compromised code                           inputs, extraction, leakage
```

**Quick vocabulary:** *model/weights* (the learned numbers), *features/labels* (inputs and correct answers), *prompt* (instruction text for an LLM), *embedding* (numbers representing meaning, used in RAG), *fine-tuning* (extra training on specific data).

---

## Part D: The ML lifecycle and where attacks happen

```
┌──────────┐   ┌───────────┐   ┌──────────┐   ┌───────────┐   ┌───────────┐   ┌──────────┐
│ 1. DATA  │──►│ 2. DATA   │──►│ 3. MODEL │──►│ 4. MODEL  │──►│ 5. DEPLOY │──►│ 6. USE   │
│ COLLECT  │   │ PREP/LABEL│   │ TRAINING │   │ STORAGE   │   │ (API/App) │   │ INFERENCE│
└────┬─────┘   └─────┬─────┘   └────┬─────┘   └─────┬─────┘   └─────┬─────┘   └────┬─────┘
     │               │              │               │               │              │
 Data poisoning  Label flipping  Backdoor in    Model theft    Insecure API,   Prompt injection
 Scraped bad     Biased/dirty    training code  Tampered file  leaked keys,    Adversarial inputs
 sources         data            Malicious      Malicious      misconfigured   Model extraction
                                 libraries      pickle files   cloud           Data leakage
                                                                                    │
                                                                                    ▼
                                                                         7. MONITOR / FEEDBACK
                                                                         (feedback loops can be poisoned)
                                                                                    │
                                                                                    └──► back to Stage 1
```

**Teaching point:** classic security protects code and infrastructure. AI security must also protect **data, the model, and its behavior**, across the whole lifecycle.

---

## Part E: A modern AI application and what happens in one request

```
                                      ┌───────────────────────┐
                                      │ System prompt         │
                                      │ (developer's hidden   │
                                      │  rules)               │
                                      └──────────┬────────────┘
                                                 │ ②
 ┌──────┐ ① ┌──────────┐    ┌──────────────────┐ ▼      ┌─────────┐
 │ USER │──►│ Chat UI  │───►│ Backend /        │───────►│  LLM    │
 └──────┘   └──────────┘    │ Orchestrator     │◄───────│ (model) │
    ▲                       └───┬──────────▲───┘   ③    └─────────┘
    │ ⑥                         │ ②        │ ④ docs          │
    │ final answer              ▼          │                 │ ⑤ "call a tool"
    └───────────────────  ┌───────────┐    │                 ▼
                          │ Vector DB │────┘          ┌──────────────┐
                          │ (RAG docs)│               │ Tools/APIs   │
                          └───────────┘               │ email, DB,   │
                                                      │ files, web   │
                                                      └──────────────┘
```

1. User types a prompt.
2. Backend adds the system prompt and fetches relevant documents (RAG).
3. Everything is combined and sent to the LLM.
4. The LLM reads it all as one block of text.
5. It may decide to call a tool.
6. The result goes back to the user.

**What the LLM actually sees (the root problem):**

```
┌──────────────────────────────────────────────────────────┐
│ ONE BIG TEXT BLOCK                                       │
│  [Developer rules]  ← trusted instruction                │
│  [Retrieved doc]    ← untrusted data (may hide commands!)│
│  [User message]     ← untrusted input                    │
│                                                          │
│  The model can't reliably tell instruction from data.    │
└──────────────────────────────────────────────────────────┘
```

---

## Part F: Why AI security differs from traditional security

```
 TRADITIONAL APP                         AI APP

 Input ──► [ Code (fixed rules) ]        Input ──► [ Model (learned, opaque) ]
            │                                        │
            ▼                                        ▼
       Predictable output                      Variable output
                                                                          
 Code ≠ Data (clearly separated)         Instruction + Data = same text
 Fix: patch the code                     Fix: retrain, filter, add guardrails
 Input: forms, fields                    Input: language, images, audio
 Attacker needs a technical exploit      Attacker may need only clever wording
```

**Defense in depth is the only real answer for AI:**

```
 User input ─► [Input filter] ─► [Model + safe system prompt] ─► [Output filter]
                                          │
                                          ▼
                               [Least-privilege tools] ─► [Logging/monitoring]
```

No single layer is perfect, so you stack them.

---

## Part G: Four meanings of "AI security"

```
                        AI is the...
              TARGET                         WEAPON / TOOL
         ┌──────────────────┐        ┌──────────────────────────┐
 ATTACK  │ 1. SECURITY OF AI│        │ 2. SECURITY FROM AI      │
 /HARM   │ Protect models,  │        │ Deepfakes, AI phishing,  │
         │ data, prompts    │        │ AI-written malware       │
         │ ⭐ OUR FOCUS     │        │                          │
         ├──────────────────┤        ├──────────────────────────┤
 DEFENSE │ 4. SAFETY OF AI  │        │ 3. SECURITY WITH AI      │
 /CARE   │ Prevent harmful  │        │ SOC automation, anomaly  │
         │ outputs, even    │        │ detection, threat hunting│
         │ without attacker │        │                          │
         └──────────────────┘        └──────────────────────────┘
```

**Security vs Safety:**

```
 SECURITY:  [Adversary] ──attacks──► [AI] ──► misbehaves
 SAFETY:    [AI] ──on its own──► harmful/biased output (no attacker needed)
```

---

## Part H: The offensive mindset for AI (attack chain)

```
 ┌───────────┐   ┌───────────┐   ┌───────────┐   ┌───────────┐
 │ 1. CONTROL│──►│ 2.INFLUENCE│──►│ 3. ABUSE  │──►│ 4. IMPACT │
 │   INPUT   │   │   THE MODEL│   │CAPABILITIES│  │           │
 │ prompt,   │   │ injection, │   │ tools,     │  │ data leak,│
 │ document, │   │ poisoning, │   │ permissions│  │ unauthor- │
 │ file, data│   │ jailbreak  │   │ data access│  │ ized action│
 └───────────┘   └───────────┘   └───────────┘   └───────────┘
```

**The four questions to ask of any AI system:**

```
 1. What can I CONTROL?      → inputs, documents, data sources
 2. What does it TRUST?      → system prompt, retrieved data, tool outputs
 3. What can it DO?          → tools, permissions, data access
 4. What is the WORST case?  → leak, unauthorized action, reputation damage
```

---

## Five-sentence recap

1. Offensive security is authorized attacking to find weaknesses first.
2. Security goals are Confidentiality, Integrity, Availability, and AI can fail at all three.
3. In ML, **data becomes code**, so poisoning data means controlling behavior.
4. AI has a lifecycle (data → train → store → deploy → use → monitor), and each stage has its own attacks.
5. LLMs treat instructions and data as the same text, which is why AI needs its own security discipline.

## Self-check questions
1. Why is "data becomes code" a security problem?
2. Name one attack each at training, supply chain, and inference.
3. Why can't prompt injection be patched like SQL injection?
4. What's the difference between a vulnerability assessment, a pentest, and a red team?
5. Which of the four meanings of "AI security" is our focus?

---

I can export this as a Word or Markdown file for your teaching material. Ready for **Topic 2: AI Application Architecture** whenever you are. I'll go deep on each component (LLM, RAG, agents, tools, APIs) with the same diagram-first style.
