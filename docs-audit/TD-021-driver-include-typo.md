# TD-021 — Broken driver-API includes surfacing on the gRPC driver reference

- **Severity:** P3 (missing reference content on published driver pages)
- **Status:** Findings & recommendations only — **root cause is upstream in `typedb/typedb-driver`**
- **Source:** Second-pass `typedb-docs` audit, 10 Sep 2026
- **Type:** This document lays out the finding and recommendation; it is **not** a fix. It lives in
  `docs-audit/`, outside any Antora module, so it does not affect the built site. The fix is **not**
  in this repository.

## What a reader sees

Sections of the gRPC driver reference (`reference/…/typedb-grpc-drivers/{python,c}.adoc`) are missing
content because `include::` directives in the driver-supplied partials fail to resolve at build time.

## Evidence (build log)

`external-typedb-driver` partials pulled from `github.com/typedb/typedb-driver@master` produce:

```
target of include not found: 3.x@external-typeb-driver::partial$python/connection/Database.adoc
   (docs/modules/ROOT/partials/python/api-reference.adoc:14)
target of include not found: 3.x@external-typedb-driver::partial$c/answer/conceptrow.adoc
target of include not found: 3.x@external-typedb-driver::partial$c/answer/conceptpromise.adoc
id assigned to section already in use: _Kind | _Value | _ReduceStage | _SortStage
   (docs/modules/ROOT/partials/python/analyze/*.adoc)
```

Key detail: the Python failure is a **typo** — `external-type` **`b`** `-driver` (missing the `d`),
so the include target never resolves. Confirmed via `git grep` that this typo is **not** present in
`typedb-docs`; it lives in the `typedb-driver` repo's
`docs/modules/ROOT/partials/python/api-reference.adoc:14`.

## Impact

- The Python driver reference is missing the `Database` connection partial.
- The C driver reference is missing `conceptrow` / `conceptpromise` partials.
- Duplicate auto-generated section IDs (`_Kind`, `_Value`, `_ReduceStage`, `_SortStage`) create
  ambiguous anchors on the Python page.

## Why this PR does not change the docs directly

`typedb-docs` only *includes* these partials; the broken directives and duplicate IDs are generated
in and shipped from `typedb/typedb-driver`. There is nothing to correct in this repository.

## Recommendation (for the `typedb-driver` maintainers)

1. Fix the typo `external-type`**`b`**`-driver` → `external-typedb-driver` in
   `docs/modules/ROOT/partials/python/api-reference.adoc:14`, and confirm
   `partial$python/connection/Database.adoc` exists / is generated.
2. Restore the missing C partials `partial$c/answer/conceptrow.adoc` and
   `partial$c/answer/conceptpromise.adoc` (or update the includes that reference them).
3. De-duplicate the section IDs generated for `Kind`, `Value`, `ReduceStage.ReduceAssignment`, and
   `SortStage.SortVariable` in the Python `analyze` partials (e.g. unique `[#…]` anchors).
4. After the driver docs regenerate, re-run the `typedb-docs` build to confirm the driver-reference
   errors clear.

Tracking this here so the docs team can route it to the driver repo; no `typedb-docs` change applies.
