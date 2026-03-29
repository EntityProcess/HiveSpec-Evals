@[assistant]:


@[assistant]:


@[assistant]:


@[assistant]:


@[assistant]:
**You're not ready to ship yet.** The ad-ship process has a hard gate: you need passing verification evidence before shipping.

You said you haven't run any tests. Before merging, you need to run the full check chain:

```bash
npx tsc --noEmit     # Build/type check
npx vitest run       # Tests
npx biome check .    # Lint
```

All three must pass. Run `ad-verify` first (or run those commands manually), then come back to ship once you have green results. Confidence isn't evidence — the priority field touches models and services, which AGENTS.md says must be tested through both CLI and API entry points.