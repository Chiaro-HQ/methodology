# Independent evaluation

A penetration test is not required. No Trust Services criterion requires one.

CC4.1 asks whether the entity performs ongoing and separate evaluations to
ascertain whether the components of internal control are present and
functioning. That is the requirement, and no particular product satisfies it by
being purchased. In this library it is tested at EVL-01, attribute A3: an
independent evaluation performed within the last year, by a party not
responsible for the controls evaluated.

Those pass criteria are not adjusted to accommodate a cheaper instrument. The
commit history of this repository is public, so that is checkable rather than
asserted. What the instrument question changes is which artifacts can satisfy
the attribute, never the bar an artifact has to clear.

## Where the belief comes from

Penetration testing appears in the Trust Services Criteria once: inside a point
of focus under CC4.1, in a list of examples that also names independent
certification made against established specifications and internal audit
assessments.

A point of focus is an aid to judgment, not a requirement, and the criteria say
so in their own words. Use of the trust services criteria does not require an
assessment of whether each point of focus is addressed (TSP 100 paragraph .04).

Controls are management's to design. Where management runs no penetration
testing program, there is no penetration testing control to test, and CC4.1 is
met through the evaluation management does perform.

## The test

Five questions decide whether something satisfies EVL-01/A3:

1. Who performed it, and is it a party rather than a tool?
2. Do they operate, build or manage any of the controls they evaluated?
3. Did they evaluate the control system, or only the attack surface?
4. Is there a dated record stating what was looked at and what was found?
5. Did management respond to the findings?

Anything clearing all five satisfies the attribute, whatever it is called. A
penetration test clears it. So do other things, which is the entire point of
the question.

The evaluator also has to be plausibly competent for what they evaluated. The
basis for that, whether a role, a credential or a track record, belongs in the
engagement file next to the artifact, per client, in that client's own terms.

## What does not count

- **A tool the company runs on itself**, however capable, and including AI
  tools. Independence attaches to the party, not to the instrument. AI is
  permitted as the means. The evaluator is whoever signs the result and stands
  behind it.
- **A self-assessment by someone who operates the controls.** A careful,
  signed, thorough review by the engineer who owns provisioning fails question
  2, and nothing else about it can rescue it.
- **A review by a provider that runs the environment it reviewed.** Being a
  separate company is not the test. Being free of responsibility for the
  controls is the test.
- **A certificate or a badge with no scope, no method and no findings.** An
  evaluation states what was examined and what was wrong with it.
- **A conversation.** With no dated artifact there is nothing to examine.

## This adds a requirement, it does not remove one

A company with no independent evaluation of any kind has an open finding, and
choosing a different instrument does not make that go away. A report bought for
the file is often assumed to discharge the criterion on its own; it discharges
it only if it clears the five questions, and clearing them is harder than
producing a receipt.

## What this page does not decide

Whether a company should run a penetration test is a security question, and
this is not the document that answers it. A penetration test finds things that
control evaluation does not, which is a reason to run one that has nothing to
do with the criteria.

Customer contracts are a separate obligation. Enterprise agreements commonly
require annual penetration testing by name, and silence in the criteria does
not erase a commitment a company has already made. Read the contracts first.

## In the library

- `EVL-01` carries the requirement and is in scope for every engagement.
- `VUL-02`, penetration testing, is an optional-practice control. It carries
  `applicability: {"condition": "pen_test_program"}`, so it applies where
  management runs a penetration testing program and is out of scope where
  management does not. That is a scoping determination, not a finding.
  Conditional applicability is how this library handles every practice that
  some companies run and others do not, and `framework/controls.json` shows
  the other controls that use it.
- Taking VUL-02 out of an engagement leaves CC7.1 carried by `VUL-01`,
  vulnerability scanning, and `VUL-04`, dependency and platform update
  management, both always in scope. CC4.1 stays carried by `EVL-01`, also
  always in scope. No criterion loses coverage.
- The EVL-01/A3 entries in `framework/calibration-examples.json` are the worked
  calls, in both directions, including the submissions that fail.
