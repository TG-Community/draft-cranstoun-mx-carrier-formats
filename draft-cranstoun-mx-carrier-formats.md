---
title: MX Carrier Formats Standard
abbrev: MXS-04
docname: draft-cranstoun-mx-carrier-formats
date: '2026-04-17'
consensus: false
keyword:
  - mx
  - carriers
  - code
  - provenance
author:
  - fullname: Tom Cranstoun
    organization: CogNovaMX
    email: info@cognovamx.com
normative:
  MXS-01:
    title: MX Core Metadata Standard
    target: https://github.com/TG-Community/draft-cranstoun-mx-core-metadata
  MXS-02:
    title: MX Extensions Standard
    target: https://github.com/TG-Community/draft-cranstoun-mx-extensions
  MXS-03:
    title: MX Provenance Standard
    target: https://github.com/TG-Community/draft-cranstoun-mx-provenance
---

--- abstract

This document specifies the MX metadata vocabulary for source code as a carrier format. It is deliberately narrow. Code files are documents, and as documents they inherit the full universal vocabulary defined by MXS-01 Core Metadata. This standard adds only the fields that are specific to code as a carrier and are not already covered by the core — a minimum viable set of code-specific provenance fields.

What the code does — function signatures, API surfaces, test metadata, type systems, invariants, behaviour — is explicitly out of scope. Each language already has its own convention for this (JSDoc, Python docstrings, Doxygen, rustdoc, godoc). MX does not duplicate them.

Database and media carriers remain out of scope. The MX framework reuses existing standards: DCAT v3, CSVW, and Dublin Core for databases and datasets; Schema.org, EXIF, IPTC, XMP, and ID3 for media; each language's own documentation convention for code behaviour.

The canonical field dictionary for this standard is `fields-data-carriers.yaml` in the public MX canon — two fields, down from forty in the v1.0-proposed draft. Together with `fields-data.yaml` (the core, 62 fields) they constitute The Gathering's complete open-standard metadata vocabulary.

The tight scope is deliberate. Refer to the field triage rubric (`field-triage-rubric.cog.md` in the MX canon) for the rule every candidate field must pass.

--- middle

# Conformance

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in RFC 2119.

This standard adopts the Level 1 / Level 2 / Level 3 conformance framework defined in MXS-01 by reference.

## Selective adoption

An implementation is **code-conformant at Level N** if, for every code artefact it emits, it satisfies the Level-N identity fields from MXS-01 and, where applicable, declares the MXS-04 provenance fields defined in the following section. Implementations MAY claim code conformance without adopting every field; `sourceRepo` and `derivedFromCommit` are both OPTIONAL.

## Baseline dependency

Every code-carrier document inherits the Level 1 identity fields from MXS-01 (`title`, `author`, `created`, `description`). A code-carrier document MUST satisfy those identity fields regardless of whether it declares any MXS-04 field.

## Database and media conformance

Implementations seeking metadata conformance for datasets, tables, columns, images, video, or audio look to the external standards cited in the abstract. MX makes no conformance claim for those carriers.

---

# The code vocabulary

Applies to source code files. The vocabulary is two fields, each carrying provenance that is specific to code as a document and not already covered by MXS-01's universal identity vocabulary (`author`, `license`, `derivedFrom`, `source`).

## Fields

**`sourceRepo`** (OPTIONAL, string). URL or path of the source-control repository that holds this code file's authoritative history. Lets a reader locate the canonical copy and its change history. Aligns with Schema.org `codeRepository`.

**`derivedFromCommit`** (OPTIONAL, string). Version-control commit identifier (typically a git SHA) that this artefact was derived from. Gives precise pedigree when the artefact is a build output, a transpiled copy, or a distributed snapshot of a specific repository state. Complements `derivedFrom` in MXS-01 with commit-level specificity.

Every other concern that the v1.0-proposed draft covered — function annotations, API surface, test metadata, class invariants, dependency declarations, runtime config, build conventions — is either out of scope entirely (deferred to the language convention) or handled generically by MXS-01 identity fields.

## Canonical carrier syntax

Per MXS-02, code carriers use the native metadata mechanism of their file format:

| Carrier | Native mechanism | Example |
|---------|------------------|---------|
| JavaScript / TypeScript | JSDoc comments | `@mx:sourceRepo https://github.com/...` |
| CSS | CSS block comments | `/* @mx:sourceRepo https://github.com/... */` |
| Shell | Shell comment block | `# sourceRepo: https://github.com/...` (after shebang) |
| SQL | SQL comment block | `-- @mx sourceRepo: https://github.com/...` |
| Any | YAML frontmatter in `.cog.*` files | `sourceRepo: https://github.com/...` |

The carrier mechanism is the syntax; the field names in the vocabulary are identical across all carriers. A JavaScript file declaring `@mx:sourceRepo` and a `.cog.md` declaring `sourceRepo:` carry the same semantic value.

## Profiles

MXS-04 declares a single profile: `code`. An implementation handling code artefacts applies the `code` profile's fields in addition to the core identity fields. No per-language sub-profiles — the vocabulary is language-agnostic.

---

# What this standard does NOT cover

MXS-04 is scoped by the field triage rubric. The following concerns were in the v1.0-proposed draft and are now explicitly out of scope:

- **Function-level annotations** (`pure`, `idempotent`, `complexity`, `throws`, `returns`, `param`) — defer to JSDoc, Python docstring convention, Doxygen, rustdoc, godoc.
- **API surface specification** (HTTP method, path, auth, rate limits, cache semantics) — defer to OpenAPI.
- **Test metadata** (test type, coverage targets, fixtures) — defer to test framework conventions.
- **Class-level annotations** (design pattern, thread-safety, invariants) — defer to language convention and code reviews.
- **Dependency vocabulary** (purpose, criticality, upgrade policy, alternatives considered) — defer to package manifests (`package.json`, `pyproject.toml`, `Cargo.toml`) and Architecture Decision Records.
- **Runtime configuration** (interpreter, language version, framework versions) — defer to native config (`.node-version`, `.tool-versions`, shebang lines, package manifests).
- **Coding conventions** (style, testing conventions, documentation conventions) — defer to linter configs and community style guides.
- **Inline code annotations** (`mx:begin`, `mx:end`, `mx:sensitive`, `mx:intentional`, `mx:todo`, `mx:ai`) — each language already has its own TODO-comment and annotation conventions.

If an implementation needs any of these, it uses the relevant external convention. MXS-04 does not duplicate them.

---

# Interaction with MXS-01

Every code-carrier document inherits the Level 1 identity contract from MXS-01:

- `title` REQUIRED
- `author` REQUIRED
- `created` REQUIRED
- `description` RECOMMENDED

These apply regardless of whether any MXS-04 field is declared. A `.cog.js` file MUST declare these in its top-level JSDoc or an explicit frontmatter block.

Level 2 fields from MXS-01 (`status`, `contentType`, `tags`, `audience`, `license`, `runbook`) remain available to code documents and SHOULD be used where they apply. In particular:

- `license` — MXS-01 covers the licence field universally; code files use the same field.
- `derivedFrom` — MXS-01 covers generic provenance pointers; `derivedFromCommit` in this standard adds commit-level precision when the artefact is a build output.
- `maintainer`, `publisher` — both apply to code as to any other document.

---

# Vendor extensions

Vendor extensions to code carriers follow the three-level namespace policy defined in MXS-02:

- **Standard** (no prefix): this document plus MXS-01, MXS-02, MXS-03.
- **Vendor public** (`x-<vendor>-` prefix): vendor-specific code-carrier additions, openly published.
- **Vendor private** (`x-<vendor>-p-` prefix): vendor-specific additions, obfuscated or registry-decoded.

CogNovaMX's code-adjacent extensions live in `cognovamx-fields.yaml` under the `x-mx-` prefix and are not part of this standard.

---

# Relationship to existing standards

MX is explicit about deferring to established vocabularies. Within code carriers specifically:

- **JSDoc / TSDoc / Python docstrings / Doxygen / rustdoc / godoc** — MX does not redefine `@param`, `@returns`, `@throws`, `@example`, or any other documentation-convention tag. Code-level documentation is each language's own convention.
- **OpenAPI** — for APIs that a code file implements, full API surface specification (request/response schemas, error codes, examples) uses OpenAPI. MXS-04 does not duplicate OpenAPI.
- **Package manifests** (`package.json`, `pyproject.toml`, `Cargo.toml`, `go.mod`) — dependency declarations are the native manifest's responsibility; MXS-04 does not add parallel dependency vocabulary.
- **Dublin Core / Schema.org** — generic metadata (dates, rights, formats, display names) uses the pass-through fields declared in MXS-01 (aligned with `dc:*` and `schema:*`), not code-specific redefinitions.
- **Test framework conventions** — unit/integration/e2e classification, coverage targets, and fixtures are owned by the test framework. MXS-04 does not declare test metadata fields.

--- back

# Changelog

- **2026-04-17 (v1.1-proposed)** — cut from 40 fields to 2 per the field triage rubric. Scope tightened to code-specific provenance only; behavioural vocabulary removed and deferred to language conventions.
- **2026-04-15 (v1.0-proposed)** — initial draft. Code-only scope. Databases and media explicitly deferred to DCAT/CSVW/Schema.org/EXIF/XMP per The Gathering's "reuse existing standards, do not duplicate" principle.

# References

- Field triage rubric — governs every MXS-04 field addition or removal (in the MX canon).
- Field cull log — records the 2026-04-17 cut of 40 fields down to 2.
- Companion standards — MXS-01 Core Metadata, MXS-02 Extensions, MXS-03 Provenance.
- External standards MX defers to: DCAT v3, CSVW, Dublin Core, Schema.org, EXIF, IPTC, XMP, ID3, OpenAPI, JSDoc, Python Docstring (PEP 257), Doxygen, rustdoc, godoc, IETF RFC format.
