# Agent Result Schema

All results are JSON objects. Treat unknown fields as forward-compatible additions and branch on stable fields.

## Startup

```json
{
  "ok": true,
  "version": "generator-core.v0.6",
  "protocols": ["v1", "v2"],
  "defaultAgentProtocol": "v2"
}
```

License and provider status results expose readiness and stable error codes only. They never expose License contents, signatures, API keys, or credential values.

## Plan and confirmation

`plan` returns `ok: true`, `state: "awaiting_outline_confirmation"`, and a complete validated plan. The Agent must show the outline and wait. `confirm-plan` returns a persisted `sessionId`, `sessionVersion`, `planId`, `planHash`, and `nextStageId`.

## Stage completion

Only these states may include a CoursePack path:

```text
stage_completed | course_completed
```

The completion result includes `sessionId`, `sessionVersion`, `stageId`, `packPath`, `packId`, `packVersion`, `courseId`, `courseVersion`, `packHash`, completed lesson IDs, and optionally `nextStageId`.

## Failure

```json
{
  "ok": false,
  "error": { "code": "stable_error_code", "message": "safe human-readable detail" }
}
```

Do not repair or reinterpret failed drafts. Report the stable code and the smallest safe recovery action: configure License/provider, correct the request, retry the same Stage, or ask the user to revise and reconfirm the plan.
