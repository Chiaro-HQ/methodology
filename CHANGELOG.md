# Changelog

Method changes are dated, versioned, and published here before they are
applied to an examination. Corrections that do not change the method (typos,
formatting, clearer wording with the same meaning) ship without a version, and
the commit history on this repo records every one of them.

## v1.10, 2026-08-28

The capture backstop, and the answers that are not a clean yes or no.

- `method/collection-rules.md` gains two sections that describe how collection
  is done rather than how the software behaves: the reflection pass that runs
  before a phase is closed, which re-reads the session for any claim a person
  made that was never captured and for any qualifier ("but only on prod",
  "informally") dropped between what was said and what was recorded; and the
  rules for answers that do not fit a clean yes or no. An informal practice is
  recorded in the person's own words rather than paraphrased. An "I'm not
  sure" is pushed once toward a real answer, and if it stays unsure the
  practice is recorded as absent rather than guessed at. A "we do this for
  some systems but not others" is asked as a three-way question so partial is
  an answer the record can hold.

## v1.9, 2026-08-28

The per-system method, and the commands that pull the evidence.

- `method/collection-rules.md` gains eight sections on what each kind of
  system owes and how to reach it. The three-test probe that classifies a
  vendor nobody has met before, on data, commitment and configuration. Why a
  productivity bundle is out of scope while the identity provider and the
  device manager inside it are in. What a system's own drill looks like end to
  end, and the preferred path and the fallback path for the systems most
  companies actually run. What the minimum output for an in-scope system is.
  How the controls that apply to a system are turned into questions a person
  can answer. Why a discovery is recorded the moment it happens rather than
  reconstructed later. And the planning-time reconciliation that stops a
  template, a demo pack or another company's evidence from entering an
  engagement at file one, which is the check that has to run before the first
  file rather than after the eightieth.
- `method/commands.md` is new: the 72 read-only commands, across 22 systems,
  that pull configuration evidence, each mapped to the Trust Services Criteria
  it helps satisfy. A `command_ref` in `test_attributes.json` that names one of
  these systems resolves here. Every command reads configuration and changes
  nothing, and no credential appears in the library as a value.

## v1.8, 2026-08-28

What a check accepts, and who decides that something does not exist.

- The device configuration checks (END-03: disk encryption, screen lock,
  automatic updates) accept a screenshot or an export of the device's own
  configuration output. A written assertion that the setting is on does not
  close them, which is what their pass criteria require: the proof is the
  device's own output, not a statement about it. Audit log retention
  (MON-03/A2) reads the same way. The check asks what each provider's
  retention setting shows, so it accepts a screenshot or an export of that
  setting, and a policy document, which cannot show a provider's
  configuration, no longer closes it.
- `absent` is the user's answer, never an inference. When the thing a
  criterion names is nowhere in the evidence held, the question goes to the
  user before anything is filed: "do you actually do this?" for a practice,
  "does this exist anywhere, signed, published or defined?" for a named
  thing. "We never created one" is `absent`, and the work is to build it.
  "It lives somewhere else" is a `gap`, and the work is to find it. An empty
  search establishes neither, and a document that discusses a subject is not
  the thing a criterion names: a policy about vendor reviews is not a vendor
  review, and a policy describing data processing is not the signed
  agreement.
- A drafted document is not an adopted document. A new tool,
  `record_policy_adoption`, records the user's own words on whether they
  adopt a document that was drafted for them, attributed to the person who
  said it and dated by the server, on the same footing as any other
  statement a person attests to. It is their decision to make and the AI
  may not make it for them.
- `method/collection-rules.md` publishes further sections of the rules
  the collection runs under: the corroboration floor (every in-scope item
  ends as a real artifact in the right lane or as a logged gap in the user's
  own words, and what a user says is context that directs collection rather
  than something that completes it), the input and output contract (every
  evidence item captures the literal command, click path, URL or query
  alongside the literal response it produced, and is incomplete with either
  half missing), and the file format each kind of evidence takes. These
  were always part of what this repository set out to publish.

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
