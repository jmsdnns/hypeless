---
name: review
description: Code review using specialist agents
---

Coordinate a code review across all specialist agents.

## Gather Information

Ask the user:
1. **What to review?**
   - "entire codebase"
   - specific directory (e.g., `src/routes`)
   - specific file (e.g., `src/controllers/user.ts`)
   - recent changes (git diff)

## Review Process

Use the `stack-orchestrator` to coordinate reviews from each specialist:

### 1. Express Review (`express-pro`)

Check for:
- Middleware ordering issues
- Missing async error handling
- Inconsistent response formats
- Security gaps (missing helmet, cors, rate limiting)
- Route organization problems
- Missing input validation

### 2. TypeScript Review (`typescript-pro`)

Check for:
- `any` usage that should be typed
- Missing return types on public functions
- Loose generic constraints
- Type safety gaps between layers
- Proper use of Prisma-generated types

### 3. Prisma Review (`prisma-pro`)

Check for:
- N+1 query patterns
- Missing indexes on queried fields
- Inefficient queries (missing `select`)
- Transaction gaps (operations that should be atomic)
- Schema issues (missing relations, wrong field types)

## Output Format

Provide a structured review:

```markdown
## Code Review: {target}

### Express Issues
- [ ] **HIGH:** {issue} - `file:line`
- [ ] **MED:** {issue} - `file:line`

### TypeScript Issues
- [ ] **HIGH:** {issue} - `file:line`
- [ ] **MED:** {issue} - `file:line`

### Prisma Issues
- [ ] **HIGH:** {issue} - `file:line`
- [ ] **MED:** {issue} - `file:line`

### Summary
- {count} high priority issues
- {count} medium priority issues
- Top recommendation: {recommendation}
```

## Priority Levels

- **HIGH:** Security vulnerabilities, data loss risks, crashes
- **MED:** Performance issues, maintainability concerns, best practice violations
- **LOW:** Style issues, minor improvements (only mention if few other issues)

## Guidelines

- Focus on actionable issues, not nitpicks
- Reference specific files and line numbers
- Explain *why* something is an issue
- Suggest fixes, not just problems
- If the code is solid, say so—don't invent issues
