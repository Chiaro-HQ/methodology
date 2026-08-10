# How a Type II is tested

A Type I asks whether controls were suitably designed as of a date. A Type II
asks whether they operated over a period. The operating question is where
sampling has always lived, and it is where we depart from standard practice:

**We test complete populations by default. Sampling is a disclosed exception,
used only where a population genuinely cannot be retrieved in full.**

Audit sampling exists because human hours are expensive. When testing means a
person reading records one at a time, checking 25 of 214 changes is a
reasonable economy, and the profession built careful machinery around it:
selection methods, deviation projection, sampling risk. All of it manages the
consequences of not looking at everything.

The evidence in scope here is produced by machines, and it can be verified the
same way, at machine speed. Then the economy stops making sense: if the
complete population is retrievable and every item can be checked, checking a
subset is a choice, not a constraint. So the default is a census (every
change, every termination, every access review in the window), and what a
deviation means becomes a fact about the period, not an inference from a
sample.

## Populations

Every period test starts from a population: all occurrences of the thing the
control governs, over the observation window. Which raises the question a
critic should ask first: the population is retrieved from the client's own
systems by the client's own AI, so how do we know it is complete? A flawless
census of an incomplete population proves nothing. Worse, it looks
exhaustive.

Three rules, in descending order of strength, and the workpaper records which
one applied:

1. **Recorded retrieval, always.** A population arrives as the exact query or
   command that produced it plus its unedited output, captured verbatim,
   re-runnable. "The list of terminations" is not a population; "this HR
   system export, run like this, on this date, returning these rows" is. This
   does not make fabrication impossible; it makes it deliberate, a recorded
   act rather than a soft omission, which is the position every auditor has
   always been in with management, made explicit.

2. **Reconciliation wherever an independent second source exists.** People
   populations reconcile against identity systems (the HR roster against the
   IdP's user list), change populations against deployment history,
   termination lists against disabled accounts. A mismatch is a stop, not a
   footnote: the population definition gets corrected, the pull re-runs, and
   the mismatch itself is recorded.

3. **Where no second source exists,** the recorded retrieval stands together
   with a written representation from management, and the workpaper says so
   plainly rather than implying corroboration that did not happen.

An empty population gets the same treatment. "We had no security incidents"
is not a sentence we accept; it is a recorded pull of the incident tracker,
filtered the same way as everything else, returning zero rows. Zero is a
number with evidence behind it. A control whose population is genuinely empty
for the period is reported as not occurring, which is distinct from
operating effectively, and distinct from failing.

## How a census is evaluated

Testing everything is only credible if the per-item check is reliable, so the
evaluation is layered by how much judgment each check needs:

- **Deterministic checks** cover everything structured: dates against
  deadlines, approver against author, status fields against required values.
  Was the change approved before merge? Is the reviewer someone other than
  the author? Was access removed inside the window? These are computed, not
  judged, and they are exact at any population size.
- **Read checks** cover evidence that is prose or documents: a background
  check report, meeting minutes, an incident writeup. An AI reads each item
  against the attribute's written pass criteria, calibrated by the worked
  examples in this repository. Reading is where error lives, so that is where
  the human checks concentrate.
- **Every candidate deviation is confirmed by the CPA before it becomes an
  exception.** The machine flags; a person concludes. A false flag dies at
  review and never reaches a report.
- **A slice of clean items is re-checked as quality control.** Sampling does
  not vanish from our world entirely. It leaves the audit and returns as
  internal QC over the evaluation itself, which is where it belongs.

## When we sample anyway

Some populations cannot be practically retrieved in full: paper records at
volume, a system with no export path, evidence held by a third party that
answers slowly. For exactly those, a sampling lane exists, and the report
discloses, per control, which lane ran. "Complete population" is the default;
"sampled, full retrieval impracticable" is the exception, stated in the
deliverable, which is what keeps the lane honest and rare.

Impracticable means the population cannot reasonably be retrieved in full. It
does not mean retrieval is tedious. Twenty-three contractor agreements as PDF
scans are a census, not a sampling candidate.

Sample sizes follow the frequency of the control, as fixed values rather than
ranges, so there is no per-engagement size discretion:

| Control frequency | Sample size |
|---|---|
| Annual | 1 |
| Quarterly | 2 |
| Monthly | 2 |
| Weekly | 5 |
| Daily | 25 |
| Many times a day | 40 |
| Ad hoc / on occurrence | 25 above 250, 15 for 51-250, 8 for 11-50, all items at 10 or below |

If the population is at or below the sample size, every item is tested. A
control that carried a deviation in the prior period takes the next larger
sample size.

### Selection: nobody picks

Who chooses the sampled items matters more than how many. A client choosing
which items get tested is not being audited, and a human auditor picking
"haphazardly" is a politeness for "without a recorded method." Here selection
is deterministic, and nobody can steer it: not the client, not us, not either
side's software.

- The seed is derived, never chosen:
  `seed = SHA-256(engagement id | control ref | SHA-256 of the banked population)`,
  the three fields joined with the literal `|` character.
- Selection is stratified over the period: the population is sorted by
  occurrence date and split into K contiguous runs of as equal size as
  possible, one run per sampled item; a seeded draw (`SHA-256(seed | run
  index)`) takes one item from each run, so the sample spans the window by
  construction.
- The workpaper records the population hash, the seed, and the selected
  items.

The property this buys: the selection is fully determined the moment the
population is banked. We cannot re-roll it looking for a friendlier draw. The
client cannot influence it without changing the population, which changes the
hash, which is recorded. Anyone holding the workpaper can re-derive the same
selection. Try to re-roll it; you can't.

## Deviations

A census changes what a deviation is. There is no sampling risk, no
projection, no sample expansion: D deviations out of N occurrences is a fact
about the period. The question left is what the facts mean, and the rule is
published:

- **Zero deviations:** the control operated effectively.
- **Any deviation:** its root cause is investigated and recorded, per item.
- **Isolated deviations are disclosed as exceptions**, with the control still
  concluded effective. Isolated means the deviations trip none of the
  systematic markers below: each has an identified cause, no cause recurs,
  nothing concentrates in a way that defeats the control's purpose, and the
  practice otherwise ran. A bounded episode, one bad week with a named
  reason, is the commonest form, not the definition. Isolated misses in an
  otherwise working practice are an exception, not a gap.
- **Systematic deviations make the control ineffective.** The systematic
  markers, any one of which is enough: the same root cause recurring;
  concentration in one person or subsystem in a way that defeats the
  control's purpose; a deviation rate above five percent of a population
  large enough for rates to mean anything; persistence across the period
  rather than a bounded episode; or a design-level cause, where the control
  as designed could never have caught the case.

**Small populations are judgment, and we say so.** For populations under ten
(quarterly reviews, annual tests), rates are meaningless: one late review
out of four is 25%, and it is also just one late review. Those calls are made
by the CPA against the published factors (late or absent? bounded or
recurring? does the miss defeat the control's purpose?) and the reasoning is
recorded.

Two asymmetries, both deliberate:

- The CPA may always conclude **harsher** than the rule.
- Concluding **softer** than the rule requires a recorded reason, which
  becomes part of the workpaper.

And one rule with no exceptions: **remediation during the period never erases
a deviation.** A termination caught late and fixed in March is still a
deviation in the report, disclosed alongside management's remediation, so
the report shows both the miss and the response. A report whose exceptions
were quietly repaired away is the industry failure this repository exists to
answer.

## Worked examples

The deviation rule has judgment at its edges, so the edges get calibration
examples, the same way the attribute criteria do. Each records the facts, the
tempting conclusion, and the correct one. Every scenario below is invented.

**1. Clustered misses with a cause.**
214 changes in the window; 3 merged without review, all inside one week in
November, same author, while the second reviewer was out. Clean the other
eleven months.
*Tempting:* three deviations, so expand scrutiny and question the control.
*Correct:* isolated. Bounded in time, identified cause, practice otherwise
working. Control effective; 3 exceptions disclosed with dates and
management's response.

**2. Spread misses, low rate, no common cause.**
Same 214 changes; 3 unreviewed, one each in February, May, September, three
different authors, each individually explained.
*Tempting:* spread across the period means systematic, so ineffective.
*Correct:* spread alone is not a systematic marker. No common cause, no
concentration, rate far below any threshold at this size. Control effective;
3 exceptions disclosed. Systematic is about cause and pattern, not calendar
spacing.

**3. Rate settles it.**
40 of 214 changes unreviewed, spread across every month, many authors.
*Tempting:* each one had a story, and management remediated in the final
quarter.
*Correct:* 18.7% is not exceptions; it is how the period actually operated.
Ineffective. The remediation is disclosed as management's response and
changes nothing about the period.

**4. Late is not absent.**
Quarterly access reviews: four due, four performed, one completed five days
after quarter end.
*Tempting:* 1 of 4 is a 25% deviation rate, so ineffective.
*Correct:* small-population judgment. The review happened, days late, once,
practice otherwise on schedule. Exception disclosed; control effective. Rates
are not the tool below ten occurrences.

**5. A missing quarter is not late.**
Quarterly access reviews: four due, three performed. One quarter simply has
no review.
*Tempting:* three of four, mostly working, call it an exception.
*Correct:* the control did not operate for a distinct slice of the window.
This is the hard edge of the small-population clause: ineffective, unless a
recorded, compelling reason exists (a compensating review that covered the
same ground), and that reason becomes part of the workpaper.

**6. The empty population.**
The emergency-change attribute; the change log, filtered to emergency
changes, returns zero rows, and the filter plus output are recorded.
*Tempting:* nothing failed, so pass.
*Correct:* not occurring, with the recorded zero as its evidence. Not a pass:
the control was never exercised, and the report says so. An unrecorded "we
had none" is not a zero; it is a missing population.

**7. The drawer is not impracticable.**
23 contractor agreements exist as scanned PDFs in a shared drive folder. No
system of record, no API.
*Tempting:* no export path, so the sampling lane applies. Pick a handful.
*Correct:* census. Twenty-three files can be attached in full. Impracticable
is about what cannot reasonably be retrieved, not what is tedious. The
sampling lane is not a convenience lane.

**8. The reconciliation mismatch.**
The HR export lists 3 terminations for the window; the identity provider
shows 4 accounts disabled.
*Tempting:* test the 3, note the fourth as probably a service account.
*Correct:* stop. The fourth was a contractor who never entered the HR system,
which means the population definition, terminations per HR, was wrong, not
the count. The population becomes all access-holding departures, the pull
re-runs, and the mismatch plus its resolution is recorded. A census of the
wrong population is exactly the failure this rule exists to catch.

## A note on the attribute language

When this page was first published (2026-07-27), 79 attributes still carried
sampling-era wording ("for a sample of X," "all sampled individuals") that
this page overrode: population testing governed regardless of what the texts
said. That revision landed on 2026-08-06. The texts now read "for each X" /
"for all X," the period-form pass criteria say "every X in the period," and
the collection playbooks ask for the population, not a sample of it. Each
record-checked attribute also now carries a `frequency` field, the input to
the published fallback-selection table for the rare population where a
complete pull is impracticable. The `sample_eligible` field name and the
`sample` attribute type remain as machine identifiers; prose is
population-first throughout. The one deliberate survivor: DAT-04's "sampled
data," where the word means data-subsetting for non-production environments,
not audit sampling.

## Changes to this method

Any change to this method, and the reason for it, is published here before it
is applied to an examination. A rule that predates the engagement it governs is
easier to trust than one written after it.
