# Changelog

Method changes are dated, versioned, and published here before they are
applied to an examination. Corrections that do not change the method (typos,
formatting, clearer wording with the same meaning) ship without a version, and
the commit history on this repo records every one of them.

## v1.14, 2026-10-26

The same 89 controls and 387 checks, with changes to 42 checks and 12 controls,
59 more worked examples, and one new tool.

- Governance reviews (GOV-02), security status in leadership updates (GOV-04)
  and a founder's own access self-review (IAM-09) follow the company's own
  schedule, at least once a year (our audit's own minimum), in place of a fixed
  quarterly cycle. A stricter schedule the company sets still applies, and a
  founder-run company's governance still needs its named outside reviewer,
  whose dated written reviews the evidence guidance now names beside a
  governance body's meetings.
- Founders and co-founders named in the company's ownership records are not
  new hires for background screening (PPL-01); anyone else starting an
  employee or contractor role counts, and dated history still has to show no
  other start.
- Changes (CHG-01): a small change, as the company's written policy defines
  one (it never touches sign-in and permissions, keys and secrets, customer
  data and the database, infrastructure and configuration, the deployment
  pipeline or third-party dependencies), may go live once it has gone through
  a pull request and its automated tests pass, when someone other than its
  author looks back on every small change at least once a month, or on the
  shorter schedule the policy sets, including whether it qualified as small
  (A6). A review recorded as a pull-request comment by another developer, or a
  review record naming the change, the reviewer and the date, counts as
  review, before or after the change ships; over a Type II period, a review
  after shipping is on record before testing of the period closes (A4). For
  readiness and Type I, A4 also passes a latest change whose look-back is not
  yet due, where the process reviews changes after they ship in a look-back
  due at least once a month (for example for small changes, or for every
  change on a two-developer team), covering every change since the last one,
  and every look-back that has come due is on record. Where a team has fewer
  than 3 developers and no other developer can review a change, documented
  compensating controls addressing the same risks, such as required automated
  tests and a periodic after-the-fact review by a second person, are
  acceptable, and over a Type II period they operate (A4). A3's questions ask
  whether a change was documented on or before it was implemented, and leave
  who reviewed it to A4.
- Production access limited to named people, each with a documented need, and
  protected by MFA counts as restricted where everyone with non-production
  access also needs production (CHG-03 A3).
- A documented and approved schedule of at least once a year (our audit's own
  minimum) that meets applicable legal, contractual and regulatory
  requirements is adequate on its own for reviewing the Code of Conduct
  (GOV-01 A7), renewing its acknowledgments (GOV-01 A9) and reviewing and
  approving policies (POL-01 A5); a longer interval still needs a cited
  requirement or inspected safeguards. Manual credential rotation is checked
  against the company's own schedule, at least once a year, and short-lived
  credentials or a configured expiration pass (IAM-08 A2).
- A dependent vendor that offers no SOC 2 report or equivalent, or whose
  report the company has not obtained, can be covered by a documented
  alternative assessment (a completed security questionnaire, a review of the
  vendor's published security documentation, or controls the company runs to
  cover the gap) that records the risks assessed, the conclusion, the
  vendor-operated controls relied upon and the company's own responsibilities.
  It stands in for the report's review notes and has no report period to test
  (VND-02 A2, A3, A4). A SOC 3 is named among the equivalent attestations.
- What a provider runs inside its own service, with no setting the company can see
  or change, is shown by the provider's own statement (its SOC 2 report, published
  security documentation, or a written answer from the provider) naming what the
  check asks; over a Type II period, by its report covering the period with a
  bridge letter for any gap. What the company can see or set still needs its
  configuration. This applies to encryption at rest, algorithms and key lengths,
  key storage and key rotation (automatic, or dated within 12 months) (DAT-01 A1,
  A3 to A5), audit logs a provider keeps that customers cannot edit or delete
  (MON-01 A4, MON-03 A3), and backups and redundancy or failover a provider runs
  (BCP-02 A4, A5).
  For encryption in transit, the endpoints in scope are the company's own,
  wherever they are hosted; a provider's own servers that the company only
  connects to are the provider's (DAT-01 A2).
- A company with no endpoint or TLS setting of its own shows encryption in
  transit by scans of the providers' endpoints it connects to, or by those
  providers' own statements (in their SOC 2 reports, published security
  documentation or a written answer from each provider) that they encrypt
  those connections with TLS; over a Type II period, a provider's statement is
  its report covering the period, with a bridge letter for any gap (DAT-01 A2).
- For readiness and Type I, a practice adopted partway through passes from its
  written adoption date, once its steps are in place, when every case since
  followed it (or the record of such cases shows none since) and the earlier
  cases are named: approval of access grants (IAM-03 A4), offboarding
  timestamps (IAM-04 A5), a pre-engagement vendor assessment (VND-01 A6),
  review of a new subprocessor (VND-04 A2), notice of subprocessor changes
  (VND-04 A3), and deletion within the defined window, with no data from an
  earlier deletion left past its window (DAT-05 A3). Type II still tests every
  case in its period.
- Also: at a hosted AI provider, the retention that applies passes when it is
  no longer than the company's defined period (it need not match it), shown by
  the account setting, the provider's business terms for the account type or a
  zero data retention arrangement on record (a signed one counts), and a
  longer fixed provider period, such as an abuse monitoring window, passes
  when documented and permitted by the company's approved retention policy,
  its customer commitments and applicable law (AI-02 A2). A configured
  lifecycle policy or deletion job passes before any retention period has
  expired; a manual process shows a deletion carried out once one has (DAT-03
  A4). Role assignments may match a person's documented responsibilities and
  business need where a job title does not describe them, and a team roster or
  access review record listing duties counts (IAM-06 A3). A written
  compensating control for a duty that is not separated names what it covers,
  who performs it and when, and must address that duty's risk (GOV-03 A4). One
  user is sampled per system that has its own login; a system reached only
  through an identity provider that enforces MFA is covered by the user
  sampled there, with the provider's enforcement evidence (IAM-02 A3). A
  documented assessment of the applicable legal and contractual duties to tell
  customers about subprocessor changes, and how they are met, counts as
  evidence for those notices (VND-04 A3). Workload pressure, when identified,
  is addressed as the company's own written policy requires (PPL-04).
- Worked examples grow from 945 to 1004. 343 of them correct an AI that was too
  strict and 215 one that was too lenient, and the README now says what each
  means.
- A new tool, `answer_audit_question`, answers a question the audit's own
  record raises before handover, from the company's files or in the user's
  words; `complete_step` waits until each is answered, and an answer never
  changes a check's status. At a readiness account's first scope confirmation,
  while it has no checklist, `confirm_scope` returns the questions still open
  (each tool category not yet answered, and the engineering team size when no
  answer settles it) together, one picker per question. `set_company_profile`
  records whether devices are managed by an MDM (`uses_mdm`, which decides
  between END-01 and END-03) and whether the company administers any server or
  cloud account itself (`self_managed_infrastructure`, which decides between
  the self-managed and managed-platform network, logging and backup controls),
  and the planned Type II observation window, which sets the log retention the
  log-retention checks ask for. `record_self_assessment` takes an `open_item`
  reason for a gap that cannot be fixed before the audit (it stays open and
  never counts as a pass), or `open_items` to add one to a saved gap.
  `record_policy_adoption` needs the `version_id` the user read, and
  `record_subservice_soc2` takes an obtained report's `evidence_id`, so the
  file travels at handover. `get_unresolved` lists a readiness account's
  unresolved checks with their reasons, a saved answer marked
  `given_under_earlier_wording` keeps its credit, `load_area` includes an
  audit's earlier conversation answers within stated limits (an incomplete
  view is never read as missing work), and `start_audit` quotes the remaining
  charges, accounting for an installment already paid. On a Type I audit whose
  scope confirmation has finished, the Chiaro team adds a system through
  `report_problem`.
- When the user says the evidence zip is uploaded, the AI calls `get_status`
  once, which compares the upload with every registered file and lists
  anything missing (`method/collection-rules.md`).

## v1.13, 2026-10-21

The same 89 controls and 387 checks, with clearer evidence guidance on 32
checks and in the collection guidance of 15 controls, one new tool, and what
the examining firm considers in judging a check.

- Managed platforms: the hosting platform's managed edge or its
  provider-enforced ingress restrictions count as boundary protection, with
  evidence that the protection is active and bypass paths are controlled
  (NET-02 A1), and where there is no customer-controlled network,
  provider-enforced isolation and access rules count when they show
  least-privilege paths between the relevant segments (NET-01 A3). A
  resilience consideration can be the provider's managed redundancy, the
  backup and restore approach, or a documented decision to run in a single
  region (BCP-01 A4), and backups can be stored in a separate availability
  zone (BCP-02 A4). The provider's built-in activity log on the current plan
  counts where it records sign-ins, access changes and admin actions, as does
  a paid audit-log add-on (MON-03 A1).
- Agreements accepted online: BAA terms the customer accepted electronically,
  with the acceptance date (HIP-01 A2), and a subcontractor BAA accepted in
  the provider's console or online terms, with the acceptance record (HIP-01
  A3).
- Small teams: in a team of one to three engineers, each engineer may hold
  production admin with a documented need for each account, and the CPA-review
  flag still applies (END-02 A2). Where headcount prevents separating duties,
  a risk assessment entry that evaluates this as a fraud measure and names the
  compensating review counts (RSK-01 A9). A confidential email alias reaching
  a named responsible person, such as an outside advisor, is a reporting
  channel (COM-02 A3). The outside reviewer can be one outside advisor, with
  no board seat or equity, and an independent board member counts too (GOV-02
  A6, A7); one person can cover GOV-02 A3, A6 and A7 and the independent
  evaluation check when they are independent of day-to-day management, do not
  build or run the systems, and are not responsible for the controls they
  evaluate.
- Records a company may already keep: a dated review that kept the incident
  response plan unchanged (INC-01 A3); a tabletop walk-through as the disaster
  recovery test (BCP-01 A6); a tabletop exercise the response team took part
  in, within the audit period, which also serves as their training record
  (INC-01 A6); dated manual checklists recording what was checked and found,
  as ongoing monitoring (EVL-01 A1); merged pull requests and release notes
  showing that affected personnel were notified or took part (POL-02 A4);
  dated completion confirmations (PPL-03 A4) and dated course records within
  the cycle the company's training requirement sets (PPL-03 A5); the building
  or coworking provider's visitor check-in records, with visitor name, date
  and host (NET-03 A4); short-lived credentials that expire on their own
  (IAM-08 A2); each device's automatic update settings, shown for each device
  under a written update rule (END-01 A4); and, where neither applicable law
  nor the company's commitments call for telling customers about subprocessor
  changes, a documented basis for that (VND-04 A3).
- Intervals: 12 months is named as our audit's own minimum for a recent record
  (RSK-01 A1 and A12, AST-01 A5, NET-03 A5, VUL-02 A1, BCP-01 A6, INC-01 A3
  and A6), and a year for manual credential rotation (IAM-08 A2); RSK-01 A1
  and A12, AST-01 A5, NET-03 A5 and VUL-02 A1 accept a shorter interval the
  company sets. Access reviews follow the frequency the company's own policy
  defines (IAM-05 A3), and red flags read against the company's own scanning
  interval (VUL-01), its backup policy or recovery objectives (BCP-02) and its
  written policy on workload (PPL-04). A configured log export counts when its
  destination keeps logs for the longer of the policy period and 90 days
  (MON-01 A3).
- What a check covers: one sampled user per in-scope system, drawn from the
  groups the applicable MFA requirement covers (IAM-02 A3); separate
  development and production environments, with or without a staging
  environment (CHG-03 A1); a privacy owner when the Privacy criteria are in
  scope (GOV-03); a data handling level that may state that an area has no
  special requirement, such as disposal of public data (AST-02 A2); single
  sign-on, when any accounts still outside it are listed and justified or the
  evidence shows there are none (IAM-01 A4); and, for a team without device
  management, per-device endpoint encryption with evidenced removable-media
  restrictions, encryption or effective compensating protection (DAT-03 A3).
- Red flags and examples of weak evidence now match their checks: MFA evidence
  that leaves out enforcement or a permitted, evidenced exception (IAM-02),
  capacity (AVL-01), validation at some processing entry points but not others
  (INT-02), error detection, automated or manual, and code that is not
  deployed or not active in production (INT-03), and direct write access to
  the data store (INT-05).
- A new tool, `remove_system`, takes a system added by mistake off a readiness
  account's list, with the reason recorded; on an audit, or once one has
  started, the Chiaro team makes that change through `report_problem`.
  `set_company_profile` refuses a not-applicable confirmation for a control
  that follows a trust services category, the HIPAA add-on or physical
  facilities, and names the answer that decides it. `record_self_assessment`'s
  `fix_description` asks, on a gap, for what to produce or start doing.
  `submit_population` takes a pull too long to paste as saved files, and for
  dated events expects rows from within the window while raw keeps each record
  the command returned.
- `method/readiness-and-independence.md` states that, in judging a check, the
  examining firm considers what SOC 2 itself and the company's own written
  policy require.

## v1.12, 2026-10-18

The same 89 controls and 387 checks, with clearer evidence guidance on eight
checks, and how an audit's scope is set.

- Agreements: for a hosted AI model provider (AI-01 A2) and for a vendor's
  confidentiality and data processing commitments (VND-01 A8, A9), the
  governing written agreement or addendum can be signed, or validly accepted
  electronically as part of the business terms for the company's account,
  with evidence that the terms cover that account.
- Training on submitted data (AI-02 A1): where a provider has no setting for
  it because its commercial terms exclude training, a screenshot from the
  company's own account showing it is on those terms, together with the
  terms' no-training clause, counts. So does a signed or electronically
  executed no-training addendum. A provider's public policy page on its own
  does not.
- AI-03 A2 accepts a gateway or filtering service, or code that builds each
  provider request only from the fields the written data boundary permits,
  with every provider call going through it.
- BCP-02 A5 accepts redundancy across zones switched on for the company's own
  managed service, such as a managed database set to fail over to another
  zone, alongside standby and failover configurations. A documented manual
  failover procedure is flagged for review.
- CHG-04 A3 accepts a free analysis tool or a security linter (for example
  Semgrep Community Edition, Bandit or eslint-plugin-security) run in CI or in
  the development workflow with its results available, as well as a
  commercial scanner.
- END-02 A4 names, as its evidence, the CI/CD or repository settings that
  hold every production deploy until it is reviewed, approved and tested (for
  example branch protection requiring a pull request review and passing
  checks), shown by a pulled configuration, an export or a screenshot. A
  written deployment procedure can sit alongside them.
- Processing integrity: PI1.2 and PI1.3 carry the text the Trust Services
  Criteria give them, on system inputs and on system processing, and the risk
  and intent notes for PI1.1 to PI1.4 each describe their own criterion.
- A new account's first setup asks whether the product sends customer or
  company data to an AI model service run by another company. "Yes" keeps the
  AI governance controls (AI-01 to AI-04) in scope whatever the provider is
  called; "no" leaves them out unless an AI model service is on the system
  list.
- On an audit, the first scope confirmation sets the audit's scope and needs
  the penetration testing answer first. After that, an answer that would
  change which checks apply is refused; a recorded answer that is wrong is
  reported through `report_problem`, and the audit team updates the scope and
  the checklist together.
- A new tool, `update_system_description`, replaces the recorded description
  of a system without changing its classification, the scope confirmation or
  the checklist.
- `method/tools.md` lists each tool's arguments with the type, whether the
  argument is required, and the description the server sends with it.

## v1.11, 2026-09-23

The same 89 controls, with clearer checks and more worked examples.

- 387 checks (was 369). The 19 new checks are parts of earlier checks, and each
  one names the check it came from in `split_from`. One check is retired:
  POL-01 A1 asked about eight policy areas at once and is now eight checks, A7
  to A14.
- 95 checks have revised pass and fail wording.
- 945 worked examples (was 552). 42 checks carry separate Type I and Type II
  guidance.
- 41 checks state their rule in the check text itself, with any added rules in
  `visible_rules`; their `pass_criteria` field is empty.
- Readiness progress states its basis: evidence checked, earlier completion
  kept with its recorded basis and date, or the customer's explicit
  confirmation, labelled as evidence not checked. None of these is an audit
  conclusion.
- When a user says the thing a criterion names exists somewhere else, the
  readiness check is `unresolved` until that thing is inspected: it earns no
  credit, and it is not a `gap` unless the inspection shows the requirement is
  not met. This replaces v1.8's description of that answer as a `gap`.
- Work saved before this version is kept: earlier completions keep their
  recorded basis and date, and existing evidence is considered before asking for
  more. An examination already under way keeps the rules and evidence it started
  with.
- Type II: event populations are kept separate from each check's own sample,
  and change history can connect a configuration snapshot to the examination
  period. Type II keeps two collection rounds and automatic email
  notifications.
- New: `method/readiness-and-independence.md`, on what readiness measures and
  who makes which decisions.

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
