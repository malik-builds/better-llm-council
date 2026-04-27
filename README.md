# LLM Council

> Run any decision through a council of AI advisors. They think independently. They review each other. A chairman synthesizes the final verdict.

---

You ask one AI a question, you get one answer. That answer might be great. It might be wrong. You have no way to tell because you only saw one perspective.

The council fixes this.

It runs your question through 5 independent advisors, each thinking from a fundamentally different angle — then has them review each other anonymously, then synthesizes everything into a final verdict that tells you where advisors agree, where they clash, and what you should actually do.

This is a Claude skill adapted from **Andrej Karpathy's LLM Council** methodology. Karpathy dispatches queries to multiple models, has them peer-review each other anonymously, then a chairman produces the final answer. This skill does the same inside Claude using sub-agents with distinct thinking lenses.

---

## what it produces

```
## Council Verdict: Should I take the startup offer?

Mode: Full | Stakes: High | Convergence: Medium

### Where the Council Agrees
8 months of runway is a real risk. Equity is not the reason to take this role — experience and trajectory are.

### Where the Council Clashes
The Contrarian sees 8 months as near-fatal. The Expansionist sees it as a filter only confident founders survive.
Both are right in different circumstances.

### Minority Dissent
The Outsider's question was the most important: "Do you actually believe in what they're building?"
If the answer is no, no equity upside sustains you through the hard months.

### The Recommendation
Take the startup — but only if (1) you have 6 months of personal runway if it folds, and (2) you genuinely believe in the product.
If either condition fails, take the big tech role. No guilt required.

### The One Thing to Do First
Ask the founder to walk you through their current fundraising conversations this week.
Their answer — and how they answer it — tells you more than this council will.
```

---

## how to use it

### install

1. Copy `SKILL.md` into your Claude skills directory:
   ```
   .claude/skills/llm-council/SKILL.md
   ```
2. That's it. Claude picks it up automatically.

### trigger it

```
council this: [your question]
```

Other triggers:
- `war room this: [question]`
- `pressure-test this: [idea]`
- `debate this: [decision]`
- `pre-mortem this: [plan]` — assumes the decision failed and works backward
- `quick council this: [question]` — 3 advisors, faster, for lower-stakes calls

---

## the three modes

### quick council
3 advisors. No peer review round. Chairman synthesizes directly.

Best for: tactical decisions, time-sensitive calls, questions where you're 80% sure and want a sanity check.

### full council (default)
5 advisors. Full peer review round where advisors review each other's work anonymously. Chairman synthesizes with a convergence score.

Best for: major pivots, irreversible decisions, anything where being wrong is expensive.

### pre-mortem
The council assumes the decision was made and it *failed*. They work backward to find the failure modes before they happen.

Best for: validating a decision you've already mostly made, stress-testing a plan before you execute.

---

## the eight advisors

The council draws from a roster of 8. The full council uses 5; selection is automatic based on question domain.

| Advisor | What they look for |
|---|---|
| **The Contrarian** | What's wrong, what's missing, what will fail |
| **The First Principles Thinker** | What you're actually trying to solve, stripped of assumptions |
| **The Expansionist** | Upside everyone else is missing |
| **The Outsider** | What fresh eyes see that experts miss |
| **The Executor** | Whether it can actually be done and what the first step is |
| **The Technical Architect** | What breaks at scale, what the maintenance burden looks like |
| **The Customer** | What the end user actually experiences |
| **The Systems Thinker** | Feedback loops, second-order effects, unintended consequences |

You can request specific advisors: `council this with Technical Architect, Contrarian, and Expansionist`

---

## what makes this different from just asking Claude

**Single-model answer:** Claude gives you one synthesized take. It's balanced, it's hedged, it's trying not to offend anyone.

**The council:** Five advisors who are explicitly told to *not* be balanced. The Contrarian looks for failure. The Expansionist looks for upside. The Outsider ignores your context. The peer review forces them to evaluate each other's blind spots. The chairman synthesizes the result of that tension — not the average of five opinions, but what emerges from five perspectives actively challenging each other.

The peer review round is the key insight from Karpathy's methodology. It's what makes this more than "ask five times." When advisors see each other's work anonymously, they catch things they wouldn't have caught reasoning alone.

---

## when to use it

The council is for decisions where being wrong is expensive.

**Good council questions:**
- "Should I launch a $97 workshop or a $497 course?"
- "Which of these three positioning angles is strongest?"
- "I'm thinking of pivoting from X to Y. Am I crazy?"
- "Should I hire a VA or build an automation first?"
- "Is this architecture going to hold at 10x scale?"

**Bad council questions:**
- "What's the capital of France?" — one right answer, no need for perspectives
- "Write me a tweet" — creation task, not a decision
- "Summarize this article" — processing task, not judgment

The council shines when there's genuine uncertainty and the cost of a bad call is high. If you already know the answer and want validation, the council will probably tell you things you don't want to hear. That's the point.

---

## convergence score

The chairman rates how much advisors agreed:

- **High convergence** (4–5 pointing the same direction): strong signal, high confidence in the recommendation
- **Medium convergence** (3–2 split): genuine uncertainty, both paths defensible
- **Low convergence** (advisors all over the place): the question needs more clarity before deciding

Convergence doesn't mean the majority is right. The chairman can side with a single strong dissenter and explain why.

---

## credits

- Core methodology: [Andrej Karpathy's LLM Council](https://x.com/karpathy)
- Claude skills framework: [Anthropic](https://anthropic.com)

---

## contributing

Found an advisor archetype that's missing? Have a domain where the auto-selection doesn't pick the right five? Open an issue or PR.

---

## license

MIT. Use it, fork it, improve it.