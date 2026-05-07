# Better LLM Council

Stop asking Claude one question and getting one answer. Run it through a council. Get more insights.

---

Karpathy had an idea: send a question to multiple AI models, have them review each other's answers anonymously, then a chairman produces the final verdict. The peer review round is what makes it work — models catch each other's blind spots in a way single-model reasoning never does.

This is that idea, rebuilt as a Claude skill. Instead of multiple models, it uses sub-agents with distinct thinking lenses. Same methodology, runs entirely inside Claude, no API juggling required.

---

## what you actually get

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

## install

Copy `SKILL.md` into your Claude skills directory:

```
.claude/skills/better-llm-council/SKILL.md
```

That's it. Claude picks it up automatically.

---

## how to trigger it

```
council this: [your question]
```

Other triggers:
- `war room this: [question]`
- `pressure-test this: [idea]`
- `debate this: [decision]`
- `pre-mortem this: [plan]` — assumes the decision failed, works backward
- `quick council this: [question]` — 3 advisors, faster, for lower-stakes calls

---

## three modes

**Quick Council** — 3 advisors, no peer review, chairman synthesizes directly. For tactical decisions where you're 80% sure and want a sanity check.

**Full Council** — 5 advisors, full peer review round, convergence score. For anything where being wrong is expensive.

**Pre-mortem** — the council assumes the decision was made and it failed, then works backward. Best thing you can do before a high-stakes commitment.

---

## the eight advisors

The full council uses 5; selection is automatic based on question domain.

| Advisor | Job |
|---|---|
| **The Contrarian** | Find the fatal flaw |
| **The First Principles Thinker** | Strip assumptions, rebuild from scratch |
| **The Expansionist** | Find the upside everyone's ignoring |
| **The Outsider** | React with zero context — what a fresh user sees |
| **The Executor** | Is this actually doable, and what's step one |
| **The Technical Architect** | What breaks at scale, what the maintenance cost looks like |
| **The Customer** | What the end user actually experiences |
| **The Systems Thinker** | Second-order effects, feedback loops, unintended consequences |

Request specific advisors: `council this with Technical Architect, Contrarian, and Expansionist`

---

## why not just ask Claude five times

Asking the same question five times gives you five polite, balanced, slightly different versions of the same answer.

Two things make the council different:

**Forced perspective.** Each advisor is told not to be balanced — to lean fully into their angle. The Contrarian isn't trying to be fair. The Expansionist isn't worrying about risk. The tension between them is the point.

**Anonymous peer review.** After advisors respond, they review each other's work without knowing who said what. That's the Karpathy insight — models catch blind spots in each other's reasoning that they'd miss working alone. The chairman synthesizes the result of that tension, not the average of five opinions.

---

## when to use it

Good council questions: major pivots, job offers, pricing decisions, architecture calls, hiring, positioning choices — anything where being wrong costs real time or money.

Bad council questions: things with one right answer, creation tasks, "summarize this."

If you already know the answer and want validation, the council will probably tell you things you don't want to hear. That's the point.

---

## convergence score

The chairman rates how much advisors agreed:

- **High** (4–5 pointing the same way): confident recommendation
- **Medium** (3–2 split): genuine uncertainty, both paths defensible
- **Low** (all over the place): the question needs more clarity before you decide

High convergence doesn't mean the majority is right. The chairman can side with a single strong dissenter if the reasoning holds.

---

## credits

- Core methodology: [Andrej Karpathy's LLM Council](https://x.com/karpathy)
- Built as a Claude skill by [Abdul Malik Anvar Mackey](https://github.com/malik-builds)

---

## contributing

Missing an advisor archetype? Domain where auto-selection picks the wrong five? Open an issue or PR.

---

MIT license. Use it, fork it, improve it.
