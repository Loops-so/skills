# Loops Agent Skills

Installable agent skills for working with Loops.

This repository packages reusable skills for the `skills` CLI and the `skills.sh` ecosystem. It is intended to help coding agents integrate with Loops faster and more accurately, with guidance for the Loops API, official SDKs, common server-side implementation patterns, and email-sending best practices.

## Available Skills

| Skill | What it covers |
| --- | --- |
| [`api`](./skills/api/SKILL.md) | Use the Loops API, SDKs, and CLI to manage contacts, create contact properties, work with mailing lists, send events, validate credentials, and send transactional emails. Includes HTTP, SDK, and terminal workflows. |
| [`email-sending-best-practices`](./skills/email-sending-best-practices/SKILL.md) | Review and improve email sending quality across deliverability, consent, design, lifecycle strategy, and transactional-vs-marketing decisions. Generic by default, with Loops-specific caveats and defaults. |

## Install

Install our skills using [vercel-labs/skills](https://github.com/vercel-labs/skills). Below are suggested installation commands for the happy-path. Consult [Vercel's documentation](https://github.com/vercel-labs/skills?tab=readme-ov-file#options) for the comprehensive set of installation options.

### List the skills in this repo
```bash
npx skills add https://github.com/Loops-so/skills --list
```

### Install the API skill
```bash
npx skills add https://github.com/Loops-so/skills --skill api --global
```

### Install the email best-practices skill
```bash
npx skills add https://github.com/Loops-so/skills --skill email-sending-best-practices --global
```

## When To Use The `api` Skill

Use the `api` skill when you need to:

- Authenticate or troubleshoot the Loops CLI from the terminal
- Create, update, find, or delete contacts in Loops
- Create and manage custom contact properties
- Read mailing lists and update mailing-list membership
- Trigger Loops events from your app or backend workflows
- Send transactional emails from server-side code
- Choose between the Loops CLI, official SDKs, and raw HTTP requests
- Handle rate limits, idempotency keys, and server-side-only constraints

## Source Of Truth

This repo is a convenience layer for agents. For the latest API behavior and schema, verify against the official Loops resources:

- Docs: https://loops.so/docs
- API reference: https://loops.so/docs/api-reference/intro
- JavaScript SDK: https://loops.so/docs/sdks/javascript
- OpenAPI spec: https://app.loops.so/openapi.json
- CLI repo: https://github.com/Loops-so/cli

## Local Development

Clone the repo, make changes, and validate that the skill is discoverable by the `skills` CLI:

```bash
git clone https://github.com/Loops-so/skills.git
cd skills
npx skills add . --list
```

If you are updating API guidance, check the live docs, OpenAPI spec, and CLI repo before merging changes.

## Repository Structure

```text
skills/
  api/
    SKILL.md
    references/
  email-sending-best-practices/
    SKILL.md
    references/
```
