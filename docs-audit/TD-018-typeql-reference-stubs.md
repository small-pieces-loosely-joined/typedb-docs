# TD-018 — Incomplete "Coming soon" TypeQL Reference pages

- **Severity:** P3 (user-facing incomplete reference content)
- **Status:** Findings & recommendations only — **needs SME authoring to fix**
- **Source:** Second-pass `typedb-docs` audit, 10 Sep 2026
- **Type:** This document lays out the finding and recommendation; it is **not** a fix. It lives in
  `docs-audit/`, outside any Antora module, so it does not affect the built site.

## What a reader sees

Several TypeQL Reference pages render as empty stubs whose entire body is a "Coming soon" admonition.
A reader who navigates or links to one of these gets no reference information.

## Evidence — stub pages (body is essentially "Coming soon")

| Page | Non-empty lines |
|------|-----------------|
| `typeql-reference/…/statements/alias.adoc` | 5 |
| `typeql-reference/…/statements/struct.adoc` | 5 |
| `typeql-reference/…/value-types/lists.adoc` | 6 |
| `typeql-reference/…/expressions/lists.adoc` | 7 |
| `typeql-reference/…/expressions/structs.adoc` | 7 |
| `typeql-reference/…/annotations/subkey.adoc` | 10 |
| `typeql-reference/…/annotations/cascade.adoc` | 17 |
| `typeql-reference/…/annotations/distinct.adoc` | 26 |

## Evidence — pages with "Coming soon" subsections (otherwise substantial)

`annotations/doc.adoc`, `annotations/meta.adoc`, `data-model.adoc`, `statements/index.adoc`,
`keywords.adoc`, `typeql-reference/index.adoc` contain one or more "Coming soon" markers inside
otherwise-complete pages (partial coverage rather than full stubs).

Related: `guides/…/typeql/insert-update-data.adoc:211` states *"There is a planned `@cascade`
annotation coming soon in TypeDB,"* consistent with the `annotations/cascade.adoc` stub.

## Impact

The TypeQL Reference is the canonical language spec; empty pages for core surfaces (lists, structs)
undercut its authority and hurt SEO/answer-engine retrieval for those keywords.

## Why this PR does not change the docs directly

Filling these pages is authoring work that requires confirming, per page, whether the feature has
shipped in the current release (3.13.x) and documenting exact syntax and semantics. Guessing grammar
on the canonical language reference would be worse than an honest "Coming soon". This note routes the
work to an SME.

## Recommendation (prioritized)

1. **Confirm shipped status in 3.13.x**, especially **lists** (`value-types/lists`, `expressions/lists`)
   and **structs** (`statements/struct`, `expressions/structs`) — these read as core value/expression
   features likely already implemented. If shipped, author full pages; if not, keep the stub but add a
   short "planned / not yet available" line so the page isn't blank.
2. **Annotations** (`@subkey`, `@cascade`, `@distinct`): document if implemented, otherwise mark
   explicitly as planned and keep out of primary flows.
3. **`statements/alias`**: confirm whether `alias` exists as a statement; if deprecated/removed,
   remove the page and its nav/xref entries instead of leaving a stub.
4. For the partial pages, replace each in-page "Coming soon" with the missing content or a dated
   "planned" note.

Once SMEs confirm which features are shipped, these can be authored in a normal content PR and this
note removed. Happy to draft any specific page on confirmation that its feature is implemented.
