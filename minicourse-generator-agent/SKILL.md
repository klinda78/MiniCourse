---
name: minicourse-generator-agent
description: Orchestrate the installed Generator v2 CLI from an Agent by collecting intent, confirming the complete outline, generating one Stage at a time, and interpreting stable JSON results. Do not use for direct CoursePack authoring, Reader rendering, or learner progress.
---

# MiniCourse Generator Agent Adapter

This is a thin adapter for the installed `learning-by-card-generator-cli.exe`. Keep all planning prompts, provider calls, protocol compilation, asset handling, validation, hashing, and ZIP construction inside the executable.

## Required workflow

1. Collect and confirm `currentFoundation`, `learningGoal`, `knowledgeType`, and `applicationScenario`. Accept optional source material and user constraints.
2. Resolve the CLI path from an explicit configuration first, then the documented installed location. Do not search the disk broadly or modify `PATH`.
3. Run `version --json`, `capabilities --json`, `license status --json`, and `provider status --json`. Stop with a stable recovery message when the CLI is missing, unsupported, unlicensed, or not provider-ready.
4. Write a bounded request JSON file containing intent and non-secret constraints only. Never ask for or write provider keys.
5. Invoke `plan --protocol v2 --request <request> --json` and present the complete outline and Stage boundaries. Wait for explicit user confirmation.
6. Persist the confirmed plan exactly, then invoke `confirm-plan --protocol v2` and `generate-stage --protocol v2` for Stage 1.
7. Return a CoursePack path only when the CLI reports `stage_completed` or `course_completed`.
8. For `Continue`, query `session-status --protocol v2`, use the persisted next Stage, create a fresh idempotency key, and invoke `continue --protocol v2`. Never infer the next Stage from conversation memory or call it automatically.
9. For retry, use `retry-stage --protocol v2` with the persisted session and a fresh idempotency key. Do not edit drafts or bypass validation.

Read [references/cli-contract.md](references/cli-contract.md) before invoking the executable, [references/request-schema.md](references/request-schema.md) when writing a request, and [references/result-schema.md](references/result-schema.md) when interpreting output.

## Hard boundaries

- Never modify `skills/minicourse-generator/` or `skills/minicourse-generator-v2/`.
- Never include credentials, License contents, provider payloads, temporary URLs, prompts, or session internals in request files, logs, or CoursePacks.
- Never claim completion from a plan, draft, or unvalidated ZIP.
- Never execute `code` or `terminal` content returned as course material.
- Preserve v1 compatibility only when explicitly requested; the Agent lane always passes `--protocol v2`.
