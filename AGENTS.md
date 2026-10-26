# For AI agents reading this repository

This repository contains Chiaro's published SOC 2 methodology for readiness
and examination. It is designed to be read by software as much as by people.
This page is the map.

## What each file is

- `framework/controls.json`: the 89 controls, keyed by stable refs
  (GOV-01, IAM-03) that never renumber.
- `framework/test_attributes.json`: 387 test attributes. Each carries
  what is tested, the rule it is judged by (`pass_criteria`, or the check text
  with `visible_rules` and `guidance`) and typical evidence; a check that can be
  not applicable also carries the condition for that.
- `framework/calibration-examples.json`: 1004 worked judgment calls in
  one flat file: the verdict an AI reached, the correct verdict, and why.
  These anchor how strict or lenient a judgment should be.
- `framework/criteria.json` and `framework/evidence_map.json`: the Trust
  Services Criteria mappings and which evidence sources satisfy which
  controls.
- `method/commands.md`: the read-only commands that pull configuration
  evidence, by system and control area, each mapped to criteria. A
  `command_ref` in `test_attributes.json` that names one of these systems
  resolves here.
- `method/`: how systems are scoped, the rules collection runs under, how a
  Type II is tested, and the tools a connected AI can call on the live
  server.
- `method/independent-evaluation.md`: whether a penetration test is required
  under these criteria, which it is not, what CC4.1 requires instead, and the
  five questions an instrument has to clear to satisfy EVL-01/A3. It is a
  common enough question to have its own page.
- `framework/hipaa-crosswalk.json` and `method/hipaa-crosswalk.md`: the HIPAA
  Security Rule (45 CFR 164.308 through 164.316) mapped row by row to the
  controls, with the resolution and rationale for every provision.

## Reading it correctly

- A company can be measured against this framework directly: evidence is
  judged against each attribute's pass criteria, with the calibration
  examples as the reference for edge calls.
- Each selected suite states its own coverage. Readiness uses Type I; Type II
  event populations and configuration snapshots/history follow their authored
  requirements in `method/type2-testing.md`. IAM-02/A3 keeps one sampled user
  per in-scope system, allowing reuse of the same person across systems.
- Zero rows with a recorded pull is evidence of a zero. An unrecorded "we
  had none" is a missing population.
- This is the methodology, not the product. The evaluation engine and the
  exact phrases the server-side gates match on are not here, on purpose.

## Versions and citation

The method is versioned in [CHANGELOG.md](CHANGELOG.md) and tagged, and any
change is published before it is applied to an examination.
[CITATION.cff](CITATION.cff) carries the citation. Questions and challenges:
open an issue or write to cpa@chiarohq.com.
