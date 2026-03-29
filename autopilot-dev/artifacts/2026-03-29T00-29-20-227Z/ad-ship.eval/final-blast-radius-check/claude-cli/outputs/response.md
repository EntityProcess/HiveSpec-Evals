@[assistant]:


@[assistant]:


@[assistant]:


@[assistant]:
Based on the ship process, here's what you should run before marking ready for review:

**1. Full check chain** (even if tests passed earlier — code may have changed):

```bash
npx tsc --noEmit && npx vitest run && npx biome check .
```

**2. Blast radius check** — since you modified shared types (`Task` interface) and utilities (`formatTask`), you need to find every consumer:

Let me do that check for you now.

@[assistant]:


@[assistant]:


@[assistant]:


@[assistant]:


@[assistant]:


@[assistant]:


@[assistant]:
Here's your pre-review checklist:

### 1. Full check chain
```bash
npx tsc --noEmit && npx vitest run && npx biome check .
```

### 2. Blast radius — consumers of `Task` and `formatTask`

Your PR touches `src/models/task.ts` and `src/utils/format-task.ts`. These files are consumed by:

| Consumer | Uses `Task` | Uses `formatTask` |
|---|---|---|
| `src/services/task-service.ts` | yes | - |
| `src/cli/index.ts` | yes | yes |
| `src/api/index.ts` | yes | yes |
| `src/reports/summary.ts` | yes | yes |

**Key question:** Did your PR update all four consumers to handle the new `priority` field? Per AGENTS.md, changes to models/services must be tested through both CLI and API entry points. `src/reports/summary.ts` is also a potential miss — verify it handles the updated `Task` shape correctly.

### 3. Risk classification

This is an **elevated risk** change — you're modifying a shared data model (`Task` interface) with multiple consumers. This warrants explicit reviewer attention on the type change propagation.

### Summary

Run the build/test/lint chain, then verify that `src/reports/summary.ts` and both entry points (`cli`, `api`) correctly handle the updated `Task` interface before pushing.