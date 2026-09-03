# VSlices Ruleset

VSlices Ruleset is the official, external source of lowering knowledge used by VSlices Tooling.

It describes how semantic structures represented by VSIR may be materialized for concrete targets without embedding target-specific lowering knowledge in the `vslices` executable.

The intended boundary is:

```text
VSIR
  = semantic source

VSlices Ruleset
  = revisable lowering knowledge

VSlices Tooling
  = execution and orchestration mechanisms

project/.vslices/ruleset
  = local, version-controlled ruleset snapshot
```

## Why this repository exists

Lowering knowledge is expected to evolve as VSIR grows and as target materialization is better understood. Keeping that knowledge outside the CLI allows rules to change, be reviewed, and be experimented with without requiring a new tooling release for every semantic mapping change.

The CLI should therefore know how to discover, load, validate, and execute supported classes of rules, while concrete target mappings live here.

A missing rule is not permission for the CLI or an interpreter to guess. Unsupported or unresolved lowering must remain explicit.

## Current status

This repository is experimental. The first supported surface is intentionally small and exists to validate the boundary between tooling mechanisms and lowering knowledge.

The current ruleset begins with deterministic C# expression mappings for VSIR intrinsics already exercised by the `StreetName` benchmark.

## Layout

```text
manifest.yaml
manifest.schema.json
csharp/
  intrinsics.yaml
```

`manifest.yaml` discovers target-specific rule files. `manifest.schema.json` is external as well: the executable does not embed the concrete manifest schema or the rules contained by this repository.

## Project-local use

`vslices init` is intended to materialize an official ruleset snapshot under:

```text
.vslices/ruleset/
```

Once initialized, lowering should operate from that local state and should not depend on the network. The local copy is expected to be editable and suitable for version control so project-specific experiments and changes remain visible and reconstructible.

## Evolution principle

A useful working distinction is:

- when VSlices gains a new operational capability, `vslices/tooling` may need to change;
- when VSlices learns a new way to lower existing semantic structure, this ruleset should usually change instead.

The ruleset is not intended to become an unrestricted plugin or package system. New rule execution primitives should be introduced only when concrete VSIR structures demonstrate the need for them.

## Longer-term validation

The ruleset should be exercised through deterministic, reproducible tests in VSlices Tooling. A long-term goal is for VSlices projects, including the tooling itself where appropriate, to increasingly describe representable semantics through `.vsir` artifacts and use the same lowering pipeline they provide to other projects.
