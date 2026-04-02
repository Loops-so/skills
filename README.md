# Loops Agent Skills

Official installable Loops skills for AI coding agents.

This repository packages `SKILL.md`-based skills for working with Loops. It is intended to help agents use the Loops API, official SDKs, the Loops CLI, and email-sending best practices more accurately.

These skills are designed for agent environments that support installable skills, such as Claude Code, Codex, Cursor, and other tools that work with the `skills` CLI.

## Install

For `skills` CLI setup, agent-specific install targets, and the full list of flags, use the official docs:

- https://skills.sh/docs/cli

This README only documents the Loops-specific install commands.

```bash
# See what this repo contains
npx skills add https://github.com/Loops-so/skills --list

# Install all Loops skills globally (recommended)
npx skills add https://github.com/Loops-so/skills --global

# Install specific Loops skills globally
npx skills add https://github.com/Loops-so/skills --global --skill api
npx skills add https://github.com/Loops-so/skills --global --skill cli
npx skills add https://github.com/Loops-so/skills --global --skill email-sending-best-practices
```

Project-level installs are also supported. Omit `--global` if you want the skills scoped to the current repository instead of user-level.

## Verify Install

After installation, try a task that should trigger one of the skills:

- "Add a contact to Loops from my Next.js backend."
- "Install the Loops CLI and authenticate against the right team."
- "Send a transactional email with the Loops CLI."
- "Audit this onboarding email flow for deliverability issues."

## What's Included

This repo currently ships:

- three installable skills that auto-load when relevant
- detailed reference files for API, SDK, CLI, and email-program guidance

This repo does not currently ship:

- slash commands
- MCP servers

## Available Skills

### `api`

Use this skill when you need to:

- create, update, find, or delete contacts
- create and manage custom contact properties
- read mailing lists and update mailing-list membership
- send Loops events from backend code
- send transactional emails with the API or SDKs
- choose between official SDKs and raw HTTP requests
- handle rate limits, idempotency keys, and server-side-only constraints

Example prompts:

- "Add this user to Loops after signup."
- "Check whether this Loops API key is valid."
- "Send this event from my Rails app."

Skill file: [skills/api/SKILL.md](./skills/api/SKILL.md)

### `cli`

Use this skill when you need to:

- install or update the Loops CLI
- authenticate from the terminal and manage stored team keys
- validate credentials with the CLI
- run one-off contact, list, event, or transactional-email commands
- inspect CLI output in text or JSON from the shell

Example prompts:

- "Install the Loops CLI on macOS."
- "Log into the CLI for the staging team."
- "Send a transactional email from the terminal."

Skill file: [skills/cli/SKILL.md](./skills/cli/SKILL.md)

### `email-sending-best-practices`

Use this skill when you need to:

- review inbox-placement or sender-reputation problems
- improve consent flows, list hygiene, or unsubscribe behavior
- improve subject lines, preview text, sender identity, or template design
- decide between lifecycle, campaign, and transactional email
- review a Loops setup for quality or deliverability gaps

Example prompts:

- "Review this welcome email for deliverability risk."
- "Should this message be transactional or marketing?"
- "How should we clean up this stale audience?"

Skill file: [skills/email-sending-best-practices/SKILL.md](./skills/email-sending-best-practices/SKILL.md)

## Stability Notes

- This repo is maintained by Loops.
- The Loops CLI has its own skill in this repo.
- The Loops CLI itself is still pre-release and may change faster than the API and SDK docs.

## Source Of Truth

This repo is the installable Loops entry point for agent skills. When product behavior changes faster than this repo, verify against the official Loops resources:

- Docs: https://loops.so/docs
- API reference: https://loops.so/docs/api-reference/intro
- JavaScript SDK: https://loops.so/docs/sdks/javascript
- OpenAPI spec: https://app.loops.so/openapi.json
- CLI repo: https://github.com/Loops-so/cli

## Contributing

Pull requests are welcome.

When updating a skill:

- keep `SKILL.md` concise and move detailed material into `references/`
- avoid duplicating the same guidance across `SKILL.md` and references
- verify product-specific details against official Loops sources
- re-run `npx skills add . --list` before merging

## Local Validation

```bash
git clone https://github.com/Loops-so/skills.git
cd skills
npx skills add . --list
```

## Repository Structure

```text
skills/
  api/
    SKILL.md
    references/
  cli/
    SKILL.md
    references/
  email-sending-best-practices/
    SKILL.md
    references/
```
