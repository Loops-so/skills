# Loops Agent Skills

Installable agent skills for working with Loops.

This repository packages reusable skills for the `skills` CLI and the `skills.sh` ecosystem. It is intended to help coding agents integrate with Loops faster and more accurately, with guidance for the Loops API, official SDKs, and common server-side implementation patterns.

## Available Skills

| Skill | What it covers |
| --- | --- |
| [`api`](./skills/api/SKILL.md) | Use the Loops API, SDKs, and CLI to manage contacts, create contact properties, work with mailing lists, send events, validate credentials, and send transactional emails. Includes HTTP, SDK, and terminal workflows. |

## Install

```bash
# List the skills in this repo
npx skills add https://github.com/Loops-so/skills --list

# Install the API skill
npx skills add https://github.com/Loops-so/skills --skill api
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

## Local Development

Clone the repo, make changes, and validate that the skill is discoverable by the `skills` CLI:

```bash
git clone https://github.com/Loops-so/skills.git
cd skills
npx skills add . --list
```

If you are updating API guidance, check the live docs and OpenAPI spec before merging changes.

## Repository Structure

```text
skills/
  api/
    SKILL.md
    references/
```
