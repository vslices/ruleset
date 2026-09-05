# VSlices Ruleset

VSlices Ruleset is the official, external source of lowering knowledge used by VSlices Tooling.

It describes how semantic structures represented by VSIR may be materialized for concrete targets without embedding target-specific lowering knowledge in the `vslices` executable.

The intended boundary is:

```text
VSIR
  = semantic source

VSlices Ruleset
  = source-owned revisable lowering knowledge

VSlices Tooling
  = execution and orchestration mechanisms

project/.vslices/ruleset
  = installed source-owned snapshot

project/.vslices/extensions
  = project-owned semantic extension overlay
```

## Why this repository exists

Lowering knowledge is expected to evolve as VSIR grows and as target materialization is better understood. Keeping that knowledge outside the CLI allows rules to change, be reviewed, and be experimented with without requiring a new tooling release for every target mapping change.

The CLI should therefore know how to discover, load, validate, and execute supported classes of rules, while concrete target mappings live here.

A missing rule is not permission for the CLI or an interpreter to guess. Unsupported or unresolved lowering must remain explicit.

## Current status

This repository is experimental. The supported surface is intentionally small and exists to validate the boundary between tooling mechanisms and lowering knowledge.

Current C# rules include deterministic expression mappings for consumer-proven intrinsics such as `non-empty`, `not-whitespace`, `length-at-most`, ordinal equality and `trim`.

## Layout

```text
manifest.yaml
manifest.schema.json
csharp/
  intrinsics.yaml
```

`manifest.yaml` discovers target-specific rule files. `manifest.schema.json` describes that distributable snapshot. Project semantic extensions are deliberately **not** part of this manifest.

## Project-local use

`vslices init` materializes a Ruleset snapshot under:

```text
.vslices/ruleset/
```

Lowering operates from that local state and does not require the network after materialization. The snapshot is suitable for version control as evidence of the installed lowering knowledge, but its lifecycle remains source-owned: `vslices init --force` and `vslices update --ruleset` may replace it.

Project-owned semantic extensions therefore live separately under:

```text
.vslices/extensions/
```

That overlay is owned by the consumer project and must survive Ruleset replacement. Tooling, rather than this repository, defines and validates the current project-extension catalog format.

## Evolution principle

A useful working distinction is:

- when VSlices gains a new operational capability, `vslices/tooling` may need to change;
- when VSlices learns a new way to lower already-recognized core semantic structure, this Ruleset should usually change instead;
- when one project explicitly admits custom semantic vocabulary, that declaration belongs to the project's `.vslices/extensions` overlay rather than this distributable Ruleset snapshot.

The Ruleset is not intended to become an unrestricted plugin or package system. New rule execution primitives should be introduced only when concrete VSIR structures demonstrate the need for them.

## Longer-term validation

The Ruleset should be exercised through deterministic, reproducible tests in VSlices Tooling. A long-term goal is for VSlices projects, including the tooling itself where appropriate, to increasingly describe representable semantics through `.vsir` artifacts and use the same lowering pipeline they provide to other projects.
