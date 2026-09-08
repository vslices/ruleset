# TicketTrayFilter projection relation experiment

This branch is the Ruleset companion to [`vslices/tooling#7`](https://github.com/vslices/tooling/pull/7).

The original `TicketTrayFilter` flattening hypothesis remains gated. The branch has since accumulated independent target-lowering evidence from the same consumer corpus, especially `SrvIdentityId`, `Location`, `StreetExtension`, and `Name`.

For the current cross-repository model:

- [`vslices/intermediate-representation`](https://github.com/vslices/intermediate-representation) owns VSIR language semantics;
- [`vslices/tooling`](https://github.com/vslices/tooling) owns executable parsing, validation, discovery, authoring and lowering mechanisms;
- this repository owns deterministic target realization knowledge;
- [`vslices/planifications`](https://github.com/vslices/planifications) owns the progressive migration/reconstruction traversal.

## TicketTrayFilter evidence gate

Ticket Support declares semantic representation coordinates containing nominal representation types wrapped in `Option`, while its current Query-facing C# representation flattens several coordinates to nullable strings.

That difference does not by itself prove that flattening is C# lowering knowledge.

`flatten-single-field` therefore remains a candidate relation only, not accepted Ruleset vocabulary.

Do not add a target projection primitive merely because one materialization differs from the semantic representation. A rule is admitted only after:

```text
VSIR faithfully expresses the relation
semantic conservation remains fail-closed
Tooling can reach a target-knowledge lookup without inventing the relation
the missing fact is genuinely target-specific
```

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

This repository already owns the deterministic C# realization:

```text
intrinsic.concat-space
  bindings: [values]
  -> string.Join(" ", new[] { ... })
```

The same Ruleset node is intentionally reused whether the semantic expression appears in a representation projection or as an argument to a construction condition. Context does not create a second concat relation.

The inserted spaces are part of the rendered string and therefore contribute to a subsequent `.Length` check. Optional-operand target realization remains a separate type/lowering concern; no nullable/Option flattening is inferred here.

## Explicit representation composition

The composition:

```text
select(represent(state.Street), Value)
```

reaches separate Ruleset nodes for `projection.represent` and `projection.select`.

`projection.select` does not create or imply `represent`.

This preserves the VSIR distinction between:

```text
Select(Represent(state.Street), Value)
```

and:

```text
Select(state.Street, Value)
```

unless an explicit semantic rule establishes equivalence.

## One semantic `apply`

VSIR exposes one semantic `apply` operation.

The shape of its input determines which admitted C# realization is selected:

```text
direct input
  -> construction.apply.*
  -> Apply-style realization

mapped/container input
  -> construction.apply-sequence.*
  -> ApplySeq-style realization
```

The target distinction remains Ruleset/lowering knowledge and does not leak back into VSIR vocabulary as `apply-seq`.

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

The current cross-repository completeness criterion is:

```text
discovery can explain how to express a VSIR construction
new/update can author it
validation can check it
lower can consume it
Ruleset can materialize it when target knowledge is required
```

Ruleset therefore participates in the final leg of **authoring parity**, but does not own the authoring grammar or the semantic language.

## Explicit binding contract

Every target rule declares the complete set of placeholders it accepts through `bindings`.

The contract is exact:

```text
no duplicate binding declaration
no undeclared template placeholder
no declared binding unused by the template
no missing binding at render time
no extra binding at render time
```

The branch CI validates the installable manifest and every rule catalog against this contract. Tooling applies the same rule model to project-owned extension target realizations.

## Open TicketTrayFilter questions

- Is flattening authorized by the semantic representation itself, by an explicit relation, or by target policy?
- Does `Option<X.Repr>` compose as optionality around a projection or as a target nullable convention?
- What prevents a multi-field `X.Repr` from being flattened accidentally?
- What target-neutral information must Tooling pass so the Ruleset does not reconstruct VSIR semantics?
- What diagnostic is expected when no unique projection is authorized?

Until those questions cross the same evidence gate, the original TicketTrayFilter flattening hypothesis remains unresolved.

## Reconstruction path

For a fresh review, read in this order:

1. [`vslices/tooling#7`](https://github.com/vslices/tooling/pull/7) — executable experiment and handoff;
2. [`vslices/intermediate-representation/SPECIFICATION.md`](https://github.com/vslices/intermediate-representation/blob/main/SPECIFICATION.md) and the active experimental amendments — normative language semantics;
3. [`vslices/intermediate-representation/AUTHORING-LOWERING-PARITY.md`](https://github.com/vslices/intermediate-representation/blob/main/AUTHORING-LOWERING-PARITY.md) — cross-repository parity model;
4. [`vslices/planifications/plans/progressive-source-migration.md`](https://github.com/vslices/planifications/blob/main/plans/progressive-source-migration.md) — reconstruction traversal.
