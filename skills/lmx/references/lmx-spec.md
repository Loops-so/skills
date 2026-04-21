# LMX Specification

Source URL: https://loops.so/docs/creating-emails/lmx

**LMX** (Loops Markup Language) is an XML-based rich-text format for email content. Every element is a PascalCase tag; there is no shorthand syntax.

---

## 1. Core Rules

1. **XML, not HTML.** Tags are case-sensitive PascalCase: `<Paragraph>`, not `<paragraph>` or `<P>`.
2. **Self-closing tags must use `/>`.** Example: `<Image src="..." />`, `<Br />`, `<Divider />`. Writing `<Image src="...">` without a close is invalid.
3. **Only the tags defined in this spec are valid.** Unknown tags are rejected.
4. **Unknown attributes are ignored.** Don't invent attributes — values outside the documented set have no effect.
5. **Required attributes must be present.** For example, `<Image>` without `src` is invalid.
6. **All attribute values are quoted strings.** Numbers and booleans are written as strings: `width="400"`, `notrack="true"`. Types are interpreted from the attribute definition.
7. **Text at the root is invalid.** Every piece of text must be inside a block tag. Top-level text, top-level variables, and top-level inline tags (`<Strong>`, `<Link>`, `<Br>`, etc.) are rejected.
8. **Whitespace between block tags is not rendered.** You can indent and line-break freely.
9. **Escape `<` and `&` in text** as `&lt;` and `&amp;`. Escape `"` and `&` in attribute values as `&quot;` and `&amp;`.
10. **A document is a sequence of top-level block elements**, optionally preceded by a single `<Style />` tag.

---

## 2. Top-Level Structure

A valid document contains zero or more of the following **top-level tags**:

```
H1, H2, H3, Paragraph, Quote, CodeBlock, Button, Image,
Divider, OrderedList, UnorderedList, Columns, ComponentContainer,
For, Icons, Style
```

Tags like `<ListItem>`, `<ColumnItem>`, `<Icon>`, `<Br>`, `<Strong>`, `<Em>`, `<Link>`, etc. are **not** valid at the top level — they only appear inside their container or inline context.

Example minimal document:

```xml
<H1>Welcome</H1>
<Paragraph>Hello world.</Paragraph>
```

---

## 3. Content Types And Nesting

Each tag declares what content it accepts:

| Content type | Meaning |
| --- | --- |
| `inline` | Text, `{variables}`, inline format tags (`<Strong>`, `<Em>`, …), `<Br>` |
| `text` | Plain text only — no inline tags, no `{variables}` (braces are literal) |
| `none` | No text at all — only specific element children, or self-closing |

Nesting summary:

- `<H1>`, `<H2>`, `<H3>`, `<Paragraph>`, `<Quote>`, `<ListItem>` → inline content
- `<CodeBlock>`, `<Button>` → text only (braces are literal inside these)
- `<OrderedList>`, `<UnorderedList>` → only `<ListItem>` children
- `<Columns>` → only `<ColumnItem>` children
- `<ColumnItem>`, `<ComponentContainer>`, `<For>` → block tags (same set as top-level, minus `<Style>`)
- `<Icons>` → only `<Icon />` children
- `<For>` → must contain at least one block child
- Self-closing with no children: `<Image />`, `<Divider />`, `<Br />`, `<Icon />`, `<Style />`

---

## 4. Tag Reference

### 4.1 Headings — `<H1>`, `<H2>`, `<H3>`

Inline content. Shared optional attributes on all text blocks:

| Attribute | Type | Notes |
| --- | --- | --- |
| `fontSize` | number | In pixels (e.g. `"24"`) |
| `lineHeight` | number | Percentage (e.g. `"150"` = 150%) |
| `align` | string | `"left"`, `"center"`, `"right"` |
| `blockColor` | hex | Background color of the block (e.g. `"#f8fafc"`) |
| `blockBorderRadius` | number | |
| `paddingTop` | number | |
| `paddingRight` | number | |
| `paddingBottom` | number | |
| `paddingLeft` | number | |

```xml
<H1>Plain heading</H1>
<H2 align="center" fontSize="28">Centered heading</H2>
<H3 blockColor="#f0f0f0" paddingTop="16">Heading on tinted background</H3>
```

### 4.2 `<Paragraph>`

Inline content. Same optional attributes as headings.

```xml
<Paragraph>Plain paragraph.</Paragraph>
<Paragraph align="center" fontSize="18">Centered bigger paragraph.</Paragraph>
<Paragraph>Mixed <Strong>bold</Strong> and <Em>italic</Em> and <Link href="https://loops.so">a link</Link>.</Paragraph>
```

### 4.3 `<Quote>`

Inline content. Same optional attributes as headings.

```xml
<Quote>A short quotation.</Quote>
<Quote fontSize="16">Quoted <Em>italic</Em> text.</Quote>
```

### 4.4 `<CodeBlock>`

**Text only.** No inline tags. `{foo}` inside is a literal string, not a variable.

Attributes: `fontSize`, `lineHeight`, `blockColor`, `blockBorderRadius`, padding attrs.

```xml
<CodeBlock>const x = 1;</CodeBlock>
```

### 4.5 `<Button>`

**Text only** (same rule as `<CodeBlock>` — `{foo}` is literal).

| Attribute | Type | Notes |
| --- | --- | --- |
| `href` | url | |
| `bgColor` | hex | |
| `textColor` | hex | |
| `borderRadius` | number | |
| `borderWidth` | number | |
| `borderColor` | hex | |
| `innerXPadding` | number | |
| `innerYPadding` | number | |
| `fontSize` | number | |
| `size` | string | `"small"`, `"medium"`, `"large"` |
| `align` | string | `"left"`, `"center"`, `"right"` |
| `notrack` | boolean | `"true"` disables link tracking |
| `paddingTop`, etc. | number | Block padding |

```xml
<Button href="https://loops.so" bgColor="#000" textColor="#fff" borderRadius="12">Click me</Button>
<Button href="https://example.com" size="large" align="center">Big centered CTA</Button>
```

### 4.6 `<Image />` (self-closing)

| Attribute | Type | Required | Notes |
| --- | --- | :---: | --- |
| `src` | url | yes | Image URL |
| `alt` | string | | Alt text |
| `href` | url | | Wraps image in a link |
| `width` | number | | In pixels |
| `align` | string | | `"left"`, `"center"`, `"right"` |
| `borderRadius` | number | | |
| `borderWidth` | number | | |
| `borderColor` | hex | | |
| `notrack` | boolean | | |
| `blockColor`, etc. | | | Block background / padding attrs |

```xml
<Image src="https://example.com/photo.png" alt="A photo" width="400" />
<Image src="https://example.com/logo.png" href="https://loops.so" align="center" />
```

### 4.7 `<Divider />` (self-closing)

| Attribute | Type | Notes |
| --- | --- | --- |
| `align` | string | `"left"`, `"center"`, `"right"` |
| `width` | number | Percentage (e.g. `"80"` = 80%) |
| `borderWidth` | number | |
| `color` | hex | |
| `size` | string | `"small"`, `"medium"`, `"large"` |

```xml
<Divider />
<Divider width="80" color="#ccc" size="small" />
```

### 4.8 `<Br />` (self-closing)

Line break inside an inline context. Never at the top level.

```xml
<Paragraph>Line one<Br />Line two</Paragraph>
```

### 4.9 `<OrderedList>` / `<UnorderedList>`

Children: `<ListItem>` only. No other content.

| Attribute | Type | Notes |
| --- | --- | --- |
| `start` | number | Ordered list only — starting number |
| `align` | string | `"left"`, `"center"`, `"right"` |

```xml
<OrderedList start="3">
  <ListItem>First (labeled 3)</ListItem>
  <ListItem>Second (labeled 4)</ListItem>
</OrderedList>

<UnorderedList>
  <ListItem>Bullet with <Em>italic</Em></ListItem>
  <ListItem>Bullet with <Strong>bold</Strong></ListItem>
</UnorderedList>
```

### 4.10 `<ListItem>`

Inline content. Only valid inside `<OrderedList>` or `<UnorderedList>`.

Attributes: `fontSize`, `lineHeight`, `blockColor`, `blockBorderRadius`, padding attrs.

### 4.11 `<Columns>` + `<ColumnItem>`

`<Columns>` children must be `<ColumnItem>`. `<ColumnItem>` children are block tags (same set as top-level, except `<Style>`).

`<Columns>` attributes:

| Attribute | Type | Notes |
| --- | --- | --- |
| `gap` | number | Space between columns (px) |
| `widths` | string | Comma-separated percentages, e.g. `"40,60"` or `"33,33,34"` |
| `verticalAlignment` | string | `"top"`, `"middle"`, `"bottom"` |
| `stackOnMobile` | boolean | |
| `reverseOnMobile` | boolean | |
| block style attrs | | `blockColor`, `blockBorderRadius`, padding |

`<ColumnItem>` takes no attributes.

```xml
<Columns gap="24" widths="40,60" verticalAlignment="middle">
  <ColumnItem>
    <H3>Left</H3>
    <Paragraph>Left body.</Paragraph>
  </ColumnItem>
  <ColumnItem>
    <Image src="https://example.com/pic.png" />
  </ColumnItem>
</Columns>
```

### 4.12 `<ComponentContainer>`

Wraps a reusable email component. Children are block tags (no `<Style>`, no nested `<ComponentContainer>`).

| Attribute | Type | Notes |
| --- | --- | --- |
| `componentId` | string | Reference to a saved component |
| `id` | string | Per-instance container id |
| block style attrs | | `blockColor`, `blockBorderRadius`, padding |

```xml
<ComponentContainer componentId="cmp_123" id="ctr_1">
  <Paragraph>Header content</Paragraph>
</ComponentContainer>
```

### 4.13 `<For>`

Loops over an array variable. Must contain at least one block child.

| Attribute | Type | Notes |
| --- | --- | --- |
| `variable` | variable expression | `"{items}"`, `"{data.products}"`, or `"{event.items}"` — braces required |

```xml
<For variable="{data.products}">
  <Paragraph>Product name</Paragraph>
</For>
```

### 4.14 `<Icons>` + `<Icon />`

Social / inline icon row. Children are `<Icon />` only.

`<Icons>` attributes:

| Attribute | Type | Notes |
| --- | --- | --- |
| `align` | string | `"left"`, `"center"`, `"right"` |
| `gap` | number | Space between icons (px) |
| `size` | number | Icon size (px) |
| `color` | hex | Default icon color |
| block style attrs | | `blockColor`, `blockBorderRadius`, padding |

`<Icon />` attributes:

| Attribute | Type | Required | Notes |
| --- | --- | :---: | --- |
| `name` | string | yes | Icon name (e.g. `"twitter"`) |
| `href` | url | | |
| `color` | hex | | Overrides parent `<Icons>` color |
| `notrack` | boolean | | |

```xml
<Icons align="center" gap="20" size="24" color="#334155">
  <Icon name="twitter" href="https://x.com/loops" />
  <Icon name="github" href="https://github.com/loops" />
</Icons>
```

### 4.15 `<Style />` (self-closing, top-level metadata)

At most one per document. Must appear before all other tags. Does not produce rendered content — it sets document-level styling. All attributes are optional.

| Attribute | Type |
| --- | --- |
| `styleTemplateId` | string |
| `backgroundColor`, `backgroundXPadding`, `backgroundYPadding` | hex / number |
| `bodyColor`, `bodyXPadding`, `bodyYPadding`, `bodyFontFamily`, `bodyFontCategory` | mixed |
| `borderColor`, `borderWidth`, `borderRadius` | mixed |
| `buttonBodyColor`, `buttonBodyXPadding`, `buttonBodyYPadding` | mixed |
| `buttonBorderColor`, `buttonBorderWidth`, `buttonBorderRadius` | mixed |
| `buttonTextColor`, `buttonTextFontSize` | mixed |
| `dividerColor`, `dividerBorderWidth` | mixed |
| `textBaseColor`, `textBaseFontSize`, `textBaseLineHeight`, `textBaseLetterSpacing` | mixed |
| `textLinkColor` | hex |
| `heading1Color`, `heading1FontSize`, `heading1LineHeight`, `heading1LetterSpacing` | mixed |
| `heading2Color`, `heading2FontSize`, `heading2LineHeight`, `heading2LetterSpacing` | mixed |
| `heading3Color`, `heading3FontSize`, `heading3LineHeight`, `heading3LetterSpacing` | mixed |

```xml
<Style styleTemplateId="st_123" bodyColor="#ffffff" bodyYPadding="12" />
<Paragraph>Hello</Paragraph>
```

---

## 5. Inline Tags

Inline tags live inside `<H1>`/`<H2>`/`<H3>`/`<Paragraph>`/`<Quote>`/`<ListItem>` and inside one another (they compose). They are **never** valid at the top level.

| Tag | Effect |
| --- | --- |
| `<Strong>…</Strong>` | Bold |
| `<Em>…</Em>` | Italic |
| `<Underline>…</Underline>` | Underline |
| `<Strike>…</Strike>` | Strikethrough |
| `<Code>…</Code>` | Inline code format |
| `<Text>…</Text>` | No format — carrier for `textColor` only |
| `<Link href="…">…</Link>` | Hyperlink |

All format tags (and `<Text>`) accept an optional `textColor` hex attribute (`"#f00"` or `"#ff0000"`). Non-hex values are rejected.

`<Link>` attributes:

| Attribute | Type | Required | Notes |
| --- | --- | :---: | --- |
| `href` | url | yes | |
| `notrack` | boolean | | `"true"` disables tracking |

```xml
<Paragraph>Mixed <Strong>bold</Strong>, <Em>italic</Em>, <Strong><Em>both</Em></Strong>.</Paragraph>
<Paragraph>A <Link href="https://example.com">link</Link> and <Link href="https://x.com" notrack="true">an untracked link</Link>.</Paragraph>
<Paragraph>Color only: <Text textColor="#f00">red</Text>. Colored bold: <Strong textColor="#00f">blue bold</Strong>.</Paragraph>
```

Inner `textColor` wins when nested. Example: `<Text textColor="#f00"><Strong textColor="#00f">x</Strong></Text>` → `x` is bold + blue.

---

## 6. Variables

Variables are written as `{name}` **inside text content** of inline-content tags (`<Paragraph>`, headings, `<Quote>`, `<ListItem>`, and inside inline tags). They are **not** valid:

- Inside `<CodeBlock>` or `<Button>` (text-only — braces are literal there)
- As attribute values (except `<For variable="…">`)
- At the top level (wrap in `<Paragraph>` first)

Three kinds, disambiguated by prefix:

| Syntax | Kind | Meaning |
| --- | --- | --- |
| `{firstName}` | merge tag | Contact property (bare name), used in campaigns and loop emails |
| `{contact.email}` | merge tag | Contact property (explicit `contact.` prefix), used in campaigns and loop emails |
| `{data.order.id}` | data variable | Transactional email data variable, only available in transactional emails |
| `{event.type}` | event property | Event-trigger property, used only in loops emails that are triggered by events |

Examples:

```xml
<Paragraph>Hello {firstName}!</Paragraph>
<Paragraph>Order {evemt.orderId} shipped to {contact.email}.</Paragraph>
<Paragraph>Triggered by {event.eventName}.</Paragraph>
<Paragraph>Click <Link href="https://example.com">{firstName}'s receipt</Link>.</Paragraph>
```

Inside `<For variable="…">`, the variable expression must include braces and must be a single expression:

```xml
<For variable="{data.products}">
  <Paragraph>A product.</Paragraph>
</For>
```

---

## 7. Common Mistakes

| Mistake | Fix |
| --- | --- |
| `<paragraph>`, `<p>`, `<P>` | `<Paragraph>` |
| `<Image src="x.png">` (no self-close) | `<Image src="x.png" />` |
| Plain text at top level | Wrap in `<Paragraph>…</Paragraph>` |
| `<Strong>` at top level | Wrap in `<Paragraph><Strong>…</Strong></Paragraph>` |
| `<ListItem>` outside a list | Nest in `<UnorderedList>` or `<OrderedList>` |
| `<ColumnItem>` outside `<Columns>` | Wrap in `<Columns>…</Columns>` |
| `<Icon />` outside `<Icons>` | Wrap in `<Icons>…</Icons>` |
| `{firstName}` inside `<CodeBlock>` / `<Button>` | These are text-only — braces are literal |
| `textColor="red"` | Use hex: `textColor="#f00"` or `textColor="#ff0000"` |
| `<For variable="data.products">` | Braces required: `<For variable="{data.products}">` |
| `<For>` with only text | Must contain at least one block tag, e.g. `<Paragraph>` |
| Two `<Style />` tags | Only one `<Style />` per document |
| Unescaped `<` or `&` in text | Use `&lt;` and `&amp;` |

---

## 8. Full Example Document

```xml
<Style styleTemplateId="st_123" bodyColor="#ffffff" backgroundColor="#f1f5f9" bodyYPadding="16" />
<H1>Welcome, {firstName}</H1>
<Paragraph fontSize="18" lineHeight="150">
  Thanks for signing up on {signupDate}. Here's what <Strong>you</Strong>
  can do next:
</Paragraph>
<UnorderedList>
  <ListItem>Read <Link href="https://loops.so/docs">the docs</Link></ListItem>
  <ListItem><Strong>Invite</Strong> your team</ListItem>
  <ListItem>Explore <Em>integrations</Em></ListItem>
</UnorderedList>
<Button href="https://loops.so/start" size="large" align="center" bgColor="#0f172a" textColor="#ffffff" borderRadius="12">Get started</Button>
<Divider align="center" width="80" size="small" color="#cbd5e1" />
<Columns gap="24" widths="50,50" verticalAlignment="top">
  <ColumnItem>
    <H3>Docs</H3>
    <Paragraph>Start with the quickstart.</Paragraph>
  </ColumnItem>
  <ColumnItem>
    <H3>Community</H3>
    <Paragraph>Join other builders.</Paragraph>
  </ColumnItem>
</Columns>
<For variable="{data.products}">
  <Paragraph>Featured product — see it <Link href="https://example.com">here</Link>.</Paragraph>
</For>
<Icons align="center" gap="20" size="24" color="#334155">
  <Icon name="twitter" href="https://x.com/loops" />
  <Icon name="github" href="https://github.com/loops" />
</Icons>
<Quote fontSize="16">"Loops made our lifecycle emails effortless." — {contact.email}</Quote>
<CodeBlock>curl -X POST https://app.loops.so/api/v1/events</CodeBlock>
```
