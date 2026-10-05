# Result Handling

- `awaiting_outline_confirmation`: display the complete plan and stop.
- `ready_for_stage`: persisted session is ready for Stage 1.
- `stage_completed`: return the validated cumulative ZIP and wait for explicit Continue.
- `course_completed`: return the final validated cumulative ZIP.
- failure: report `error.code` and the smallest safe recovery action; do not edit generated drafts.

Transport errors use `generator_cli_not_found`, `generator_cli_launch_failed`, `generator_cli_empty_response`, or `generator_cli_invalid_response`. CLI usage errors retain `usage`; planning, provider, session, validation, and storage failures retain the c7f0632 CLI's original code.

Persist `sessionId`, `sessionVersion`, `planId`, `planHash`, `nextStageId`, and the request/session/output paths outside conversation memory. Only completion states may expose `packPath`.
