# Examples

## First generation

Collect and confirm the four learner fields, write the request JSON, run `plan --protocol v2`, display all lessons and Stage boundaries, and stop. After explicit confirmation, persist the exact returned plan, run `confirm-plan`, then run `generate-stage` for Stage 1 with a fresh idempotency key.

## Continue

Only after the user explicitly asks to continue, run `session-status --protocol v2`. If `nextStageId` is present, invoke `continue --protocol v2` with the same persisted request/session/output directories and a fresh idempotency key.

## Missing CLI

Report `generator_cli_not_found` and the expected bundle path. Do not search unrelated directories.

## Missing provider configuration

Preserve the CLI error. Explain which credential name is missing without requesting its value in chat. The user must configure the secure launcher/process environment and retry.

## Failed Stage

Report the CLI error and preserve the latest completed pack. Use `retry-stage` only for a recoverable failure; do not mutate the accepted plan or prior artifacts.

## Completed course

When the CLI returns `course_completed`, return the validated cumulative `packPath`, versions, hash, completed lessons, and validation summary. Do not call Continue again.
