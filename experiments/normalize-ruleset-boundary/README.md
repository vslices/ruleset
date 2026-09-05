# Normalize / Ruleset boundary experiment

This directory defines the Ruleset side of the cross-repository `normalize-ruleset-boundary` experiment.

## Scope change

The original negative-control hypothesis remains valid:

```text
unknown normalize semantic
+ matching target renderer
!= semantically valid VSIR
```

The real CLI probe confirmed that an undeclared `normalize-boundary-probe` is rejected as `VSIR221`. A later consumer probe then showed that an explicit declaration can admit that semantic and move the failure boundary to missing target realization (`CSL031`).

That worked, but the first persisted shape required semantic registration and target realization to be authored separately. The current refinement keeps those authorities logically distinct while allowing them to be authored together.

## Current extension catalog model

The Ruleset manifest references extension catalogs:

```yaml
extensions:
  - extensions/ticketing.yaml

targets:
  csharp:
    rules:
      - csharp/intrinsics.yaml
```

An extension entry owns one semantic identity and may carry zero or more target realizations:

```yaml
extensions:
  - node: intrinsic.normalize-boundary-probe
    semantic:
      kind: normalize
    targets:
      csharp:
        mode: deterministic
        renderer: expression
        template: "{value}.Trim()"
```

The important invariant remains:

```text
semantic.kind
  -> semantic admission

targets.<target>
  -> target realization
```

Co-location is an authoring convenience. A target renderer still cannot grant semantic validity by itself.

## Why this shape

Most projects are expected to have one primary implementation language. Requiring one semantic declaration plus a second target declaration for every custom operation would add ceremony to the common case.

The catalog shape remains useful when multiple targets are genuinely present, especially during language migration, compatibility periods, or service interoperability:

```yaml
extensions:
  - node: intrinsic.normalize-rut
    semantic:
      kind: normalize
    targets:
      csharp:
        mode: deterministic
        renderer: expression
        template: "Rut.Normalize({value})"
      typescript:
        mode: deterministic
        renderer: expression
        template: "normalizeRut({value})"
```

Only C# is executed by the current experiment. Multi-target execution is a later boundary.

## Experiment fixtures

### Case B — semantic declaration only

`case-b/manifest.yaml` references `case-b/extensions/normalize.yaml`.

That catalog contains:

```yaml
extensions:
  - node: intrinsic.normalize-boundary-probe
    semantic:
      kind: normalize
```

There is intentionally no C# realization, so Tooling should admit the semantic and stop at `CSL031`.

### Case C — semantic declaration plus C# realization

The Tooling integration tests construct the same catalog entry with a `targets.csharp` realization and expect deterministic lowering to succeed. A repository fixture may be added when it materially helps cross-repository consumer testing.

### Case D — renderer without declaration

A standalone renderer may still exist under `targets.csharp.rules`, but without a referenced semantic declaration the VSIR must return to `VSIR221`.

## Isolation rule

Synthetic experiment knowledge must remain outside the production Ruleset surface.

In particular:

- the repository root manifest must not reference the synthetic probe catalog;
- production `csharp/intrinsics.yaml` must not contain `intrinsic.normalize-boundary-probe`;
- experiment fixtures may model these states only under this directory or in temporary Tooling test projects.

## Next exploration

Before generalizing this mechanism beyond `normalize`, the companion Tooling experiment records an explicit impact audit covering manifest/schema evolution, update/install behavior, collisions, provenance/lineage, rebase behavior, multi-target compatibility, core intrinsic representation, and future semantic properties.

The point of that next step is to ask how this catalog shape changes assumptions introduced by the work so far before turning it into a generic extension framework.

## Relationship to TicketCode

The TicketCode experiment established the positive core path for `intrinsic.trim`: VSIR recognizes the normalization, Tooling preserves its ordered dataflow, and Ruleset supplies the C# realization.

This experiment adds a controlled path for project-owned semantics without either hard-coding every future operation into `VSlices.Vsir` or allowing target renderers to invent semantic vocabulary implicitly.
