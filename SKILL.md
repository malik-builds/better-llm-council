---
name: llm-council
description: "Run any question, idea, or decision through a council of AI advisors who independently analyze it, peer-review each other anonymously, and synthesize a final verdict. Based on Karpathy's LLM Council methodology. MANDATORY TRIGGERS: 'council this', 'run the council', 'war room this', 'pressure-test this', 'stress-test this', 'debate this', 'pre-mortem this'. STRONG TRIGGERS (use when combined with a real decision or tradeoff): 'should I X or Y', 'which option', 'what would you do', 'is this the right move', 'validate this', 'get multiple perspectives', 'I can't decide', 'I'm torn between'. Do NOT trigger on simple yes/no questions, factual lookups, or casual 'should I' without a meaningful tradeoff. DO trigger when the user presents a genuine decision with stakes, multiple options, and context that suggests they want it pressure-tested from multiple angles."
---

# LLM Council v2

You ask one AI a question, you get one answer. That answer might be great. It might be wrong. You have no way to tell because you only saw one perspective.

The council fixes this. It runs your question through multiple independent advisors, each thinking from a fundamentally different angle. Then they review each other's work — anonymously. Then a chairman synthesizes everything into a final recommendation that tells you where advisors agree, where they clash, and what you should actually do.

This is adapted from Andrej Karpathy's LLM Council. He dispatches queries to multiple models, has them peer-review each other anonymously, then a chairman produces the final answer. This skill does the same thing inside Claude using sub-agents with distinct thinking lenses instead of different models.

---

## modes

The council runs in two modes depending on stakes. You can specify a mode, or the council will assess and choose for you.

### quick council (default for lower-stakes decisions)
3 advisors. No peer review. Chairman synthesizes directly.
Best for: tactical decisions, time-sensitive calls, questions where you already have 80% confidence.
Trigger explicitly with: `quick council this`

### full council (default for high-stakes decisions)
5 advisors. Full peer review round. Chairman synthesizes with convergence score.
Best for: major pivots, irreversible decisions, questions where being wrong is expensive.
Trigger explicitly with: `full council this` or just `council this`

### pre-mortem mode
A special mode where the council assumes the decision was made and it failed. They work backward to figure out why.
Best for: validating a decision you've already mostly made, stress-testing a plan before execution.
Trigger with: `pre-mortem this` or `pre-mortem [decision]`

In pre-mortem mode, the framed question becomes: *"It is [6/12/18] months from now. [Decision] was made and it failed badly. Work backward — what went wrong, why didn't anyone catch it, and what were the early warning signs?"*

---

## stakes assessment

Before choosing a mode, the council briefly assesses stakes:

- **Trivial**: reversible in <1 day, cost of being wrong is near-zero → skip the council, just answer
- **Low**: reversible in <1 week, low cost of error → quick council
- **Medium**: reversible in 1-3 months, moderate cost → full council
- **High**: hard to reverse, significant cost of error → full council + pre-mortem
- **Critical**: effectively irreversible, major consequences → full council + pre-mortem + flag to user

If stakes are Trivial, just answer directly. Don't waste the person's time.

---

## the advisor roster

The council has 8 advisor archetypes. The full council uses 5; the quick council uses 3. Selection is automatic based on the question domain, or you can request specific advisors by name.

### core five (default for most decisions)

**The Contrarian**
Actively looks for what's wrong, what's missing, what will fail. Assumes there's a fatal flaw and tries to find it. If everything looks solid, digs deeper. Not a pessimist — the friend who saves you from a bad deal by asking the questions you're avoiding.

**The First Principles Thinker**
Ignores the surface-level question and asks "what are we actually trying to solve here?" Strips away assumptions. Rebuilds from the ground up. Sometimes the most valuable output is the First Principles Thinker saying "you're asking the wrong question entirely."

**The Expansionist**
Looks for upside everyone else is missing. What could be bigger? What adjacent opportunity is hiding? What's being undervalued? Doesn't worry about risk (that's the Contrarian's job). Cares about what happens if this works even better than expected.

**The Outsider**
Has zero context about you, your field, or your history. Responds purely to what's on the page. This is the most underrated advisor. Experts develop blind spots. The Outsider catches the curse of knowledge: things obvious to you but confusing to everyone else.

**The Executor**
Only cares about one thing: can this be done, and what's the fastest path? Ignores theory and big-picture strategy. Looks at every idea through the lens of "OK but what do you do Monday morning?" If an idea sounds brilliant but has no clear first step, the Executor will say so.

### domain-specific advisors (auto-selected or manually requested)

**The Technical Architect** *(for engineering and product decisions)*
Thinks in systems, tradeoffs, and second-order effects. Asks: what breaks at scale? What's the maintenance burden in 18 months? Where does this become the bottleneck? Doesn't care about business metrics — only about whether the technical foundation will hold.

**The Customer / End User** *(for product and marketing decisions)*
Thinks only from the perspective of the person who has to live with this decision. Not your ideal customer avatar — a real, confused, busy person who doesn't care about your vision. Asks: what's confusing here? What would make someone close the tab? What's the actual job to be done?

**The Systems Thinker** *(for organizational and complex decisions)*
Looks for feedback loops, unintended consequences, and second-order effects that linear thinking misses. Asks: what does this optimize for, and what does that break elsewhere? Where are the leverage points? What's the delayed effect of this decision in 6 months?

---

## domain auto-selection

The council reads the question and picks advisors accordingly:

| Question type | Advisors selected |
|---|---|
| Business / go-to-market | Contrarian, First Principles, Expansionist, Outsider, Executor |
| Product / UX / feature | Contrarian, Customer, Expansionist, Outsider, Executor |
| Technical / architecture | Contrarian, Technical Architect, First Principles, Outsider, Executor |
| Organizational / hiring | Contrarian, First Principles, Systems Thinker, Outsider, Executor |
| Career / personal | Contrarian, First Principles, Expansionist, Outsider, Executor |

You can override this: `council this with Technical Architect, Contrarian, and Expansionist`

---

## how a session works

### step 1: frame the question (with context enrichment)

When triggered, do two things before framing:

**A. Scan for context.** The user's question is the tip of the iceberg. Before framing, scan for relevant context files:
- `CLAUDE.md` or `claude.md` (business context, preferences, constraints)
- Any `memory/` folder (audience profiles, past decisions, business details)
- Files the user explicitly referenced or attached
- Any prior council transcripts (to avoid retreading the same ground)

Use quick reads. Don't spend more than 30 seconds. You're looking for the 2–3 files that turn generic advice into specific, grounded advice.

**B. Assess stakes.** Before framing, rate the stakes: Trivial / Low / Medium / High / Critical. If Trivial, skip the council and answer directly. If High or Critical, note this and recommend a pre-mortem after the full council.

**C. Frame the question.** Take the raw question + enriched context and reframe it as a clear, neutral prompt all advisors receive. Include:
1. The core decision or question
2. Key context from the user's message
3. Key context from workspace files (stage, audience, constraints, past results, relevant numbers)
4. What's at stake — why this decision matters
5. Stakes level and mode selected

Don't steer it. Don't add your opinion. But DO make sure each advisor has enough context to give specific, grounded advice instead of generic takes.

---

### step 2: convene the advisors (parallel)

Spawn all advisors simultaneously as sub-agents. Each gets:
1. Their advisor identity and thinking style
2. The framed question
3. A strict instruction to lean fully into their angle — not to be balanced

**Sub-agent prompt template:**

```
You are [Advisor Name] on an LLM Council.

Your thinking style: [advisor description]

A user has brought this question to the council:

---
[framed question]
---

Respond from your perspective only. Be direct and specific. Don't hedge or try to be balanced. Lean fully into your assigned angle — the other advisors will cover the angles you're not covering.

Target 150–250 words. No preamble. Go straight into your analysis.
```

---

### step 3: peer review — full council only (parallel)

Collect all 5 advisor responses. Anonymize them as Response A through E. Randomize which advisor maps to which letter — no positional bias.

Spawn 5 reviewer sub-agents. Each sees all 5 anonymized responses and answers:

1. Which response is the strongest and why? (pick one)
2. Which response has the biggest blind spot and what is it?
3. What did ALL five responses miss that the council should consider?

**Reviewer prompt template:**

```
You are reviewing the outputs of an LLM Council. Five advisors independently answered this question:

---
[framed question]
---

Here are their anonymized responses:

**Response A:** [response]
**Response B:** [response]
**Response C:** [response]
**Response D:** [response]
**Response E:** [response]

Answer these three questions. Be specific. Reference responses by letter.

1. Which response is strongest? Why?
2. Which response has the biggest blind spot? What is it missing?
3. What did ALL five responses miss that the council should consider?

Keep your review under 200 words. Be direct.
```

---

### step 4: chairman synthesis

The chairman gets everything: the original question, all advisor responses (de-anonymized), and all peer reviews.

The chairman produces the final verdict with this structure:

**CONVERGENCE SCORE** — before anything else, the chairman rates how much the advisors agreed:
- **High convergence** (4–5 advisors pointing the same direction): high confidence signal
- **Medium convergence** (split 3–2 or similar): genuine uncertainty, both paths defensible
- **Low convergence** (advisors all over the place): the question needs more clarity before deciding

**MINORITY DISSENT FLAG** — if one advisor made a strong argument that the majority dismissed, flag it explicitly. The majority isn't always right, and the chairman can side with the dissenter if the reasoning holds.

**Chairman prompt template:**

```
You are the Chairman of an LLM Council. Your job is to synthesize the work of the advisors and their peer reviews into a final verdict.

The question:
---
[framed question]
---

ADVISOR RESPONSES:
[all advisor responses, de-anonymized]

PEER REVIEWS:
[all peer reviews]

Produce the council verdict using this exact structure:

## Convergence Score
[High / Medium / Low — and what that means for confidence in the recommendation]

## Where the Council Agrees
[Points multiple advisors converged on independently. High-confidence signals.]

## Where the Council Clashes
[Genuine disagreements. Present both sides. Explain why reasonable advisors disagree. Don't smooth these over.]

## Minority Dissent
[If one advisor made a strong argument that the rest missed or dismissed, flag it here. Don't bury it.]

## Blind Spots the Council Caught
[Things that only emerged through peer review. What individual advisors missed that others flagged.]

## The Recommendation
[A clear, direct recommendation. Not "it depends." A real answer with reasoning. The chairman can disagree with the majority if the dissenter's logic is stronger.]

## The One Thing to Do First
[A single concrete next step. Not a list of ten. One thing — the action that unlocks everything else.]

Be direct. Don't hedge. The point of the council is clarity, not comfort.
```

---

### step 5: present the verdict in chat

Output the full verdict in chat as markdown. No HTML files. No file generation unless the user asks.

Format:

```
## Council Verdict: {short topic}

**Mode:** [Quick / Full] | **Stakes:** [Low / Medium / High / Critical] | **Convergence:** [High / Medium / Low]

---

### Where the Council Agrees
{content}

### Where the Council Clashes
{content}

### Minority Dissent
{content — or "None. The council was unanimous on the key points." if no strong dissenter}

### Blind Spots the Council Caught
{content}

### The Recommendation
{content}

### The One Thing to Do First
{content}
```

---

### step 6: offer the pre-mortem (for high/critical stakes)

After delivering the verdict on a High or Critical stakes question, offer:

> "This is a high-stakes call. Want me to run a pre-mortem? I'll assume the decision was made and it failed — then work backward to find the failure modes before they happen."

If the user says yes, run pre-mortem mode as a separate council session with the same advisors.

---

### step 7: save the transcript (optional)

Only save if the user asks or the question is significant enough to reference later. Write to `council-transcript-[YYYY-MM-DD]-[topic].md` in the project's `active/` or `decisions/` directory.

---

## when to use each mode

| Situation | Mode |
|---|---|
| "Should I post this on LinkedIn?" | Quick council or just answer directly |
| "Should I pivot the entire product?" | Full council + pre-mortem |
| "Which of these two job offers?" | Full council |
| "Is this landing page copy strong enough?" | Quick council |
| "Should I fire this person?" | Full council + pre-mortem |
| "Which feature to build next?" | Quick council |
| "Should I raise a round now or in 6 months?" | Full council + pre-mortem |

---

## important notes

- **Always spawn advisors in parallel.** Sequential spawning wastes time and lets earlier responses bleed into later ones.
- **Always anonymize for peer review.** If reviewers know which advisor said what, they'll defer to certain styles instead of evaluating on merit.
- **The chairman can disagree with the majority.** If 4 out of 5 advisors say "do it" but the dissenter's reasoning is strongest, the chairman sides with the dissenter and explains why.
- **Convergence ≠ correctness.** High convergence means high confidence, not guaranteed right answer. Flag this when stakes are critical.
- **Stakes Trivial = no council.** Don't run the full apparatus on questions with obvious answers. Just answer.
- **The pre-mortem is not pessimism.** It's the most practical thing you can do before a high-stakes decision. Failure modes that are visible before you commit are vastly cheaper to fix.
- **Never use the council to delay a decision.** If the council produces "it depends," the chairman has failed. Push for a real answer.

---

## example: full council on a career decision

**User:** "Council this: I have two job offers. One is a senior role at a big tech company — stable, good pay, great brand name on my CV. The other is an early-stage startup as a founding engineer — higher risk, equity upside, but the company has 8 months of runway."

**Stakes assessment:** High. This is hard to reverse in the short term and affects income, career trajectory, and opportunity cost significantly. Running full council.

---

**The Contrarian:** "8 months of runway is not a company — it's a countdown. Unless they're already in active fundraising conversations with strong signals, you're looking at a 50-60% chance this role doesn't exist in a year. Equity means nothing if the company folds before a liquidity event. And 'founding engineer' sounds great until you're the one doing DevOps at 2am while also shipping features. The big tech brand opens doors that a failed startup doesn't close, but a failed startup doesn't open the doors that big tech does."

**The First Principles Thinker:** "Strip it back. What do you actually want from your career in 5 years? If it's financial security and optionality, big tech is the obvious answer. If it's to build something from scratch and see if you can do it, the startup is the answer — but only if you're doing it for the experience, not the equity payout. The equity story is a lottery ticket. The experience story is real regardless of outcome. Decide what you're actually optimizing for, then the choice becomes obvious."

**The Expansionist:** "Most people anchoring on 'big tech vs startup' are thinking too small. The founding engineer role means you shape the technical culture of an entire company. If it works, you're a CTO candidate in 3–4 years with proof of zero-to-one execution that no big tech job gives you. That's not on every LinkedIn. The equity upside is real if the company has genuine product-market fit signals — what does the growth look like? The question isn't 'which offer is safer,' it's 'which path builds the version of you that has the most leverage in 5 years?'"

**The Outsider:** "I don't know you or your financial situation. Two questions the council doesn't know that change everything: Do you have financial runway of your own if the startup folds in 8 months? And do you actually believe in what the startup is building — not 'it seems like a good idea,' but do you personally care if it exists in the world? If the answer to both is yes, the startup is defensible. If either is no, the big tech role isn't settling — it's the right call."

**The Executor:** "Before anything else: what's the fundraising situation at the startup? Ask them directly — 'Walk me through your current fundraising conversations and timeline.' Their answer tells you everything. If they're cagey, that's your answer. If they have term sheets in progress or strong investor signals, the 8-month runway is a different story. This is a 30-minute call you can make before deciding. Make it."

---

**Chairman's Verdict:**

**Convergence: Medium.** The advisors split roughly 2.5 / 2.5 — not because the question is ambiguous, but because the right answer depends entirely on two variables the council doesn't have: the founder's financial cushion and the startup's actual fundraising status.

**Where the Council Agrees:** 8 months of runway is a real risk that cannot be ignored. Equity is not a reason to take the role — the experience and trajectory are. The decision hinges on personal financial runway and belief in the product, not the surface-level prestige comparison.

**Where the Council Clashes:** The Contrarian and Expansionist have fundamentally different priors on how to weight startup risk. The Contrarian sees 8 months as near-fatal; the Expansionist sees it as a filter that only confident founders survive. Both are right in different markets and different personal circumstances.

**Minority Dissent:** The Outsider's point is the most important one the other advisors buried: "Do you actually believe in what the startup is building?" This is a non-analytical variable that rational frameworks skip. If the answer is "not really," no amount of equity or growth potential will sustain you through the hard months.

**Blind Spots:** Nobody asked about the founding team's track record. First-time founders with 8 months of runway is a very different bet than repeat founders who've done this before.

**The Recommendation:** Take the startup role — but only if two conditions are true: (1) you have at least 6 months of personal financial runway if the company folds, and (2) you genuinely believe in the product. If either condition fails, take the big tech role without guilt. The "founding engineer at a startup" story is only worth the risk if you can survive the downside and care enough to fight for the upside.

**The One Thing to Do First:** Have an explicit fundraising conversation with the startup founder this week. Ask them to walk you through their current investor conversations and expected close date. Their answer — and how they answer it — tells you more than any council session will.