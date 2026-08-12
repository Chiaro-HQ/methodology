# Changelog

Method changes are dated, versioned, and published here before they are
applied to an examination. Corrections that do not change the method (typos,
formatting, clearer wording with the same meaning) ship without a version, and
the commit history on this repo records every one of them.

## v1.3, 2026-08-11

When evidence is collected. The method page has always said a population is
all occurrences over the observation window; this release pins the collection
timing that makes the sentence literally true, recorded here before it is
applied to an examination.

- Collection happens in two phases, split by evidence type. Configuration
  state, policies, and rosters are captured while the period is still open,
  inside its final three weeks, each with the exact moment of observation.
  Every event population is collected after the period closes, because a
  list of everything that happened is only complete once the period has
  ended.
- The stretch between a configuration observation and the period's close is
  carried by the system's own change record, pulled complete after the
  close. The report states the date the state was inspected and the interval
  the change record covers.
- The tooling enforces the split with exactly two refusals: an event
  population offered early, and a configuration capture offered after the
  close. Nothing is refused for lateness; the one thing that decays while
  collection drifts is the client's own log retention, and what can no
  longer be produced is disclosed.

## v1.2, 2026-08-06

The census language pass. The method page has said since first publication
that record-checked attributes are tested over the complete population; the
texts now say it too.

- Attribute texts read "for each X" / "for all X"; period-form pass criteria
  read "every X in the period"; collection playbooks ask for the population,
  not a sample of it. The exception rule is unchanged: an isolated miss in an
  otherwise working practice is an exception, not a gap, and the published
  deviation rule decides systematic.
- Every record-checked attribute carries a `frequency` field, the input to
  the published fallback-selection table for the rare population where a
  complete pull is impracticable. Attributes drawing on the same population
  carry the same frequency.
- The readiness verdict vocabulary gains `absent` (the practice itself does
  not exist), distinct from `gap` (the practice exists but cannot be
  evidenced). Recording an absence requires the operator's own words.
- Deliberate survivor: DAT-04's "sampled data," which means data-subsetting
  for non-production environments, not audit sampling. Machine identifiers
  (the `sample` attribute type, `sample_eligible`) keep their names.

## v1.1, 2026-08-06

The HIPAA Security Rule crosswalk.

- `method/hipaa-crosswalk.md` and `framework/hipaa-crosswalk.json`: all 64
  provisions of 45 CFR 164.308 through 164.316, one row each, mapped to the
  control library with a stated resolution and rationale.
- Three controls added to carry what the SOC 2 library alone did not:
  business associate agreements (HIP-01), emergency access to ePHI systems
  (HIP-02), and six year security documentation retention (HIP-03). They
  apply only when the HIPAA add-on is in scope.
- Five HIPAA-gated attributes added to existing controls (risk analysis,
  sanctions, the named security official, emergency mode protections, local
  ePHI storage), so the ePHI delta is tested rather than advised.
- The library grows to 89 controls, 369 test attributes, and
  528 calibration examples.

## v1.0, 2026-07-28

Initial public release.

- 86 controls, 355 test attributes, 61 Trust Services Criteria,
  22 evidence sources.
- 498 calibration examples, all synthetic, published with their
  correction ratio.
- The scoping playbook, the collection rules, and the full tool inventory
  from the live server.
- The Type II testing method: complete populations by default, the published
  deviation rule with 8 worked examples, and the seeded sampling
  fallback.
