---
name: minicourse-generator-agent
description: Use the bundled Generator v2 CLI to plan a MiniCourse, confirm its complete outline, generate Stage 1, and continue later Stages only after explicit user approval. Do not use for Reader rendering or direct CoursePack authoring.
---

# MiniCourse Generator Agent

Use the bundled `bin/learning-by-card-generator-cli.exe` built from commit `c7f0632` by Windows Runner `37133423405`. The CLI owns prompts, provider calls, protocol compilation, assets, validation, hashing, and ZIP construction.

## Workflow

1. Collect and confirm the learner's current foundation, learning goal, knowledge type, and application scenario. Accept optional source material and constraints.
2. Resolve the CLI from `MINICOURSE_GENERATOR_CLI` when explicitly configured; otherwise use `../bin/learning-by-card-generator-cli.exe` relative to this Skill directory. Do not search the disk or modify `PATH`.
3. Verify the executable exists; otherwise return `generator_cli_not_found`. Invoke the executable directly and run `version --json` first; require exit code `0`, valid JSON, and `generator-core.v0.6`.
4. Write a bounded request JSON matching [references/request-schema.md](references/request-schema.md). Never write provider keys into it.
5. Invoke `plan --protocol v2 --request <absolute-path> --json`. Present the complete outline and Stage boundaries, then stop for explicit confirmation.
6. Save the confirmed plan exactly and invoke `confirm-plan --protocol v2`. Persist the returned session identity.
7. Invoke `generate-stage --protocol v2` for Stage 1 with an explicit session directory, output directory, request file, and fresh idempotency key.
8. Return a ZIP path only from `stage_completed` or `course_completed`.
9. On explicit `Continue`, call `session-status --protocol v2` first and then invoke `continue --protocol v2` for the persisted `nextStageId`. Never infer continuation from chat memory.
10. Retry only after user intent or a recoverable failure, using `retry-stage --protocol v2` and a fresh idempotency key.

Read [references/cli-contract.md](references/cli-contract.md) before invocation and [references/result-schema.md](references/result-schema.md) before interpreting output.
Read [references/examples.md](references/examples.md) for first generation, Continue, missing CLI/provider, retry, and completion handling.

## Safety boundaries

- Do not ask the user to paste an API key into chat, arguments, or request JSON. The c7f0632 CLI uses credentials already supplied to its process environment by the user's secure launcher.
- Do not load or reproduce the frozen Generator prompts.
- Do not hand-author protocol artifacts or ZIPs.
- Do not call a later Stage without explicit `Continue`.
- Do not execute course `code` or `terminal` content.
- Treat all CLI JSON and produced ZIPs as untrusted until the CLI reports successful validation.
- Preserve every CLI exit code and JSON error object. If stdout is empty or invalid JSON, report `generator_cli_empty_response` or `generator_cli_invalid_response` without echoing stderr.
