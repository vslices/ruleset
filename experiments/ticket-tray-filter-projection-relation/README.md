# TicketTrayFilter projection relation experiment

This branch is the Ruleset companion to `vslices/tooling:experiment/ticket-tray-filter-projection-relation`.

It intentionally starts without a new executable C# rule.

## Consumer evidence

Ticket Support declares semantic representation coordinates including nominal representation types wrapped in `Option`, while its current Query-facing C# representation flattens several of those coordinates to nullable strings.

That difference does not by itself prove that flattening is C# lowering knowledge.

## Evidence gate

Do not add a projection primitive until the Tooling experiment establishes that:

1. the relevant source type and projection relation are faithfully represented by VSIR;
2. semantic conservation remains fail-closed;
3. the current C# lowering mechanism can reach a target-knowledge lookup without inventing the relation;
4. the missing fact is genuinely target-specific rather than consumer policy or semantic structure.

`flatten-single-field` is therefore a candidate name only, not accepted Ruleset vocabulary.

## Questions before a rule exists

- Is flattening authorized by the semantic representation itself, by an explicit relation, or by target policy?
- Does `Option<X.Repr>` compose as optionality around the projection or as a target nullable convention?
- What prevents a multi-field `X.Repr` from being flattened accidentally?
- What target-neutral information must Tooling pass to a rule so the rule does not reconstruct VSIR semantics?
- What diagnostic is expected when no unique projection is authorized?

## Success criterion

This branch should remain rule-free until the real `TicketTrayFilter` CLI run reaches a missing Ruleset-knowledge boundary. If the first failure belongs to VSIR, parsing/validation, target context or lowering mechanism, the corresponding change belongs elsewhere.
