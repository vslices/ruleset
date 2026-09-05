# Normalize / Ruleset boundary experiment

This directory records the Ruleset-side evidence for the cross-repository `normalize-ruleset-boundary` experiment.

## Boundary that survived

The original negative-control result remains valid:

```text
unknown normalize semantic
+ matching target renderer
!= semantically valid VSIR
```

The A/B/C/D experiment established:

```text
undeclared
-> VSIR221

declared project semantic, no C# realization
-> CSL031

declared project semantic + C# realization
-> success

renderer without semantic declaration
-> VSIR221
```

A renderer never becomes semantic authority merely because Tooling can execute it.

## Ownership refinement after review

The first experiment shape referenced extension catalogs from the Ruleset manifest. That made the catalogs look project-owned while physically placing them inside the replaceable `.vslices/ruleset` snapshot.

That lifecycle is now rejected.

```text
project/.vslices/ruleset
  = source-owned installed snapshot
  = replaceable by init --force / update --ruleset

project/.vslices/extensions
  = project-owned semantic extension overlay
  = preserved by Ruleset replacement
```

Consequently, this repository's `manifest.yaml` describes only distributable Ruleset knowledge. It does not reference project semantic extensions, and `manifest.schema.json` no longer declares an `extensions` surface.

The project-owned catalog format lives on the Tooling side and currently looks like:

```yaml
# .vslices/extensions/manifest.yaml
version: 0.1
catalogs:
  - ticketing.yaml
```

```yaml
# .vslices/extensions/ticketing.yaml
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

The semantic declaration and target realization may remain co-located for authoring ergonomics, while Tooling preserves their separate authority.

## What remains in this Ruleset PR

The synthetic `normalize-boundary-probe` no longer needs a distributable Ruleset fixture. Its semantic declaration is project-owned and is exercised by Tooling integration tests and the Ticket Support consumer overlay.

The real `not-whitespace` intrinsic is different. Consumer evidence justified it as core VSIR semantics, so its C# realization belongs in production `csharp/intrinsics.yaml` together with the other core target mappings.

This leaves the Ruleset-side change intentionally small:

```text
production C# realization for not-whitespace
+ schema/docs clarifying that project extensions are not Ruleset-owned
```

## Package-discovery evidence

An earlier nested experimental fixture named `manifest.yaml` caused `vslices update --ruleset` to reject the repository archive with `RSM007`, because a distributable Ruleset archive must expose exactly one manifest.

That behavior remains correct. The fixture was first renamed and is now removed entirely because project extension catalogs no longer belong to a distributable Ruleset fixture.

The package invariant stays:

```text
one repository archive
-> exactly one installable manifest.yaml
```

## Relationship to TicketCode and Risk

`TicketCode` established the positive core path for `intrinsic.trim`:

```text
VSIR recognizes trim
-> Tooling preserves ordered normalization dataflow
-> Ruleset supplies C# realization
```

`Risk` then confirmed that core `trim` composes with core `not-whitespace`, validating the normalized value before construction.

Project extension admission is a separate overlay concern owned by Tooling/consumer projects. This repository supplies target knowledge for core semantics and remains intentionally ignorant of which custom vocabulary a project chooses to admit.

## Non-scope

This Ruleset PR does not define:

```text
project extension lifecycle
project extension catalog parsing
implicit semantics from renderer lookup
multi-target execution
extension support for ensure/equality/invariants/features
behavioral equivalence across target realizations
a universal plugin system
```

Those mechanisms belong to the companion Tooling experiment when justified by concrete evidence.
