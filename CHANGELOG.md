# Changelog

Method changes are dated, versioned, and published here before they are
applied to an examination. Corrections that do not change the method (typos,
formatting, clearer wording with the same meaning) ship without a version.

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
