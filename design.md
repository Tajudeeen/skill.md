<!--
  DESIGN.MD — web3-senior-engineer-auditor skill
  Aesthetic: Ambit #f5f3ec / #d8ff4f / Georgia serif / field-of-black
-->
# Design System — web3-senior-engineer-auditor

## 0. Artifact posture

This is not a "document." It is a **reference artifact** for builders who write
smart contracts, dApps, and on-chain agents. It sits at the intersection of a
security audit checklist and a senior engineer's field manual. It must scan
like a datasheet but read like annotated source.

## 1. Color field

```text
field      #0a0a0a  — page ground
paper      #f5f3ec  — body text, warm off-white
lime       #d8ff4f  — accents, inline code, risk highlights
charcoal   #1a1a1b  — card fill
slate      #2a2a2c  — borders, dividers
```

No grays between. No blue. No "brand." This is a dark terminal and a lime
cursor.

## 2. Typography stack

Primary: Georgia, serif — body, headers, quotes.
Secondary: JetBrains Mono — code blocks, inline `code`, tables.

Hierarchy is set by weight and spacing, not size storms.

```text
h1  32px / 1.2  Georgia Bold    paper      margin 2rem 0 1rem
h2  22px / 1.3  Georgia Bold    paper      margin 1.5rem 0 0.75rem
h3  18px / 1.3  Georgia Bold    lime       margin 1rem 0 0.5rem
body  16px / 1.7  Georgia Regular  paper     line 1.7
code  14px / 1.4  JetBrains Mono   lime      padding 0.1rem 0.3rem
```

## 3. Layout: the floating card

Ambit uses offset layers. In this markdown context, that translates to:

- Cards are signaled by `> ` blockquote with a leading `> ` empty line, or by
  fenced sections with a `> ` top rule.
- Inline risk callouts use `> ⚠️`.
- Inline success/nips use `> ✓`.

Example:

```markdown
> ⚠️ This contract is upgradeable. The admin key
> `0x000...dead` controls the proxy. Treat it as
> a hot wallet with full mint/burn power.
```

## 4. Inline code treatment

Inline code is `lime on charcoal`. Code blocks are `charcoal` with a `slate`
top rule and the standard four-backtick fence.

```text
`function foo()`  →  lime on charcoal, 0.2rem border-radius
```

## 5. Blockquote dialect

Used for three things only:

1.  **Risk notes** — `> ⚠️`
2.  **Success/confirmation** — `> ✓`
3.  **Field observations** — `> —` (em-dash lead, italic Georgia)

No plain blockquotes. Every `> ` carries a meaning token.

## 6. Tables

No full-width tables. Ambit tables are narrow — they fit 8 columns of content
max. Use them for matrices, not prose.

| Layer        | Trust source        | On-chain? |
|--------------|---------------------|-----------|
| Frontend     | Wallet / RPC        | off       |
| Backend      | Signer / keeper key | off       |
| Attestation  | Registry contract   | on        |

## 7. Section markers

Each major section begins with a horizontal rule made of spaced dots — not a
solid line:

```
⋯ ⋯ ⋯ ⋯ ⋯ ⋯ ⋯ ⋯ ⋯ ⋯ ⋯ ⋯ ⋯ ⋯ ⋯ ⋯ ⋯ ⋯ ⋯ ⋯ ⋯ ⋯ ⋯ ⋯
```

This is Ambit's way of saying "layer break" without a harsh line.
