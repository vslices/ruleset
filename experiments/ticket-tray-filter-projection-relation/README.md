# TicketTrayFilter projection relation experiment

This branch is the Ruleset companion to `vslices/tooling:experiment/ticket-tray-filter-projection-relation`.

The original `TicketTrayFilter` flattening hypothesis remains gated. The branch has since accumulated independent target-lowering evidence from the same consumer corpus, especially `SrvIdentityId` and `Location`.

## TicketTrayFilter consumer evidence

Ticket Support declares semantic representation coordinates including nominal representation types wrapped in `Option`, while its current Query-facing C# representation flattens several of those coordinates to nullable strings.

That difference does not by itself prove that flattening is C# lowering knowledge.

`flatten-single-field` therefore remains a candidate name only, not accepted Ruleset vocabulary.

## Evidence gate

Do not add a projection primitive merely because a target materialization happens to differ from the semantic representation. A rule is admitted only after Tooling establishes that:

1. the relevant semantic relation is faithfully represented by VSIR;
2. semantic conservation remains fail-closed;
3. the C# lowering mechanism can reach a target-knowledge lookup without inventing the relation;
4. the missing fact is genuinely target-specific rather than consumer policy or missing semantic structure.

## Location evidence crossed

`Location.vsir` now provides explicit semantic structure for relations that were previously blocked behind the lowering mechanism:

```text
structured type
  sequence<T>

representation expression
  represent(value)
  select(source-expression, field)
  map(source-expression, binding, value-expression)

construction
  resolve
  apply with direct input
  apply with mapped sequence input
```

The important distinction is that the Ruleset does not infer those relations. VSIR already states them. Tooling parses and validates their structure, and the Ruleset supplies only deterministic C# realization knowledge.

For representation, explicit composition is preserved:

```text
select(represent(state.Street), Value)
```

lowers through separate `projection.represent` and `projection.select` nodes. `projection.select` does not insert `represent` implicitly.

For construction, one VSIR `apply` operation remains canonical. The shape of its input determines which C# realization is selected:

```text
direct input
  -> Apply

mapped sequence input
  -> ApplySeq
```

This is now an admitted lowering mechanism rather than an authoring-language split into target-shaped verbs.

## Authority boundary

```text
VSIR / validation
  -> admits and preserves semantic structure

Tooling
  -> validates and composes constrained lowering mechanisms

Ruleset
  -> supplies deterministic target realization vocabulary
```

A renderer/template still does not create semantic authority by itself. A Ruleset node is usable only when an admitted VSIR semantic structure reaches it through Tooling.

## Open TicketTrayFilter questions

- Is flattening authorized by the semantic representation itself, by an explicit relation, or by target policy?
- Does `Option<X.Repr>` compose as optionality around the projection or as a target nullable convention?
- What prevents a multi-field `X.Repr` from being flattened accidentally?
- What target-neutral information must Tooling pass to a rule so the rule does not reconstruct VSIR semantics?
- What diagnostic is expected when no unique projection is authorized?

## Success criterion

Location succeeds when the same normalized VSIR that can be progressively authored through `new -> discovery -> update` can also be consumed by `lower` without rewriting it into a legacy grammar or introducing implicit representation/construction semantics.

The original TicketTrayFilter flattening hypothesis remains unresolved until its own evidence crosses the same gate.
