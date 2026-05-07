---
name: loops-lmx
description: >
  Use this skill whenever the user wants to create, write, generate, or edit
  email content in Loops. This includes composing campaigns, loops, lifecycle
  emails, transactional email bodies, or any email template for the Loops
  editor or API. LMX (Loops Markup Language) is the format used for all Loops
  email content. Trigger on phrases like "create a campaign", "generate an
  email", "write a welcome email", "draft a lifecycle email", "build an email
  template", "write a transactional email body", "create an onboarding email",
  "LMX", "Loops email", or any request to produce or modify email body content
  intended for Loops. Do not trigger for questions about the Loops HTTP API,
  SDK integration, or CLI unless email body content is also involved.
metadata:
  version: 1.2.0
---

# LMX Skill

This skill helps write, review, and generate correct LMX email markup for Loops. LMX is an XML-based format: every element is a PascalCase tag, self-closing tags require `/>`, and only the tags in the spec are valid.

## When To Use

Use this skill when the task involves:

- generating or editing LMX email content
- reviewing LMX markup for correctness
- choosing the right LMX tags or attributes for a layout
- applying design guidelines to an LMX document
- explaining how a specific LMX tag or attribute works

## Working Style

When this skill is active:

1. Read `references/lmx-spec.md` for the full tag and attribute reference. It is authoritative — do not invent tags or attributes.
2. Read `references/lmx-design-guidelines.md` for Loops design guidelines. Apply these to every document you generate unless the user explicitly overrides a rule.
3. Validate nesting and content-type rules before producing output (see spec section 3).
4. Check the common-mistakes table in the spec before finalizing output.
5. Always produce a complete, valid document — not fragments, unless the user specifically asks for one.

## Category Routing

- Tag definitions, required/optional attributes, nesting rules, content types, variable syntax, self-closing requirements, or escaping:
  Read `references/lmx-spec.md`

- Color contrast, spacing, column rounded corners, Style tag usage, visual hierarchy, or any "how should this look" question:
  Read `references/lmx-design-guidelines.md`

## Output Checklist

Before returning any LMX output, verify:

- [ ] All tags are PascalCase and in the allowed set
- [ ] All self-closing tags use `/>` (e.g. `<Image />`, `<Divider />`, `<Br />`, `<Icon />`, `<Style />`)
- [ ] No text or inline tags at the top level
- [ ] Variables use explicit LMX namespaces: `{contact.name}` or `{system.name}`
- [ ] Variables only appear where supported: inline content, button text, and supported dynamic attrs (`Button.href`, `Image.alt`, `Image.href`, `Image.dynamicSrc`, `Link.href`, `Section.href`)
- [ ] No inline fallback syntax is invented; fallbacks live outside the LMX string
- [ ] `<Button>` text has no inline tags, but can contain variables; `<CodeBlock>` treats braces literally
- [ ] `<Style />` appears at most once as a top-level tag; put it first in generated output
- [ ] Body/background colors are intentional: supplied by `themeId` or explicit `bodyColor`/`backgroundColor`
- [ ] No same-color-on-same-color situations (check text vs block color, icon color vs background, etc.)
- [ ] Sufficient Y-spacing on block elements
- [ ] Current tag names are used: `<Component>`, not `<ComponentContainer>`; `themeId`, not `styleTemplateId`
- [ ] `<Columns>` has exactly two `<ColumnItem>` children
- [ ] Dynamic images use static `src` plus `dynamicSrc`, not variables in `src`
- [ ] `<Icons color>` uses one of `#000000`, `#808080`, or `#FFFFFF`; `<Icon>` has no `color` attr
- [ ] Loops appends an email footer automatically; do not add manual unsubscribe/address footer in generated LMX unless explicitly requested
