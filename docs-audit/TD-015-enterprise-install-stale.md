# TD-015 — Enterprise install docs are stale (pre-rebrand image, EOL versions)

- **Severity:** P1–P2 (a documented install command that no longer works)
- **Status:** Findings & recommendations only — **needs TypeDB team input to fix** (private registry)
- **Source:** Second-pass `typedb-docs` audit, 10 Sep 2026
- **Type:** This document lays out the finding and recommendation; it is **not** a fix. It lives in
  `docs-audit/`, outside any Antora module, so it does not affect the built site.

## Affected files

| File | Line(s) | Stale content |
|------|---------|---------------|
| `home/modules/ROOT/partials/enterprise_docker.adoc` | 9, 22 | `docker pull vaticle/typedb-cloud:latest`; volume paths `/opt/typedb-cloud-all-linux-x86_64/...` |
| `home/modules/ROOT/partials/enterprise_kubernetes.adoc` | 60, 346, 350 | `typedb-cloud-all-mac-x86_64-2.25.12`, `| vaticle/typedb-cloud`, `| 2.25.9` |

These partials are surfaced on the published Enterprise installation pages under `home/`.

## What a reader sees

The Enterprise Docker instructions tell the user to run:

```bash
docker pull vaticle/typedb-cloud:latest
```

and mount volumes into `/opt/typedb-cloud-all-linux-x86_64/server/...`. The Kubernetes page pins
TypeDB Cloud **2.25.9 / 2.25.12** and the same `vaticle/typedb-cloud` image.

## Evidence

- The Docker namespace was rebranded **Vaticle → TypeDB**; `vaticle/*` is legacy.
- Docker Hub public API returns **no** repository for `vaticle/typedb-cloud`, `typedb/typedb-cloud`,
  `typedb/typedb-enterprise`, or `typedb/typedb-cluster` (all 404). The only public images in the
  `typedb/` namespace are `typedb` (Community Edition, updated 2026-09-08), `typedb-snapshot`,
  `typedb-mcp`, and `typedb-mcp-server`.
- `enterprise_docker.adoc` itself states: *"The TypeDB Enterprise image is hosted in our **private**
  Docker repository. Make sure to use `docker login` first to authenticate."* — so the correct image
  cannot be verified from outside the private registry.
- Current TypeDB release line is **3.13.0** (verified on `repo.typedb.com`), so the `2.25.x` version
  strings are two major versions behind.

## Impact

A prospective Enterprise user following these instructions runs a `docker pull` that fails (or pulls
a pre-rebrand image), and configures volume paths/versions that no longer match the product. This is
a broken first-run experience on a commercial install path.

## Why this PR does not change the docs directly

The correct values live behind a **private** registry and cannot be verified publicly. Substituting a
guessed image name, container path prefix, or version onto an install page risks replacing stale-but-
traceable instructions with confidently-wrong ones. Per the audit principle "don't guess on install
pages," we are surfacing the finding and asking for the authoritative values instead.

## Recommendation

Update both partials once the following are confirmed by the TypeDB team:

1. **Image reference** — current TypeDB Enterprise image name and the private registry/host
   (e.g. `docker login <registry>` + `<registry>/<image>:<tag>`), replacing `vaticle/typedb-cloud:latest`.
2. **In-container path prefix** — the current install directory, replacing
   `/opt/typedb-cloud-all-linux-x86_64/` in the `docker create -v` mounts.
3. **Version strings** — the current Enterprise version to display in
   `enterprise_kubernetes.adoc` (lines 60, 350), replacing `2.25.12` / `2.25.9`.
4. Sweep the Enterprise partials for any remaining `vaticle/` and `typedb-cloud` naming and align
   them with the current product name (Enterprise vs the legacy "Cloud" self-hosted naming).

Once these are provided, this can be turned into a one-commit content fix and this note removed.
