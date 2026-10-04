# Generator Agent CLI Contract

## Executable and discovery

The public executable is `learning-by-card-generator-cli.exe`. Use an explicit configured path when supplied; otherwise use the app-owned installed path documented by the Windows package. A portable/Runner copy may be selected by an explicit override. Do not scan arbitrary disks or modify the system `PATH`.

Every invocation writes exactly one JSON result object to stdout. Diagnostics, if any, belong on stderr and must be redacted.

## Startup checks

```powershell
learning-by-card-generator-cli.exe version --json
learning-by-card-generator-cli.exe capabilities --json
learning-by-card-generator-cli.exe license status --json
learning-by-card-generator-cli.exe provider status --json
```

The Agent lane requires `defaultAgentProtocol: "v2"`, support for `v2`, a ready License, and a ready provider configuration before starting production.

## Production commands

```text
plan --protocol v2 --request <request.json> --json
confirm-plan --protocol v2 --plan <plan.json> --session-dir <dir> --json
generate-stage --protocol v2 --session <id> --session-dir <dir> --request <request.json> --output <dir> --idempotency-key <key> --json
session-status --protocol v2 --session <id> --session-dir <dir> --json
continue --protocol v2 --session <id> --session-dir <dir> --request <request.json> --output <dir> --idempotency-key <key> --json
retry-stage --protocol v2 --session <id> --session-dir <dir> --request <request.json> --output <dir> --idempotency-key <key> --json
```

`compile` is a legacy/development command and is not the Agent course workflow. A later Stage requires explicit user `Continue`.

## Exit codes

- `0`: operation succeeded;
- `2`: usage or argument error;
- other non-zero values: stable License, provider, request/session, provider request, pack validation, storage, or internal failure.

Use the JSON `error.code` as the primary recovery signal. Never parse human-readable messages as protocol.
