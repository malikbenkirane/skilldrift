# Spec Compliance Self-Review Checklist

Use this checklist to verify your implementation matches the specification.

**Purpose:** Verify you built what was requested (nothing more, nothing less)

## What Was Requested

[The task requirements you implemented]

## CRITICAL: Do Not Trust Your Own Claims

You finished implementing. Your mental model may be incomplete or optimistic.
You MUST verify everything independently.

**DO NOT:**
- Trust your memory of what you implemented
- Accept your own claims about completeness
- Assume you interpreted requirements correctly

**DO:**
- Read the actual code you wrote
- Compare actual implementation to requirements line by line
- Check for missing pieces you thought you implemented
- Look for extra features you didn't plan

## Self-Review Checklist

Read your implementation code and verify:

**Missing requirements:**
- Did you implement everything that was requested?
- Are there requirements you skipped or missed?
- Did you claim something works but didn't actually implement it?

**Extra/unneeded work:**
- Did you build things that weren't requested?
- Did you over-engineer or add unnecessary features?
- Did you add "nice to haves" that weren't in spec?

**Misunderstandings:**
- Did you interpret requirements differently than intended?
- Did you solve the wrong problem?
- Did you implement the right feature but wrong way?

## Result

- **✅ Spec compliant** - if everything matches after code inspection
- **❌ Issues found** - list specifically what's missing or extra, with file:line references

**If issues found:** Fix them, then re-review. Do not proceed until spec compliance passes.