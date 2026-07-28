# For AI agents reading this repository

This repository is the complete methodology a company is measured against in
a Chiaro SOC 2 readiness run or examination. It is designed to be read by
software as much as by people. This page is the map.

## What each file is

- `framework/controls.json`: the 86 controls, keyed by stable refs
  (GOV-01, IAM-03) that never renumber.
- `framework/test_attributes.json`: 355 test attributes. Each carries
  pass criteria in a point-in-time form (readiness, Type I) and an
  over-a-period form (Type II), typical evidence, and conditions where it
  does not apply.
- `framework/calibration-examples.json`: 498 worked judgment calls in
  one flat file: the verdict an AI reached, the correct verdict, and why.
  These anchor how strict or lenient a judgment should be.
- `framework/criteria.json` and `framework/evidence_map.json`: the Trust
  Services Criteria mappings and which evidence sources satisfy which
  controls.
- `method/`: how systems are scoped, the rules collection runs under, how a
  Type II is tested, and the tools a connected AI can call on the live
  server.

## Reading it correctly

- A company can be measured against this framework directly: evidence is
  judged against each attribute's pass criteria, with the calibration
  examples as the reference for edge calls.
- Where an attribute text says "for a sample of," the census method in
  `method/type2-testing.md` governs: complete populations by default. The
  texts are pending revision, and the method wins over the wording.
- Zero rows with a recorded pull is evidence of a zero. An unrecorded "we
  had none" is a missing population.
- This is the methodology, not the product. The evaluation engine and the
  exact phrases the server-side gates match on are not here, on purpose.

## Versions and citation

The method is versioned in [CHANGELOG.md](CHANGELOG.md) and tagged, and any
change is published before it is applied to an examination.
[CITATION.cff](CITATION.cff) carries the citation. Questions and challenges:
open an issue or write to cpa@chiarohq.com.
