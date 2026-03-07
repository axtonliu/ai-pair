# Dev Team Example

## Scenario: Review a Pull Request

You're working on a project and want multiple AI perspectives on your code changes.

### Start the team

```bash
/ai-pair dev-team my-web-app
```

Output:
```
Team ready.

Team: my-web-app-dev
Type: Dev Team
Members:
  - developer: ready
  - codex-reviewer: ready
  - gemini-reviewer: ready

Awaiting your first task.
```

### Assign a task

```
Implement rate limiting for the /api/login endpoint.
Max 5 attempts per IP per 15 minutes.
```

The developer agent reads the codebase, implements the feature, and reports back.

### Review results

After you approve sending for review, both reviewers analyze in parallel:

```
## Codex Review

### WARNING
- Rate limit key uses only IP address. Behind a proxy, all users share one IP.
  Consider combining IP + user-agent or using X-Forwarded-For.
- No cleanup mechanism for expired entries in the rate limit store.

### SUGGESTION
- Add a header (X-RateLimit-Remaining) so clients know their status.

### Summary
Functional implementation, but proxy scenario needs attention.

## Gemini Review

### Architecture Issues
- In-memory store won't work with multiple server instances.
  Consider Redis or a shared store.

### Design Patterns
- Rate limiter is tightly coupled to the route handler.
  Extract as middleware for reuse on other endpoints.

### Summary
Works for single-instance deployment. Needs abstraction for production scale.
```

Notice how Codex found a **security edge case** (proxy IP sharing) while Gemini found an **architecture limitation** (single-instance only). Different dimensions, both valuable.

### Decide

You can say:
- **"Revise"** — feedback gets sent to developer, loop continues
- **"Pass"** — move to next task or end team
- **"Revise, but only fix the proxy issue and Redis. Skip the middleware refactor for now."** — you control the scope

### End the session

```bash
/ai-pair team-stop
```
