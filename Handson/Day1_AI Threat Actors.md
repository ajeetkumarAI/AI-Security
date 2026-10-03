# Topic: AI Threat Actors

A threat actor is the **who** behind an attack. The earlier topics covered where attacks can happen (the surface). This one covers who would attack, why, and how capable they are. This is what lets you rank which attack paths actually matter.

---

## Part A: What is a threat actor?

A **threat actor** is any person, group, or system that can cause harm to your AI system, deliberately or accidentally.

```
 WHO ──► WHY ──► HOW CAPABLE ──► HOW CLOSE ──► WHAT THEY HIT
 actor   motive   skill +         access        surface +
                  resources       position      asset

 A threat only becomes REAL RISK when all five line up.
```

| Question | Term | Example |
|---|---|---|
| Who are they? | Actor | Competitor, insider, criminal gang |
| Why attack? | Motivation | Money, secrets, disruption |
| What can they do? | Capability | Script kiddie vs state lab |
| Where do they stand? | Access | Anonymous user vs employee |
| What do they go for? | Target | Model weights, customer data, GPU credits |

**Threat vs threat actor:** "Prompt injection" is a *threat* (the what). "A fraudster planting hidden instructions in support tickets" is a *threat actor using that threat*. Good threat models name both.

---

## Part B: The four-trait actor profile

Describe every actor using four traits.

```
 ┌──────────────┐ ┌──────────────┐ ┌──────────────┐ ┌──────────────┐
 │ 1. MOTIVE    │ │ 2. CAPABILITY│ │ 3. ACCESS    │ │ 4. PERSISTENCE│
 │ money,       │ │ skill, tools,│ │ anonymous,   │ │ one-off try   │
 │ espionage,   │ │ budget, GPUs,│ │ customer,    │ │ vs months of  │
 │ ideology,    │ │ time, team   │ │ partner,     │ │ patient       │
 │ fame, revenge│ │              │ │ insider      │ │ campaign      │
 └──────────────┘ └──────────────┘ └──────────────┘ └──────────────┘
```

A simple likelihood estimate: **Motive × Capability × Access**. If any one is near zero, that actor is not your main worry for that surface. For example, a state lab (high capability) with no reason to target a small bakery chatbot (no motive) is low priority.

---

## Part C: The actor map (who sits where)

Actors differ most in **how close they already are to the system**.

```
                      FAR from system ───────────────────► INSIDE the system

   ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐
   │ ANONYMOUS    │  │ REGISTERED   │  │ PARTNER /    │  │ INSIDER /    │
   │ INTERNET     │  │ USER OR      │  │ VENDOR /     │  │ PRIVILEGED   │
   │              │  │ CUSTOMER     │  │ SUPPLIER     │  │ STAFF        │
   │ jailbreakers │  │ fraudsters   │  │ supply chain │  │ malicious or │
   │ criminals    │  │ competitors  │  │ attackers    │  │ careless     │
   │ hacktivists  │  │ (as users)   │  │ compromised  │  │ employees    │
   │ state actors │  │              │  │ vendors      │  │ contractors  │
   └──────────────┘  └──────────────┘  └──────────────┘  └──────────────┘
      Least access ──────────────────────────────────────► Most access
```

Any actor can also **climb**. An anonymous attacker who steals one employee's credentials becomes, effectively, an insider.

---

## Part D: The actor catalog

### 1. Curious users and hobbyist jailbreakers

```
 [Individual] ──prompts──► [Chat UI] ──► LLM
   Goal: bypass the rules for fun, fame, or to see what happens
```

- **Motive:** curiosity, bragging rights, social media
- **Capability:** low to medium; shares tricks in communities
- **Access:** anonymous or normal user
- **Targets:** surface ① (user input), ③ (system prompt)
- **Typical impact:** reputational damage (screenshots of the bot misbehaving), leaked system prompt
- **Note:** low skill, but huge numbers. Their tricks spread fast and later get reused by serious actors.

### 2. Cybercriminals

```
 [Criminal group] ──► steal keys / abuse access ──► sell, resell, or cash out
```

- **Motive:** money
- **Capability:** medium to high, organized, and they reuse proven techniques
- **Access:** anonymous, moving up via stolen credentials
- **Targets:** ⑨ (infrastructure, leaked API keys), ⑦ (tools that move money), ① (fraud via the bot)
- **Typical goals:**
  - Stealing cloud or API credentials to run AI workloads on your bill (often called **LLMjacking**)
  - Tricking a support or finance bot into refunds or transfers
  - Stealing customer data from RAG stores
  - Reselling access to compromised AI accounts
- **Note:** they pick the **cheapest path to cash**. Often that is a leaked key, not a clever prompt.

### 3. Competitors and IP thieves

```
 [Competitor] ──thousands of queries──► [Your model API]
        └──► train a copy ("model extraction / distillation")
```

- **Motive:** competitive advantage, avoiding R&D cost
- **Capability:** medium to high (they have ML skill and compute)
- **Access:** often a paying customer, which looks like normal usage
- **Targets:** ④ (the model), ⑤ (proprietary training data), ③ (prompt engineering secrets)
- **Typical impact:** loss of intellectual property, lost market edge
- **Note:** hard to detect because each individual request looks legitimate.

### 4. Nation-state and advanced persistent threat (APT) actors

```
 [State-backed team] ──patient, multi-stage──► supply chain → infra → data
```

- **Motive:** espionage, strategic advantage, disruption of critical services
- **Capability:** very high (funding, custom tooling, zero-day research, long timelines)
- **Access:** can gain any level, including supply chain and insiders
- **Targets:** ⑥ (supply chain), ④ (frontier model weights), ⑤ (data), ⑨ (infrastructure)
- **Typical goals:** steal advanced model weights, quietly monitor sensitive AI-assisted workflows, plant long-term access
- **Note:** they are **patient and stealthy**. They also use AI themselves for faster recon, phishing, and scaling operations.

### 5. Hacktivists and ideological actors

```
 [Activist group] ──► deface, embarrass, disrupt, expose
```

- **Motive:** ideology, protest, publicity
- **Capability:** low to medium
- **Access:** anonymous
- **Targets:** ① (making the bot say offensive things), ⑩ (public outputs), availability (flooding)
- **Typical impact:** reputational harm, outages, leaked internal material

### 6. Malicious insiders

```
 [Employee / contractor with legitimate access]
      ├──► copy model weights or datasets
      ├──► read other users' logs and chats
      └──► alter prompts, data, or guardrails
```

- **Motive:** money, grievance, revenge, recruited by a competitor or state
- **Capability:** varies, but **access is already granted**
- **Access:** highest (prompts, data, weights, keys)
- **Targets:** ④ weights, ⑤ data, ③ prompts, ⑫ logs
- **Note:** they bypass most external defenses. Controls here are **least privilege, access logging, and separation of duties**.

### 7. Negligent insiders (accidental actors)

```
 [Well-meaning employee] ──pastes confidential data──► [public AI tool]
 [Developer]             ──commits API key───────────► [public repo]
 [Team]                  ──connects agent to everything with admin rights
```

- **Motive:** none (no intent to harm)
- **Capability:** not relevant
- **Access:** legitimate
- **Targets:** ⑫ (data flows into logs or third parties), ⑨ (leaked secrets), ⑦ (excessive permissions)
- **Note:** statistically one of the **most common causes of real incidents**. Include them in every threat model even though they aren't "attackers."

### 8. Supply chain attackers

```
 [Attacker] ──uploads──► [Model hub / package registry / plugin store]
                              │
                       Victim downloads and trusts it
                              ▼
                       Code or backdoor runs inside the victim's boundary
```

- **Motive:** money, espionage, mass compromise
- **Capability:** medium to high
- **Access:** none to the victim directly; they exploit **trust in third parties**
- **Targets:** ⑥ (models, libraries, datasets, plugins, MCP servers)
- **Typical methods:** typosquatted package names, trojaned model files, malicious plugins, compromised maintainer accounts
- **Note:** one successful upload can reach many victims.

### 9. Content planters (indirect injection actors)

```
 [Attacker] ──plants hidden instructions──► web page / email / PDF / ticket / review
                                                  │
                          AI reads it later while serving a different user
```

- **Motive:** data theft, fraud, manipulating AI-driven decisions
- **Capability:** low to medium (no system access required)
- **Access:** only the ability to **write somewhere the AI will read**
- **Targets:** ② (indirect input), ⑧ (vector DB), ⑦ (tools)
- **Examples of goals:** make a resume screener rank a candidate higher, make an email agent leak data, make a shopping assistant favor one seller (a form of "AI SEO" manipulation)
- **Note:** a cheap and growing actor type because the barrier is just publishing content.

### 10. Data poisoners

```
 [Poisoner] ──injects crafted samples──► scraped web data / public datasets /
                                          feedback channels / open contributions
```

- **Motive:** sabotage, planting a backdoor, bias, or (in some cases) protest against data scraping
- **Capability:** medium
- **Access:** anywhere data enters the pipeline
- **Targets:** ⑤ (training data), ⑫ (feedback loops), ⑧ (knowledge base)

### 11. Scammers using AI against people ("security from AI")

```
 [Scammer] + [AI tools] ──► deepfake voice, tailored phishing, fake support bots
```

- **Motive:** money
- **Capability:** low skill needed because AI supplies the polish
- **Targets:** ⑪ (humans)
- **Note:** here AI is the **weapon**, not the target. Include it because it lowers the skill needed for every other actor.

### 12. Compromised or rogue AI agents (non-human actors)

```
 [Agent A, compromised by injection] ──messages──► [Agent B]
        └─► uses its own permissions to act for an attacker
```

- **Motive:** none of its own; it is **steered by whoever controls its input**
- **Capability:** whatever tools and permissions it was given
- **Access:** whatever it was granted
- **Note:** a new category. The agent isn't "evil," but once hijacked it is an actor inside your trust boundary acting with real privileges. This is the confused deputy problem from the earlier topic.

### 13. Ethical actors (shown for completeness)

AI red teamers, security researchers, and bug bounty hunters use the same techniques **with authorization** and report findings. They are the reason you can find problems before real attackers do.

---

## Part E: Quick comparison table

| Actor | Motive | Capability | Typical access | Main surfaces | Detection difficulty |
|---|---|---|---|---|---|
| Hobbyist jailbreaker | Fun, fame | Low-Med | Anonymous | ① ③ | Easy-Med |
| Cybercriminal | Money | Med-High | Anonymous → stolen creds | ⑨ ⑦ ① | Medium |
| Competitor | Advantage | Med-High | Paying customer | ④ ⑤ ③ | Hard |
| Nation-state | Espionage | Very high | Any, via supply chain/insiders | ⑥ ④ ⑤ ⑨ | Very hard |
| Hacktivist | Ideology | Low-Med | Anonymous | ① ⑩ | Easy |
| Malicious insider | Money, grievance | Varies | Privileged | ④ ⑤ ③ ⑫ | Hard |
| Negligent insider | None | N/A | Legitimate | ⑫ ⑨ ⑦ | Medium |
| Supply chain attacker | Money, espionage | Med-High | Via third-party trust | ⑥ | Hard |
| Content planter | Fraud, manipulation | Low-Med | Can write to external content | ② ⑧ ⑦ | Hard |
| Data poisoner | Sabotage, backdoor | Medium | Data entry points | ⑤ ⑫ ⑧ | Very hard |
| Hijacked agent | None (steered) | Tool-dependent | Granted permissions | ⑦ | Hard |

---

## Part F: Actor-to-surface matrix

This ties back to the ①–⑫ surface map. A filled cell means "this actor commonly uses this surface."

```
                          ①  ②  ③  ④  ⑤  ⑥  ⑦  ⑧  ⑨  ⑩  ⑪  ⑫
 Hobbyist jailbreaker     ●      ●
 Cybercriminal            ●              ●     ●   ●
 Competitor                      ●  ●  ●
 Nation-state                       ●  ●  ●  ●   ●
 Hacktivist               ●                          ●
 Malicious insider               ●  ●  ●                    ●       ●
 Negligent insider                                ●   ●           ●  ●
 Supply chain attacker                    ●
 Content planter              ●                ●   ●
 Data poisoner                          ●             ●             ●
```

**How to use it:** for the surface you are reviewing, look down its column to see which actors you should be thinking about. For example, a column with many dots (such as ⑨ infrastructure) deserves extra attention.

---

## Part G: Motivation view (why attacks happen)

```
 ┌─────────┐  ┌───────────┐  ┌───────────┐  ┌──────────┐  ┌──────────┐  ┌─────────┐
 │ MONEY   │  │ ESPIONAGE │  │ DISRUPTION│  │ IDEOLOGY │  │ CURIOSITY│  │ REVENGE │
 │ fraud,  │  │ steal IP, │  │ outages,  │  │ protest, │  │ fame,    │  │ insiders│
 │ resale, │  │ weights,  │  │ cost abuse│  │ shaming  │  │ learning │  │ ex-staff│
 │ ransom  │  │ secrets   │  │           │  │          │  │          │  │         │
 └────┬────┘  └─────┬─────┘  └─────┬─────┘  └────┬─────┘  └────┬─────┘  └────┬────┘
      ▼             ▼              ▼             ▼             ▼             ▼
   refunds,      model weights,  flood the     make bot say   jailbreak     sabotage
   stolen keys,  training data,  API, drain    offensive      posts         data or
   data sales    monitoring      budget        things                       prompts
```

**Why motive matters for defense:**
- Money-driven actors follow the **cheapest path** → close the easy doors first (keys, permissions).
- Espionage-driven actors are **patient** → invest in detection and supply chain controls.
- Fame-driven actors want **visible, shareable results** → watch public-facing behavior.
- Revenge-driven insiders trigger on **life events** (layoffs) → control access during transitions.

---

## Part H: How AI changes the actor landscape

```
 BEFORE AI                          WITH AI
 Skilled attacker needed   ──────►  Less skill needed (AI writes phishing, code, scripts)
 Slow, manual recon        ──────►  Faster, automated recon at scale
 Generic scams             ──────►  Personalized scams in any language
 Attacker needs system     ──────►  Attacker only needs to plant content the AI reads
 access
 Humans are the only       ──────►  AI agents themselves can become actors
 actors inside
```

Three key shifts to teach:
1. **Lower barrier to entry:** more people can do more damage.
2. **New access model:** content planting needs no breach, just publishing.
3. **Non-human actors:** agents with permissions can act on an attacker's behalf.

---

## Part I: Worked example, who would attack the support bot?

**System (from the earlier topic):** a chatbot answering from help docs, looking up orders, and issuing refunds.

```
   Customer ──► [Chat] ──► [Backend] ──► [LLM] ──► [Order API]
                              │                └──► [Refund API]
                         [Vector DB: help docs + past tickets]
```

| Actor | Why they'd care | What they'd try | Surfaces |
|---|---|---|---|
| Curious user | Fun | Make the bot ignore rules, leak the prompt | ① ③ |
| Refund fraudster | Money | Talk the bot into refunds for orders that aren't theirs | ① ⑦ |
| Content planter (a customer) | Money | Submit a ticket with hidden instructions that affect later chats | ② ⑧ ⑦ |
| Competitor | Advantage | Query heavily to copy the bot's behavior or probe pricing logic | ④ ③ |
| Cybercriminal | Money | Find leaked API keys in code or logs, run their own workloads | ⑨ |
| Malicious insider | Grievance | Export customer chat logs | ⑫ |
| Negligent insider | None | Pastes real customer data into a public AI tool for convenience | ⑫ |
| Hacktivist | Ideology | Get the bot to produce embarrassing statements | ① ⑩ |

**Prioritizing:**

```
 HIGH priority  ──► Refund fraudster, content planter, cybercriminal
                    (clear motive + real access + real money at stake)
 MEDIUM         ──► Competitor, negligent insider
 LOWER          ──► Hacktivist, curious user (annoying, rarely costly here)
 MONITOR        ──► Nation-state (unlikely target for this product)
```

The ranking changes with the product. A frontier model lab would put nation-states and competitors at the top.

---

## Part J: Building an actor profile card (threat modeling tool)

For each relevant actor, fill in one card.

```
 ┌───────────────────────────────────────────────────────┐
 │ ACTOR:        ______________________                  │
 │ MOTIVE:       money / espionage / disruption / other  │
 │ CAPABILITY:   low / medium / high / very high         │
 │ ACCESS:       anonymous / user / partner / insider    │
 │ GOAL:         what they want to achieve               │
 │ FAVORITE SURFACES: ①②③...                             │
 │ LIKELY PATH:  entry → influence → power → impact      │
 │ LIKELIHOOD:   motive × capability × access            │
 │ EXISTING CONTROLS: ____________________________      │
 │ GAPS:         ____________________________            │
 └───────────────────────────────────────────────────────┘
```

**Process:**

```
 1. LIST actors  ─►  2. PROFILE each  ─►  3. MAP to surfaces  ─►  4. BUILD paths
                                                                      │
 6. RETEST  ◄─  5. FIX top-ranked gaps  ◄──── RANK by likelihood × impact ◄┘
```

---

## Part K: Common misunderstandings

| Myth | Reality |
|---|---|
| "Attackers are all genius hackers." | Most use simple, proven tricks against the cheapest weakness. |
| "Only external people attack." | Insiders and negligent staff cause a large share of incidents. |
| "Nobody would target our small AI app." | Criminals are opportunistic; leaked keys and refund abuse don't need you to be famous. |
| "Threat actors must be human." | Hijacked agents can act inside your boundary. |
| "An attacker needs access to my system." | Content planters only need access to **something your AI reads**. |
| "Everyone is equally likely to attack me." | Likelihood depends on your product, data, and money flow. |

---

## Five-sentence recap

1. A threat actor is who might harm the system, described by **motive, capability, access, and persistence**.
2. Actors range from hobbyist jailbreakers and criminals to competitors, insiders, supply chain attackers, content planters, and nation-states.
3. **Access level** is the biggest differentiator, and any actor can climb to higher access by stealing credentials.
4. AI **lowers the skill barrier**, enables attacks through planted content, and introduces **hijackable agents** as a new kind of actor.
5. Rank actors by motive × capability × access for **your** product, then map them to surfaces and attack paths.

## Self-check questions
1. What are the four traits used to profile an actor?
2. Why is a negligent insider included even though they have no malicious intent?
3. Why is a content planter dangerous despite having no access to your system?
4. How does a competitor's model extraction hide inside normal traffic?
5. Why does the actor ranking differ between a small support bot and a frontier model lab?

## Practice exercise
Pick an AI product (resume screener, coding assistant, email agent). List at least five actors, fill in an actor profile card for the top two, map each to surfaces on the ①–⑫ map, and write one attack path per actor.

---

Next is **AI Trust Boundaries**, where we draw the lines between trusted and untrusted components on the architecture and examine which crossings are dangerous. That connects directly to the "untrusted data into powerful tool" arrows from the attack surface topic. I can also bundle all topics so far into one Word or Markdown teaching document whenever you like.
