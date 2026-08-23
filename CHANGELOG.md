# Changelog

Method changes are dated, versioned, and published here before they are
applied to an examination. Corrections that do not change the method (typos,
formatting, clearer wording with the same meaning) ship without a version, and
the commit history on this repo records every one of them.

## v1.7, 2026-08-23

Empty populations say what they mean, and a mislabeled record can be corrected.

- Criteria for events that may never have occurred at a young company now say
  so explicitly: provisioning events, terminations, new hires, vendors with
  confidential or personal data access, dependency advisories, and model
  training runs each carry "if none occurred as of the test date, pass by
  default", matching the clause their sibling criteria already carried. An
  empty population stated plainly is a pass; it was never meant to read as a
  gap, and inconsistent wording made it grade differently in different tools.
- Five coverage checks whose own pass criteria name a document as the passing
  artifact (an incident response plan's own review date on its cover page, a
  risk assessment on file, a recovery plan's version history, a documented
  management review) now accept a document as their primary evidence kind,
  with records and system output still accepted as before. Nothing accepted
  yesterday is refused today; the change is strictly widening.
- A new collection tool, `reclassify_evidence`, corrects the evidence-type
  label on a file already submitted: a dated write-up of a completed
  activity is a record, not a document, and the label decides which checks
  the file can satisfy. The evidence tier is still derived server-side from
  the original submission's own signals, so a relabel cannot claim a tier
  the submission could not.

## v1.6, 2026-08-20

Governance for a company of one.

- Six worked calibration examples on GOV-02 and GOV-04 record how a
  one-person company meets the oversight criterion: the founder is the
  governance body, and one named person outside day-to-day work (an advisor,
  an investor, a mentor, a fractional security lead) receives a written
  security and compliance update each quarter. The update thread is the
  agenda, the minutes, and the record of decisions and follow-up.
- One of the six fails: a founder who sometimes consults unnamed investor
  friends, with nobody named in any document and no update ever written. The
  independence element is a named person and a written cadence; until both
  exist the flag for a very small entity stays for CPA review.
- No pass criteria change. Nothing here passes the independence check with no
  outside person. The diff is in this repository.

## v1.5, 2026-08-20

Calibration for a company of one.

- Fourteen worked calibration examples across thirteen checks record how a
  one-person company meets controls that are usually written for a team:
  background checks with no hires yet, a founder's own role description and
  dated annual self-review, a named backup with runbooks for continuity,
  provisioning and offboarding where the only events are a contractor's,
  privileged access a founder grants themself with a dated justification, one
  owner account plus scoped service accounts as role-based access, a solo
  developer's emergency-change and architecture-review records, separation of
  duties evaluated as infeasible with the compensating controls named, a
  vendor's accepted standard terms as the contract's security clauses, and an
  independent advisor's written annual review as an independent evaluation
  instrument.
- One of the fourteen fails: a continuity plan that lives only in a spoken
  arrangement.
- No pass criteria change. The examples add pass paths at the size of one
  person; they do not remove a requirement. The diff is in this repository.
- [method/tools.md](method/tools.md) catches up with three tools the server
  has exposed since 2026-08-13: one that asks how else a security setting could
  be changed outside the normal process (bypass routes), one that records what
  the report assumes the client's customers and cloud providers do
  (complementary controls), and one that records the SOC 2 reports held for
  subservice organizations. EVL-01/A3's typical-evidence line now lists the
  instruments the independent-evaluation page describes.

## v1.4, 2026-08-12

Independent evaluation, and the penetration testing question.

- [method/independent-evaluation.md](method/independent-evaluation.md) states
  what CC4.1 requires, the five questions an instrument has to clear to satisfy
  EVL-01/A3, and what does not count. No trust services criterion requires a
  penetration test. A penetration test is one instrument that satisfies the
  requirement that does exist.
- EVL-01/A3 gains worked calibration examples in both directions, including two
  that fail: a tool a company runs on itself, and a review performed by the
  provider that operates the environment being reviewed.
- VUL-02, penetration testing, becomes an optional-practice control. It applies
  where management runs a penetration testing program and is out of scope where
  management does not, through the same conditional applicability this library
  already uses for practices that some companies run and others do not. CC7.1
  stays carried by VUL-01 and VUL-04, both always in scope.
- The pass criteria for EVL-01/A3 are not changed by any of this. The diff is
  in this repository.
- [method/tools.md](method/tools.md) picks up two tools the server exposes: one
  that records a prior report from another firm, which seeds scope and raises
  the amount of testing while carrying no conclusion, and one that reconciles
  the recorded system scope against an independent enumerator.

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
