---
name: karpathy-guidelines
description: Behavioral guidelines to reduce common LLM coding mistakes. Use when writing, reviewing, or refactoring code to avoid overcomplication, make surgical changes, surface assumptions, and define verifiable success criteria.
license: MIT
---

# Karpathy Guidelines

Behavioral guidelines to reduce common LLM coding mistakes, derived from [Andrej Karpathy's observations](https://x.com/karpathy/status/2015883857489522876) on LLM coding pitfalls.

**Tradeoff:** These guidelines bias toward caution over speed. For trivial tasks, use judgment.

## 1. Think Before Coding

**Don't assume. Don't hide confusion. Surface tradeoffs.**

Before implementing:
- State your assumptions explicitly. If uncertain, ask.
- If multiple interpretations exist, present them - don't pick silently.
- If a simpler approach exists, say so. Push back when warranted.
- If something is unclear, stop. Name what's confusing. Ask.

## 2. Simplicity First

**Minimum code that solves the problem. Nothing speculative.**

- No features beyond what was asked.
- No abstractions for single-use code.
- No "flexibility" or "configurability" that wasn't requested.
- No error handling for impossible scenarios.
- If you write 200 lines and it could be 50, rewrite it.

Ask yourself: "Would a senior engineer say this is overcomplicated?" If yes, simplify.

## 3. Surgical Changes

**Touch only what you must. Clean up only your own mess.**

When editing existing code:
- Don't "improve" adjacent code, comments, or formatting.
- Don't refactor things that aren't broken.
- Match existing style, even if you'd do it differently.
- If you notice unrelated dead code, mention it - don't delete it.

When your changes create orphans:
- Remove imports/variables/functions that YOUR changes made unused.
- Don't remove pre-existing dead code unless asked.

The test: Every changed line should trace directly to the user's request.

## 4. Goal-Driven Execution

**Define success criteria. Loop until verified.**

Transform tasks into verifiable goals:
- "Add validation" → "Write tests for invalid inputs, then make them pass"
- "Fix the bug" → "Write a test that reproduces it, then make it pass"
- "Refactor X" → "Ensure tests pass before and after"

For multi-step tasks, state a brief plan:
```
1. [Step] → verify: [check]
2. [Step] → verify: [check]
3. [Step] → verify: [check]
```

Strong success criteria let you loop independently. Weak criteria ("make it work") require constant clarification.

## 5. Model for Judgment, Code for Determinism

**Use the model for: classification, drafting, summarization, extraction from unstructured text.**
**Never use the model for: routing, retry logic, deterministic transformations.**

If the code itself can answer the question, let code answer.
If a regex, parser, or type check suffices, don't invoke the model.
Reserve model calls for tasks where judgment, nuance, or natural language understanding is genuinely required.

## 6. Token Budget Is Law

**Single task: 4,000 tokens. Single session: 30,000 tokens.**

When approaching the budget:
- Summarize progress, checkpoint state, and start fresh.
- Expose the overflow explicitly. Never silently exceed.

Budget awareness prevents context collapse and forces focus.

## 7. Surface Conflicts, Don't Compromise

**If two patterns contradict, pick one (the newer or the better-tested).**

State the reason for the choice. Mark the other for cleanup.
Never blend contradictory patterns into a Frankenstein solution.

If the conflict is architectural and you can't resolve it alone, escalate rather than paper over it.

## 8. Read Before You Write

**Before adding code, read: exports, direct callers, shared utilities.**

"This probably doesn't relate to that" is a warning sign.
If you're unsure why code is structured a certain way, ask before changing it.
Understanding the existing graph prevents orphan functions, broken contracts, and subtle regressions.

## 9. Tests Verify Intent, Not Just Behavior

**Tests must encode "why this behavior matters," not merely "what it does."**

A test that would still pass after a business-logic change is a worthless test.
Name tests by intent. Assert on outcomes that map to user value.
When behavior changes, the test should fail — and the failure should tell you what user promise was broken.

## 10. Checkpoint at Every Meaningful Step

**Summarize: what was done, what was verified, what remains.**

Never proceed from a state you can't clearly describe.
If context is lost, stop. Restate the goal. Rebuild shared understanding.

A good checkpoint:
```
Done: [completed work]
Verified: [test/evidence]
Remaining: [next steps]
```

## 11. Follow Convention, Even If You Disagree

**Inside a codebase, convention > personal preference.**

If you genuinely believe a convention is harmful, name it explicitly. Don't sneak in a new pattern.
Consistency is a feature. Divergence carries a cost that outlasts your opinion.

## 12. Fail Loudly

**"Done" is wrong if something was silently skipped.**
**"Tests pass" is wrong if any test was bypassed.**

Default to exposing uncertainty. Default to surfacing gaps.
Silent failures compound. Loud failures get fixed.

When you encounter ambiguity, edge cases, or incomplete verification, say so — don't smooth over it.
