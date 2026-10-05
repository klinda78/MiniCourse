# CLI Contract

Executable: `bin/learning-by-card-generator-cli.exe`, built from `c7f0632` by Run `37133423405`.

Supported Agent operations:

```text
version --json
plan --protocol v2 --request <request.json> --json
confirm-plan --protocol v2 --plan <plan.json> --session-dir <directory> --json
generate-stage --protocol v2 --session <id> --session-dir <directory> --request <request.json> --output <directory> --idempotency-key <key> --json
session-status --protocol v2 --session <id> --session-dir <directory> --json
continue --protocol v2 --session <id> --session-dir <directory> --request <request.json> --output <directory> --idempotency-key <key> --json
retry-stage --protocol v2 --session <id> --session-dir <directory> --request <request.json> --output <directory> --idempotency-key <key> --json
```

Success exits `0`; usage errors exit `2`; generation and validation failures are non-zero. Parse the single JSON object from stdout and branch on `ok`, `state`, and `error.code`, not localized messages.

The Agent adds these transport errors without changing the c7f0632 executable:

- `generator_cli_not_found` (`127`)
- `generator_cli_launch_failed` (`126`)
- `generator_cli_empty_response`
- `generator_cli_invalid_response`

When the CLI returns valid JSON, preserve its object and exit code. Invoke the exe directly so PowerShell does not reinterpret `--session`, `--session-dir`, or other double-hyphen arguments through a wrapper script.

All paths must be explicit. Every Agent command passes `--protocol v2`; omission preserves the legacy v1 default and is not this Skill's workflow.
