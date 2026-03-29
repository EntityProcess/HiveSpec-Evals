# autopilot-dev Plugin Eval Results

**Date:** 2026-03-29
**PR:** [EntityProcess/agentv#824](https://github.com/EntityProcess/agentv/pull/824)
**Issue:** [EntityProcess/agentv#823](https://github.com/EntityProcess/agentv/issues/823)
**Plugin:** `plugins/autopilot-dev/` — phase-based delivery lifecycle (claim → explore → design → plan → implement → verify → ship)

## Final Results

| Eval | Tests | Claude CLI | Pi CLI |
|---|---|---|---|
| ad-explore | 3 | **3/3 PASS (1.000)** | **3/3 PASS (1.000)** |
| ad-verify | 4 | **4/4 PASS (1.000)** | 3/4 (0.833) |
| ad-design | 3 | **3/3 PASS (1.000)** | 0/3 (0.417) |
| ad-claim | 3 | **3/3 PASS (1.000)** | 2/3 (0.833) |
| ad-ship | 4 | **4/4 PASS (1.000)** | 1/4 (0.708) |
| **Total** | **17** | **17/17 PASS** | **9/17 pass** |

## Targets

- **claude-cli**: Claude Code CLI v2.1.86 (claude-cli provider)
- **pi-cli**: Pi Coding Agent CLI via OpenRouter (openai/gpt-5.1-codex)
- **grader**: Gemini 3 Flash Preview (gemini-flash)

## Reproduction

```bash
cd agentv
bun apps/cli/src/cli.ts eval run --target claude-cli --workers 1 evals/autopilot-dev/ad-explore.eval.yaml
```

Requires:
- `.env` with `GOOGLE_GENERATIVE_AI_API_KEY` (for gemini-flash grader)
- `claude` CLI installed (for claude-cli target)
- `.agentv/targets.yaml` with claude-cli and pi-cli targets configured

## Directory Structure

```
results/
  claude-cli/     # Console output per eval
  pi-cli/
artifacts/        # JSONL index, grading.json, timing.json per test
```

## Key Findings

1. **SKILL.md as file input was needed** for design/ship/verify/claim evals — workspace skill discovery alone wasn't sufficient to trigger skills reliably. Including the SKILL.md in the test input ensures the agent reads the skill discipline.
2. **Explore evals pass without file inputs** — the workspace setup hook copies skills to `.claude/skills/`, `.agents/skills/`, `.pi/skills/` and agents discover them naturally for exploration tasks.
3. **Ship tests must be decision-making focused** — testing actual git operations in a sandbox leads to false negatives because agents check `git diff` and find mismatches. Frame ship tests as "should I auto-merge or get review?" with change descriptions in the prompt.
4. **Claim tests must not depend on GitHub** — provide issue bodies directly in prompts since workspace has no GitHub remote.
