# Normalize / Ruleset boundary experiment

This directory defines the Ruleset side of the cross-repository `normalize-ruleset-boundary` experiment.

## Scope change

The original negative-control hypothesis remains valid:

```text
unknown normalize semantic
+ matching target renderer
!= semantically valid VSIR
```

The first real CLI probe in `vslices/tooling` confirmed that an undeclared `normalize-boundary-probe` is rejected as `VSIR221`.

That result also exposed a flexibility boundary: requiring a new `VSlices.Vsir` release for every custom normalization semantic is unnecessarily restrictive when a custom Ruleset can carry explicit semantic extension knowledge.

The experiment therefore now distinguishes two independent authorities that may travel in the same Ruleset snapshot:

```text
semantic extension declaration
  !=
target realization
```

## Current minimal declaration

The Tooling-side case B accepts this experiment-only manifest shape:

```yaml
semantic-extensions:
  normalize-boundary-probe:
    kind: normalize
```

This declaration says only that the identity `normalize-boundary-probe` is admitted as a `normalize` semantic. It does not provide C# execution knowledge.

The corresponding isolated fixture lives under `case-b/manifest.yaml`.

## Expected experiment sequence

### A. Undeclared semantic

```text
normalize-boundary-probe
+ no semantic declaration
-> VSIR221
```

### B. Declared semantic without C# renderer

```text
semantic-extensions:
  normalize-boundary-probe:
    kind: normalize

+ no intrinsic.normalize-boundary-probe renderer
-> semantic validation succeeds
-> C# lowering reaches target capability boundary
-> CSL031
```

### C. Declared semantic with renderer

A later experiment commit may add an isolated C# renderer and should then demonstrate deterministic C# lowering.

### D. Renderer without declaration

Removing the semantic declaration while retaining that renderer must restore `VSIR221`. A renderer alone must never grant semantic validity.

## Isolation rule

Synthetic experiment knowledge must remain outside the production Ruleset surface.

In particular:

- the repository root `manifest.yaml` must not declare `normalize-boundary-probe`;
- production `csharp/intrinsics.yaml` must not contain `intrinsic.normalize-boundary-probe`;
- experiment fixtures may model those states only under this directory or in temporary Tooling test projects.

## Architectural intent

The active Ruleset may eventually carry both:

```text
target-neutral semantic admission evidence
+
target-specific realization knowledge
```

but Tooling must consume them as separate authorities.

This experiment remains deliberately limited to `normalize`. It does not define a universal semantic plugin system, and it does not extend `ensure`, equality, invariants, features, or other VSIR concepts without later evidence.

## Relationship to TicketCode

The previous TicketCode experiment established the positive core path for `intrinsic.trim`: VSIR recognizes the normalization, Tooling preserves its ordered dataflow, and Ruleset supplies the C# realization.

This experiment asks how custom semantics can be admitted without either hard-coding every future operation into `VSlices.Vsir` or allowing target renderers to invent semantic vocabulary implicitly.
