# TicketTrayFilter projection relation experiment

Status: **closed for this branch**. This is the Ruleset companion to [`vslices/tooling#7`](https://github.com/vslices/tooling/pull/7).

The original `TicketTrayFilter` flattening hypothesis was investigated and rejected. No implicit `flatten-single-field` target vocabulary is admitted by this branch.

For the current cross-repository model:

- [`vslices/intermediate-representation`](https://github.com/vslices/intermediate-representation) owns VSIR language semantics;
- [`vslices/tooling`](https://github.com/vslices/tooling) owns executable parsing, validation, discovery, authoring and lowering mechanisms;
- this repository owns deterministic target realization knowledge;
- [`vslices/planifications`](https://github.com/vslices/planifications) owns the progressive migration/reconstruction traversal.

## TicketTrayFilter closure

The consumer evidence exposed a historical difference between nominal optional semantic representations and several nullable-string C# coordinates. That difference did **not** establish target flattening semantics.

The normalized VSIR instead states the relation explicitly:

```yaml
state:
  ProjectReference:
    optional: ProjectReference

representation:
  ProjectReference:
    type:
      optional: ProjectReference.Repr
    mapping:
      represent: state.ProjectReference
```

The admitted cross-repository path is:

```text
VSIR
  optional<X.Repr>
  + explicit represent(state.X)

Tooling
  structural semantic type
  + explicit projection expression

Ruleset
  type.optional
  + projection.represent

C#
  deterministic realization of those stated facts
```

A historical `string?` convenience shape is evidence about one materialization, not authority to rewrite `Option<X.Repr>`.

Therefore:

```text
flatten-single-field
  -> rejected for this experiment
  -> no Ruleset node added
  -> no implicit nullable-string lowering
```

A future consumer may establish a different explicit relation, but that would be a new evidence-driven experiment.

## Location evidence crossed

`Location.vsir` provides explicit semantic structure for relations that previously exposed missing lowering mechanisms:

```text
structured type
  sequence<T>

representation expressions
  represent(value)
  select(source-expression, field)
  map(source-expression, binding, value-expression)

construction
  resolve
  apply with direct input
  apply with mapped/container input
```

The Ruleset does not infer those relations. VSIR states them, Tooling parses/validates their structure, and this repository supplies deterministic C# realization knowledge.

Current target nodes exercised by the Location path include:

```text
type.sequence
projection.stringify
projection.represent
projection.select
projection.map
construction.resolve.condition
construction.resolve.value
construction.apply.input
construction.apply.value
construction.apply-sequence.input
construction.apply-sequence.value
```

## TicketTrayFilter structural type evidence

The final TicketTrayFilter path adds the independently justified target realization:

```text
type.optional
  bindings: [value]
  -> Option<{value}>
```

The Ruleset realizes the structural constructor already present in VSIR. It does not infer the inner semantic type or flatten its representation.

## Name condition-expression evidence crossed

`Name.vsir` adds a condition over a derived semantic string. The language expresses the derivation explicitly rather than overloading `length-at-most` with collection behavior:

```yaml
condition:
  intrinsic: length-at-most
  args:
    value:
      intrinsic: concat-space
      values:
        - input.Names
        - input.FirstSurname
        - input.SecondSurname
    max: 92
```

The target-neutral expression tree is owned by VSIR. Tooling recursively lowers the inner expression first and then supplies its rendered value to the outer condition.

This repository owns the deterministic C# realization:

```text
intrinsic.concat-space
  bindings: [values]
  -> string.Join(" ", new[] { ... })
```

The same node is reused wherever the same semantic intrinsic appears; context does not create a second concat relation.

## StreetExtension intrinsic refinement

The real `StreetExtension` witness established semantic refinement with named outputs:

```yaml
- refine:
    intrinsic: split-first-rest
    value: input.Value
    as:
      Name: name
      Value: value
```

The language contract is tracked in [`vslices/intermediate-representation#1`](https://github.com/vslices/intermediate-representation/pull/1).

This Ruleset supplies only target realization:

```text
refine.split-first-rest.condition
refine.split-first-rest.output.Name
refine.split-first-rest.output.Value
```

Missing output realization fails closed rather than dropping or guessing an output.

## Explicit representation composition

The composition:

```text
select(represent(state.Street), Value)
```

reaches separate Ruleset nodes for `projection.represent` and `projection.select`.

`projection.select` does not create or imply `represent`. This preserves the distinction from:

```text
select(state.Street, Value)
```

unless VSIR explicitly establishes equivalence.

## One semantic `apply`

VSIR exposes one semantic `apply` operation. The shape of its already-known input may select different admitted C# realization nodes:

```text
direct input
  -> construction.apply.*

mapped/container input
  -> construction.apply-sequence.*
```

That target distinction does not leak back into VSIR as separate semantics.

## Authority boundary

```text
VSIR / validation
  -> states and admits semantic structure

Tooling
  -> validates and composes constrained mechanisms

Ruleset
  -> supplies deterministic target realization vocabulary
```

A renderer/template does not create semantic authority by itself. A Ruleset node is usable only when admitted VSIR structure reaches it through Tooling.

## Authoring parity

The cross-repository public-authoring completeness criterion is:

```text
discovery can explain how to express a VSIR construction
new/update can author it
validation can check it
lower can consume it
Ruleset can materialize it when target knowledge is required
```

Canonical parser/lowering support can be broader than public authoring parity. Ruleset participates in the target-realization leg; it does not own authoring grammar or semantic conformance.

## Exact binding contract

Every target rule declares the complete set of placeholders it accepts through `bindings`.

The lexical placeholder contract shared with Tooling is:

```regex
[A-Za-z][A-Za-z0-9_-]*
```

The semantic contract is exact:

```text
no duplicate binding declaration
no undeclared template placeholder
no declared binding unused by the template
no missing binding at render time
no extra binding at render time
```

The branch CI validates the installable manifest and every rule catalog against the same placeholder grammar used by Tooling's `CSharpLoweringRuleSet`.

## Reconstruction path

For a fresh review, read in this order:

1. [`vslices/tooling#7`](https://github.com/vslices/tooling/pull/7) — primary executable experiment and handoff;
2. [`vslices/intermediate-representation/SPECIFICATION.md`](https://github.com/vslices/intermediate-representation/blob/main/SPECIFICATION.md) and the active experimental amendments — normative language semantics;
3. [`csharp/types.yaml`](../../csharp/types.yaml) and [`csharp/intrinsics.yaml`](../../csharp/intrinsics.yaml) — deterministic C# realization knowledge;
4. [`vslices/intermediate-representation/AUTHORING-LOWERING-PARITY.md`](https://github.com/vslices/intermediate-representation/blob/main/AUTHORING-LOWERING-PARITY.md) — cross-repository parity model;
5. [`vslices/planifications/plans/progressive-source-migration.md`](https://github.com/vslices/planifications/blob/main/plans/progressive-source-migration.md) — reconstruction traversal.
