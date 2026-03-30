---
name: api
description: >
  Use this skill whenever the user wants to work with Loops programmatically or
  from the terminal. This includes the Loops API, official SDKs, and the Loops
  CLI for sending events, managing contacts, working with contact properties
  and mailing lists, validating API keys, and sending transactional emails.
  Trigger on phrases like "Loops API", "Loops SDK", "Loops CLI", "loops auth
  login", "loops contacts create", "loops contacts update", "loops events
  send", "loops transactional send", "send a Loops event", "add a contact to
  Loops", "send a transactional email", or any time the user wants to
  integrate Loops into their app, backend, automation, or shell workflow.
metadata:
  version: 1.1.0
---

# Loops API, SDK, and CLI Skill

This skill helps with Loops implementation and operational workflows. Use it for application integrations, local terminal tasks, and exact request or command guidance.

## When To Use

Use this skill when the user needs to:

- integrate Loops into an app, backend, webhook, or automation
- decide between the Loops CLI, SDKs, or raw HTTP API
- manage contacts, contact properties, mailing lists, events, or transactional email
- validate credentials or troubleshoot Loops requests from the terminal

This skill is for implementation and operational usage, not broad email strategy or deliverability review.

## Working Style

When this skill is active:

1. Choose the right interface first: CLI, SDK, or raw HTTP.
2. Prefer the CLI for local admin tasks, one-off sends, troubleshooting, and shell workflows.
3. Prefer official SDKs for application code when the language has one.
4. Prefer raw HTTP only when no SDK is available or the user needs exact payload control.
5. Keep Loops requests server-side.
6. Verify exact behavior against the official docs, OpenAPI spec, or `loops --help` when details matter.

Official references:

- Docs: `https://loops.so/docs`
- API reference: `https://loops.so/docs/api-reference/intro`
- JavaScript SDK: `https://loops.so/docs/sdks/javascript`
- OpenAPI spec: `https://app.loops.so/openapi.json`
- CLI repo: `https://github.com/Loops-so/cli`

## Choose The Interface

- CLI:
  - local shell usage
  - ad hoc contact or event operations
  - credential validation
  - quick troubleshooting
  Read `references/cli.md`
- SDK or HTTP API:
  - application code
  - backend services
  - webhook handlers
  - repeatable integrations
  Read `references/http-api.md`

## Category Routing

- Auth, base URL, rate limits, contacts, properties, lists, events, transactional payloads, SDK examples, and HTTP errors:
  Read `references/http-api.md`
- Building the Loops CLI, auth flows, JSON output, named keys, shell examples, and common command workflows:
  Read `references/cli.md`

## Output Checklist

Aim to leave the user with:

- the right interface choice for the task
- exact commands or payload shapes
- any Loops-specific caveats that affect behavior
- the next validation step, such as `loops api-key`, `--help`, or a small test request
