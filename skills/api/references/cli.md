# Loops CLI Reference

## Source URLs

- https://github.com/Loops-so/cli
- https://github.com/Loops-so/cli/blob/main/README.md

The Loops CLI README explicitly says this is pre-release alpha software and that `loops --help` should be treated as the source of truth for exact features and flags.

## When To Use The CLI

Prefer the CLI when the user is:

- working from the shell
- doing one-off operational tasks
- validating or rotating credentials
- troubleshooting requests without writing application code
- inspecting output in text or JSON locally

Prefer SDKs or raw HTTP for application code.

## Build And Install

Current README guidance:

```bash
git clone https://github.com/Loops-so/cli.git
cd cli
go build -o ./loops .
```

Use the Go version from the CLI repo `go.mod`. At current HEAD that is `1.26.1`.

Move the built `./loops` binary into your `PATH` if needed.

## Auth And Configuration

The CLI needs a Loops API key. You can either:

- set `LOOPS_API_KEY` in the environment, or
- store named keys with `loops auth ...`

### Common auth flows

```bash
# Save a key interactively and verify it
loops auth login --name my-team

# Save a key without verifying first
loops auth login --name my-team --skip-verify

# List stored keys
loops auth list

# Set the active stored key
loops auth use my-team

# Clear the active stored key
loops auth use --clear

# Show resolved config, active key, masked API key, team, and endpoint
loops auth status

# Remove a stored key
loops auth logout --name my-team
```

### Global flags

These apply across the CLI:

- `-o, --output text|json`
- `-t, --team <name>` to select a stored team key explicitly
- `--debug` to print API request details before sending

## Common Workflows

### Validate credentials

```bash
loops api-key
loops api-key --output json
```

Use this for a quick API-key check against the resolved config.

### Contacts

```bash
# Find
loops contacts find --email user@example.com
loops contacts find --user-id usr_123 --output json

# Create
loops contacts create \
  --email user@example.com \
  --first-name Alex \
  --last-name Chen \
  --user-id usr_123 \
  --subscribed true \
  --user-group premium \
  --list cm_abc123=true \
  --contact-props ./contact-props.json

# Update
loops contacts update \
  --email user@example.com \
  --first-name Alex \
  --list cm_abc123=false \
  --contact-props ./contact-props.json

# Delete
loops contacts delete --email user@example.com
```

Useful contact flags:

- `--email`
- `--user-id`
- `--first-name`
- `--last-name`
- `--subscribed true|false`
- `--user-group`
- `--list id=true|false` (repeatable)
- `--contact-props path/to/file.json`

### Contact properties

```bash
loops contact-properties list
loops contact-properties list --custom
loops contact-properties create --name planTier --type string
```

Use camelCase for property names.

### Mailing lists

```bash
loops lists list
loops lists list --output json
```

### Events

```bash
loops events send \
  --event signup \
  --email user@example.com \
  --props ./event-props.json \
  --contact-props ./contact-props.json \
  --list list_123=true \
  --idempotency-key signup-user-123
```

Useful event flags:

- `--event`
- `--email` or `--user-id`
- `--props path/to/file.json`
- `--contact-props path/to/file.json`
- `--list id=true|false`
- `--idempotency-key`

If the JSON file passed to `--props` contains a top-level `eventProperties` object, the CLI uses that nested object. Otherwise it treats the full JSON object as the event-properties payload.

### Transactional emails

```bash
# List published transactional emails
loops transactional list
loops transactional list --per-page 20
loops transactional list --cursor abc123 --output json

# Send
loops transactional send \
  --id cll42l54f20i1la0lfooe3z12 \
  --email user@example.com \
  -v firstName=Alex \
  -v resetLink=https://example.com/reset \
  --idempotency-key reset-user-123

# Send with JSON vars and attachments
loops transactional send \
  --id cll42l54f20i1la0lfooe3z12 \
  --email user@example.com \
  -j ./vars.json \
  -A ./invoice.pdf \
  -a
```

Useful transactional flags:

- `--id`
- `--email`
- `-a, --add-to-audience`
- `-v, --var KEY=value` (repeatable)
- `-j, --json-vars path/to/file.json`
- `-A, --attachment path/to/file` (repeatable)
- `--idempotency-key`
- `--per-page`
- `--cursor`

The CLI also exposes a top-level alias:

```bash
loops send --id cll42l54f20i1la0lfooe3z12 --email user@example.com -v resetLink=https://example.com/reset
```

This is the same transactional send flow as `loops transactional send`.

## Practical Guidance

- Use `--output json` when the CLI output needs to feed another program.
- Use named keys plus `loops auth use` when switching between Loops teams regularly.
- Use `--team` when you want to override the active stored key for a single command.
- Use `--debug` when you need to inspect request behavior from the terminal.
- If the user needs an exact flag or command not covered here, fall back to `loops --help` and the command-specific help output.
