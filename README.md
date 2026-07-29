# Chiaro methodology

This is the complete Chiaro methodology for SOC 2 readiness and audit: the
control library we test against, the criteria each control maps to, the
evidence we accept, and the rules the collection runs under. Readiness and the
examination run on the same framework, so the bar a company prepares against
is the bar the examination applies. It is published because the alternative to
showing your work is asking people to take it on faith, and a compliance
industry that ran on faith is why anyone is reading this.

## What is here

- **`framework/controls.json`**: 86 controls.
- **`framework/test_attributes.json`**: 355 test attributes.
  Each names what is tested, what evidence typically satisfies it, the pass
  criteria in both a point-in-time and an over-a-period form, and when it does
  not apply.
- **`framework/criteria.json`**: the 61 Trust Services Criteria
  each control maps to.
- **`framework/evidence_map.json`**: 22 evidence sources mapped
  to the controls they satisfy.
- **`framework/calibration-examples.json`**: every calibration example in one
  flat file. They also live nested per attribute in `test_attributes.json`;
  this file exists so you can actually find them.
- **`method/scoping-playbook.json`**: how systems are classified in scope, out of
  scope, or as a subservice organization.
- **`method/collection-rules.md`**: the operating rules the collection runs
  under, reproduced from the instructions the server actually sends.
- **`method/type2-testing.md`**: how a Type II is tested. Complete populations
  by default, the deviation rule and its worked examples, and the sampling
  fallback with its selection algorithm.
- **`method/tools.md`**: every tool a connected AI can call, generated from the
  live server.

## The calibration examples

The attributes carry 498 worked examples of a
judgment call between them, each recording a verdict an AI reached, the verdict
that was correct, and why. They exist to calibrate judgment, and they are the part of this
repository we would least like a competitor to have.

**They carry our experience, not our clients' information.** Every published
example carries `source: "synthetic_taste_v1"`. Each one takes a judgment call
we have actually had to make and writes it as a scenario for the purpose. That
is deliberate, and it is the stricter of the two options: client work is
confidential, and redaction removes a name without removing an identity, so a
case lifted from fieldwork stays recognizable to anyone who knows the company.
What is published here is the lesson: the distinction being drawn and the
reasoning behind it. The companies, systems, numbers and people in the scenarios
are not real and are not any client of ours.

**Which way do they push?** 303 of them correct an AI that was too strict,
and 195 correct one that was too lenient. We are publishing that ratio
because anyone with the file can compute it, and because the honest reading is
not flattering by default: an auditor whose examples mostly teach "that is fine
actually" is exactly what the industry should be suspicious of after 2025.

Our answer is that the two errors are not equally common. An engine reading
evidence against written criteria over-flags far more often than it
under-flags, because it has no way to see that a missing artifact is covered
three ways elsewhere. Correcting that is most of the work. But the examples that
push the other way are the ones that matter for an opinion, so we deliberately
added to them rather than leaving the ratio where it fell, and we did not force
it to 1:1, which would have been its own fiction.

If you think a specific example softens something it should not, that is a
concrete thing you can point at. Open an issue with the control and attribute
id. That is the entire reason this file is public.

## Type II: complete populations, not samples

By default we do not sample. The data a modern company runs on is produced by
machines, so it can be verified at machine speed, all of it. The client's AI
retrieves the complete population for each control (every change, every
termination, every access review in the observation window). Completeness is
corroborated: recorded retrieval always, and reconciliation against an
independent second source wherever one exists. Then every item is tested,
deterministically where the evidence is structured, by calibrated reading where
it is prose, with every candidate deviation confirmed by the CPA before it
becomes an exception.

Sampling survives only where a population genuinely cannot be retrieved in
full, and the report discloses, per control, which lane ran. When sampling
does run, nobody picks: selection is seeded from a hash of the banked
population itself, so neither side can steer or re-roll it.

The full method is in [`method/type2-testing.md`](method/type2-testing.md):
population rules, the deviation rule and its worked examples, the fallback
table, and the selection algorithm. Most of the 79
per-item attributes say "for a sample of," as does scattered "sample" phrasing
in the control playbooks and pass criteria. Read against the method that wording
is wrong and the method governs; the texts are pending revision.

Any change to this method is published here before it is applied to an
examination. A rule that predates the engagement it governs is easier to trust
than one written after it.

## What is not here

The methodology is here in full. The software is not. Chiaro, our platform at
app.chiarohq.com, is a product: it is what makes this method fast to run, and
this repository is not its source code. Everything the method itself consists
of is on these pages: what we test, what counts as evidence, and how each call
is made.

There is one deliberate omission inside the method, and it is worth stating
plainly. A few of our checks work by reading what a client's AI reports and
refusing an answer that reads as a shortcut. The exact wording those checks
look for is not published, because publishing it would publish the way around
it. What they check, and why, is in `method/collection-rules.md`.

## Using it

Licensed CC BY 4.0. Use it, fork it, run your own readiness against it, build on
it, with attribution.

One thing it cannot give you: **anyone may use this methodology, but only a
licensed CPA firm may sign an opinion.** A SOC 2 report is an attestation under
AT-C 205, and the signature is the part that is regulated. This repository is the
work; the license to attest to it is separate.

## A first release

The control library and the calibration examples are a first release, a
starting point. The method grows as it is applied and challenged: controls get
added, attribute texts get sharpened, and a judgment call worth teaching
becomes a new calibration example. Expect this repository to keep expanding
and updating. The direction is in [`ROADMAP.md`](ROADMAP.md), and method
changes land dated and versioned in [`CHANGELOG.md`](CHANGELOG.md).

Suggestions are as welcome as corrections. Open an issue, or write to
cpa@chiarohq.com. A methodology nobody can check is not better than no
methodology, and that is the whole argument for publishing it.

Chiaro is a product of Y Assurance PLLC.
