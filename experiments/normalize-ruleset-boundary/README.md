# Normalize / Ruleset boundary experiment

This directory defines the Ruleset side of the cross-repository `normalize-ruleset-boundary` experiment.

## Hypothesis

Ruleset may realize target-specific syntax for a semantic node that VSIR already recognizes. It must not grant semantic validity to an unknown VSIR normalization intrinsic.

The probe therefore needs a deliberately adversarial rule whose node name matches an unknown normalize intrinsic used by the Tooling experiment.

## Isolation rule

The adversarial rule belongs only to an experiment fixture.

It must **not** be referenced by the repository `manifest.yaml` and must **not** be added to `csharp/intrinsics.yaml`, because doing so would make experimental fake knowledge look like production target knowledge.

The Tooling-side test may copy or materialize an equivalent isolated fixture into its temporary project when exercising the CLI.

## Expected observation

```text
VSIR with unknown normalize intrinsic
  + adversarial C# renderer with matching node name
  -> VSIR validation rejects the semantic node first
  -> renderer is never semantically eligible
  -> no C# materialization
```

If the CLI reaches rule lookup and reports a missing target rule, or if the adversarial renderer can make lowering succeed, the intended VSIR / Ruleset authority boundary is broken.

## Relationship to TicketCode

The previous TicketCode experiment established the positive path for `intrinsic.trim`: VSIR recognizes the normalization, Tooling preserves its dataflow, and Ruleset supplies the C# realization.

This experiment is the negative control for that result. It asks whether target knowledge can exceed the semantic vocabulary rather than whether known semantics can be realized.
