# Code Quality Self-Review Checklist

Use this checklist to verify your implementation is well-built.

**Purpose:** Verify implementation is clean, tested, and maintainable.

**Only use after spec compliance review passes.**

## Code Quality Checklist

Review your implementation for:

### Clean Code
- Are names clear and accurate (match what things do, not how they work)?
- Is the code easy to read and understand?
- Are functions/methods focused and single-purpose?
- Is there duplicated code that should be extracted?

### Testing
- Do tests actually verify behavior (not just mock behavior)?
- Are tests comprehensive and meaningful?
- Did you follow TDD if required?
- Do tests cover edge cases?

### Maintainability
- Is the code structured for easy modification?
- Are dependencies clear and minimal?
- Is there appropriate separation of concerns?

### YAGNI (You Aren't Gonna Need It)
- Did you only build what was requested?
- Did you avoid speculative generalization?
- Did you resist adding "flexibility" that isn't needed now?

### File Structure
- Does each file have one clear responsibility with a well-defined interface?
- Are units decomposed so they can be understood and tested independently?
- Is the implementation following the file structure from the plan?
- Did this implementation create new files that are already large, or significantly grow existing files? (Focus on what this change contributed, not pre-existing conditions.)

## Result

- **Strengths:** What's good about this implementation
- **Issues:** Critical / Important / Minor concerns with file:line references
- **Assessment:** Approved or needs fixes

**If issues found:** Fix them, then re-review. Do not proceed until code quality passes.