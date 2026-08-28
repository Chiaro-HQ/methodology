# The tools

The 49 tools a connected client's AI can call. Generated from the
live server, so this list is exactly what a client sees, including the
descriptions the AI reads to decide what to do.

The descriptions are the method. The product is that your own AI runs our
procedure, and these are the instructions it runs.

## `add_system`

Add a system, tool, or service to the engagement.

Just provide the name and what you use it for. Chiaro determines
whether it's in scope based on SOC 2 scoping criteria:
- Does it process or store customer data?
- Is it necessary for the product to function?
- Does it implement security controls for the product?

Common systems (AWS, GitHub, Okta, etc.) are automatically classified.
For less common tools, describe what you use it for.

Args:
    name: System name (e.g. "AWS", "Okta", "GitHub", "Datadog")
    description: What you use this system for. Helps with classification
        for less common tools. (e.g. "internal admin dashboards that
        access production data")
    category: Optional category hint (e.g. "infrastructure", "auth", "monitoring")
    in_service_date: YYYY-MM-DD, and ONLY for a system that went into
        service after a Type II observation period had already started.
        Ask the user when they started using it. A system adopted
        mid-period was not there for the earlier part of it, so the
        report says when it was placed into service and its records only
        cover from that day.

## `answer_question`

Submit an answer to an audit question.

For process questions (how your company handles incidents, risk, etc.):
the answer should come from the user, not from documents you've read.
If you found relevant info in their docs, present it to the user and
ask them to confirm before submitting.

For technical questions (what tools are configured, what settings exist):
you may answer directly from system evidence.

The AI classifies each control in the topic as Absent or Exists based
on the answer. After 2 rounds per topic, remaining controls default
to Exists (we'll verify with evidence).

Args:
    topic_key: The topic identifier (from get_questions output data)
    answer: Your answer — describe how your company handles this area.
        Be specific about tools, people, frequency, and documentation.

## `answer_questionnaire`

Answer a customer's security questionnaire from the company's examined facts.

Use this when the user has a security questionnaire, vendor risk
assessment, or due diligence form from a customer or prospect.

HOW THIS WORKS. You hold the file, not us. Open the buyer's file locally,
read out every question in order, and pass the question TEXT here. Do not
send the file, the buyer's name, or anything else. You get back, per
question, either an answer you can write straight into the copy, or an
item that needs the user's input with a ready-made picker.

Then write a COPY of the buyer's file with the answers filled in. The
original is never touched and the buyer's format, wording, and structure
are never altered. Fill only. Anything still open stays blank until the
user answers it.

Answer states you will get back:
  backed            established by the company's independent examination.
  company_statement the company's own disclosure or a previously given
                    answer. True, but not something we examined.
  needs_input       we have nothing that answers it. Ask the user.

needs_input is a normal and frequent outcome, not a failure. An honest ask
beats a plausible guess in a document going to a customer.

Args:
    questions: Every question from the buyer's file, in document order,
        as plain text. Include the buyer's own numbering if it has one.
        Up to 300 per call.

## `complete_step`

Complete evidence collection and hand off to CPA review.

GATED: collection must be complete before this succeeds. The gates:
  1. Every in-scope attribute is in a terminal state (collected, absent, or N/A)
  2. Every in_scope_tool system has at least one real (non-conversation)
     evidence artifact referencing it
  3. Every collected sample-type attribute has a real artifact, not just
     a verbal submit_answer
  3b. Type II only: collection phase 2 has opened AND every in-scope
     population has been handed over
  4. Every subservice org has its SOC 2 status recorded (yes or no)

On success, marks the engagement as awaiting_upload. The client uploads
their evidence zip, which flips status to ready_for_review.

## `confirm_scope`

Confirm and lock the audit scope. This advances the engagement to scanning.

Runs the full scoping engine:
1. Locks the scope (no further changes)
2. Determines which controls are N/A based on your answers
3. Computes dynamic fill values for your control descriptions
4. Maps your systems to relevant control categories
5. Initializes the evaluation grid

Before calling this, review the scope with get_scope_summary to make sure
product info, systems, and data types are all correct.

## `confirm_scope_delta`

Record whether the systems in scope changed during the observation period.

Ask the user ONE question, in their own words: since the period started,
has anything been added, dropped or replaced in the stack? This is not a
re-scope — the scope was fixed when the window opened — it is the one
check that catches a system adopted mid-period, which nothing else can
detect.

If something WAS added, call add_system for it first (it runs through
classification like any other system, and may not be in scope at all),
then call this with the user's answer in `note`. If nothing changed, call
it with unchanged=true.

Args:
    unchanged: True only when the user says nothing was added, dropped or
        replaced since the period started.
    note: The user's answer in their own words. Required when unchanged is
        false — a change with no account of it is a hole in the record.

## `confirm_tsc`

Confirm which audit scope categories to include.

Call this AFTER the client has reviewed the recommendations from
set_company_profile and decided which categories they want.

Security is always included automatically — you don't need to list it.

This is the canonical scope: the portal shows it read-only and any audit
the client purchases inherits it exactly, so re-call this tool whenever
the client decides to change their scope (before an audit is under way).
Once the readiness checklist is built, changing the CATEGORY set is a
two-step change: calling with a different set returns a preview of what
enters and leaves scope, and calling again with confirm_scope_change=true
applies it. Collected work is never lost: items that leave scope stay
saved and restore instantly if the category comes back. Compliance
add-ons stay editable anytime before the audit is purchased (re-call
with the SAME categories plus the updated addons).

Args:
    categories: The categories the client wants in scope.
        Options: "availability", "confidentiality", "processing_integrity", "privacy"
        Pass an empty list if the client only wants Security (the default).
    addons: Optional compliance add-ons to cover alongside SOC 2.
        Options: "gdpr", "ccpa", "hipaa". GDPR and CCPA require the
        "privacy" category; HIPAA requires the "availability" category.
        Pass an empty list to remove all add-ons; omit the argument to
        leave the client's current add-ons unchanged.
    confirm_scope_change: Set true only after the client has reviewed
        the scope-change preview and approved it. Ignored when neither
        the category set nor scope-moving add-ons (HIPAA) are changing.

## `delegate_topic`

Delegate a conversation topic to a team member.

The delegate receives an email with two options:
1. Connect via their own AI agent (MCP) — they get a scoped token
2. Use the web chat fallback

Only the assigned topic is accessible to the delegate.

Args:
    topic_key: Topic to delegate (from get_questions output data)
    delegate_name: Delegate's full name (e.g. "Sarah Chen")
    delegate_email: Delegate's email address

## `finalize_policy_pack`

Render the company's drafted policy library into branded PDFs in the
portal.

Working drafts stay markdown while readiness iterates; when the user wants
the finished set, this renders every document submitted with content into
a clean PDF on the COMPANY's own branding (their documents, never firm
letterhead) and files them under Deliverables in the portal. Safe to
re-run — a re-drafted document replaces its earlier render.

Args:
    confirm: first call with confirm=false lists what would render;
        re-call with confirm=true after the user says go.

## `get_judgment_kit`

Serve everything your AI needs to JUDGE and FIX one control/attribute.

Returns the Bible's pass criteria, what auditors look for, typical evidence,
applicability (when it is not applicable), worked calibration examples
(a situation, the call an AI would get wrong, the correct call, and WHY), the
coverage checks, and remediation guidance. This is reference DATA — you (the
client's AI) make the judgment and decide on fixes with the user; Chiaro
renders no verdict.

READ `calibration_examples` BEFORE YOU CALL THE ATTRIBUTE. Each one is a real
judgment call that an AI got wrong: `evidence_summary` is the situation,
`ai_verdict` is the wrong call, `correct_verdict` is the right one, and
`reasoning` says why in general terms. They exist because these are the
specific mistakes AIs make on this attribute — a document attribute passing on
substance rather than on being a standalone file, an acknowledgment measured
against the roster rather than against the acknowledgment system's own
contents, a process that excludes contractors. If your read of the evidence
matches an `ai_verdict`, that is the signal to look again, not to proceed.
(The same examples are published in Chiaro's open methodology repository —
nothing here is secret.)

Args:
    control_ref: e.g. "IAM-02".
    attribute_id: e.g. "A1". Omit to get the kit for every attribute of the control.

## `get_population_spec`

What a population is, in our definition, and what a pull of it must carry.

Call this BEFORE pulling anything. The definition is ours, not yours: if a
reconciliation later shows a difference, the answer is to re-pull against
this definition, never to narrow the definition until the difference goes
away.

Args:
    population_key: One of the keys this returns when called with no
        argument (e.g. "changes"). Omit to list them all.

## `get_progress`

Check collection progress — per-attribute completeness from the tracker.

Returns how many attributes are collected, partially collected, absent,
and which controls need more attention.

## `get_questions`

Get the next security area to discuss.

Use this anytime during evidence collection to find areas where
we need the client's input. Returns a practice-level question
for one topic at a time. Weave this into the natural conversation
alongside config scans and document reviews — don't treat it
as a separate phase.

## `get_scope_summary`

Review the current audit scope before confirming.

Shows product info, classified systems (infrastructure, your tools,
not in scope), data types, and scope status.

Call this to verify everything looks right before calling confirm_scope.

## `get_scoping_guidance`

Get scoping guidance for one system — what to verify and what evidence to focus on.

Use this in TWO places:

1) After confirm_scope, walk every Critical Dependency in the scope.
   Call this with each vendor's name. The response gives you the
   verification questions (typically 3-4) to ask the user — does
   customer data flow through them, would an outage affect commitments,
   is there a SOC 2 report on file. Capture answers via submit_answer.

2) During evidence drill (Phase 2B) for each In Scope system. Call this
   with the system name. The response tells you which GITC buckets
   apply (access / change mgmt / operations / encryption) and lists
   concrete evidence examples to look for. Focus your drill on the
   buckets that apply.

Args:
    system_name: The system or vendor name (e.g., "Anthropic", "Okta",
        "Modal", "Pinecone"). Aliases work too.

## `get_status`

Get current engagement status, progress, and what to do next.

Always call this first to understand where things stand.
Returns engagement info, collection progress, and next action.
On multi-product accounts it also lists the products and which one
this session is working on (switch with select_product).

## `get_topic_delegation_status`

Check progress on all delegated conversation topics.

Shows which topics have been assigned, to whom, and whether
their questions are answered.

## `get_unresolved`

Get items needing quick yes/no confirmation before evidence collection.

Returns controls where the conversation couldn't determine classification.
For each, ask the user a simple yes/no.

Must be called after conversation is complete.

## `load_area`

Load the topics for one evidence area: gating questions + coverage checks.

Call this with an area name to load that area's playbook. The response has
the gating questions to ask the user (in groups, via a multi-select picker
when your tool supports one) and the coverage checks for the deeper drill.

Without an area, returns a high-level summary of all areas (do NOT show
this to the user — use it for your own planning).

★ CHECK WHAT YOU ALREADY HAVE BEFORE YOU COLLECT ANYTHING. In detail mode
each attribute may carry:
  evidence_on_file  — files already registered against this attribute on
                      this engagement, with their path, lane and summary.
                      On an audit these include everything readiness handed
                      over. Do not go and fetch these again.
  readiness_cited   — files the client pointed at in READINESS for an
                      attribute whose checks are still open here. This is a
                      POINTER, not coverage: it never advanced the checklist
                      and it never will, because the audit's record is the
                      audit's own. Read its `note` — it says whether the file
                      clears the bar for what is open. Very often it does
                      not: a written policy is not a record that the thing
                      happened, and citing it again will not close the check.

Args:
    area: The area to load (e.g., "Access Management", "Governance",
          "Incident Response"). Required for normal flow. A control
          TITLE from get_progress / get_scoping_guidance (e.g.
          "Information Asset Inventory") also works — it loads that
          control's area. Every checklist entry carries its control_ref
          for your submit_* calls (never speak it to the user).
    gating_only: Internal. Leave default.

## `mark_absent`

Record that the client does NOT do one or more things. Batch-friendly.

Pass a LIST of titles to mark multiple "no" answers in a single call.
ALWAYS batch when you have multiple "no" answers from a multi-select
picker (Claude Code's AskUserQuestion or similar). One call beats many.

No judgment, no commentary. Just records the absences and moves on.

During readiness (self-serve prep) this tool redirects: record a practice
that does not exist with record_self_assessment instead, so it counts
toward readiness and stays on the fix list.

Args:
    titles: List of control titles from load_area, e.g.
        ["Code of Conduct & Ethics", "Background Checks", "Penetration Testing"].
        Single-element list is fine. Titles must match what load_area returned.
    note: Optional one-line context shared across these items
        (e.g., "solo founder, no hires yet").

## `mark_gap`

Log a deliberate gap: the client genuinely cannot produce this evidence.

Use this when a practice EXISTS but its evidence can't be produced ("we
do review access quarterly but never kept a log") or a capability is
honestly missing ("we don't run vuln scans yet"). It closes the open
checks as a REASONED gap so the handoff can proceed and our audit team
knows not to chase it. This is NOT a shortcut: a gap is surfaced to the
user, recorded verbatim, and lands in your draft report as a finding.
Only call this AFTER asking the user for the artifact and they confirmed
they can't produce it — if it's a screenshot, export, or a page they could
hand over (a Security/Terms page, where their policies live), ask first;
don't gap to move on. If the practice doesn't exist at all, use mark_absent
on the control instead. If evidence exists but is just effortful, collect it.

A system YOU can't reach is NOT a gap. The question is whose side the
obstacle is on: if the practice exists and the record exists but something
stopped YOU from retrieving it, the client can almost always still open that
page and hand you a screenshot or an export. That is a retrieval problem,
not a missing control, and this tool will pause and ask you to request the
screenshot or export first. Ask for it — that is almost always the fastest
route and it closes the check properly.

If the user tells you they genuinely cannot produce it in any form, they
confirm that themselves in the portal (an account admin, on the home page).
You cannot make that confirmation on their behalf and there is no argument
to this tool that grants it. Tell them where to go, and call this tool again
once they have.

Args:
    control_ref: Control ref from load_area data (e.g., "IAM-04").
    attribute_id: Attribute id within the control (e.g., "A3").
    reason: The user's actual reason, their words, specific. "No log was
        kept before May 2026" beats "not available". Required.
    check_ids: specific check ids to gap (from the still_missing
        payloads), as an array ["review_log_exists"] or a JSON string.
        Empty = every still-open check on this attribute.
    contradicts: Optional JSON list anchoring a contradiction this gap
        embodies to specific attributes, e.g.
        '[{"control_ref":"IAM-05","attribute_id":"A2","what":"policy promises
        quarterly access reviews but no review records exist"}]'. When the
        reason for a gap is that reality conflicts with a stated commitment,
        anchor it so /review surfaces the contradiction, not just the gap.

## `reclassify_evidence`

Correct the evidence_type label on a file you already submitted.

Use this when a file was filed under the wrong KIND — most often a dated
write-up of something that HAPPENED (an access-review record, a DR test
log, a deficiency tracker with entries) filed as "document", which is the
label for policies and plans saying what SHOULD happen. Do NOT re-submit
the file: an identical file reuses the existing row, so the label would
not change.

The evidence lane is re-derived by the server from the corrected label
plus the original submission's own signals — relabeling cannot make a
spreadsheet a system pull. Checks the corrected lane satisfies are
applied; nothing already satisfied is taken away, and the correction is
recorded for the reviewer either way.

Args:
    evidence_id: The evidence id returned at submit time. The file's
        sha256 or its saved path also work.
    evidence_type: The corrected label — same vocabulary as
        submit_evidence: "record" (a log, a ticket, a dated write-up of a
        completed activity), "policy"/"procedure"/"plan"/"document",
        "cli_output"/"config"/"api_response"/"scan" (raw command output),
        "export", "screenshot".
    why: One sentence: why the original label was wrong.

## `reclassify_system`

Reclassify a system that was incorrectly classified.

Use this if an auto-classified system doesn't match your setup. Downgrade
reclassifications (in_scope_tool → vendor, or in_scope_tool →
infrastructure_dependency) REQUIRE a `reason` field explaining why,
and the reason is recorded as a context_note for the audit reviewer.

Args:
    name: System name (must match what was added, e.g. "Stripe")
    new_classification: One of 'infrastructure_dependency', 'in_scope_tool', 'vendor'
    reason: Required when downgrading from in_scope_tool. Explain WHY
        the system shouldn't be tested directly (e.g., "client doesn't
        configure it, just consumes managed defaults"). Recorded for
        the auditor's review.

## `reconcile_scope_inventory`

Reconcile the recorded system scope against an independent enumerator.

Pull a list the systems themselves can produce — the identity provider's
application catalog, the cloud account's service inventory, DNS zones,
SaaS spend if the user offers it — and send the names here with the exact
command that produced them. I compare them against the recorded scope and
return anything unmatched as QUESTIONS to ask the user. Nothing is added
automatically: if the user confirms something unmatched is real and in
use, record it with add_system, which classifies it like any other
system.

Run this during collection phase 1 alongside confirm_scope_delta, and
run it AGAIN in phase 2 — a system adopted in the final three weeks only
shows up in the re-run.

Args:
    source: What kind of enumerator this list came from. One of:
        idp_app_catalog, cloud_service_inventory, dns_zones, saas_spend,
        other.
    command: The exact command or export that produced the list,
        verbatim and re-runnable.
    names: The enumerated names — a JSON array of strings, or one name
        per line.
    note: Anything the user said about the list (optional).

## `record_bypass_routes`

Ask how else a security setting could be changed, outside the normal process.

Call it with NO arguments first. You get back four yes-or-no questions in
plain English and the systems they are about. Ask the user each one, in your
own words if that reads better, then call again with the keys they said YES
to. "No" to all four is a real answer: pass none=true.

Keep it conversational. The user is telling you how their systems actually
work, not filling in a form, and nothing here is signed. If they mention a
route you were not asked about, send it in `other` in their words. If they
are unsure about one, treat that as a yes and say so in `other`.

Why it matters, if the user asks: their report will state that every change
to their security configuration was recorded in the system's own log. These
answers bound that sentence to what is true, so nobody is asked to stand
behind something neither of us can check.

Args:
    confirmed: The keys the user said YES to, from the four sent back. A
        JSON array of strings, or comma separated.
    other: Any route they described that the four questions did not cover,
        in their own words. One per line or a JSON array.
    none: Pass true when the user said no to all four and named nothing
        else. Recorded as the answer, never inferred from an empty list.

## `record_complementary_controls`

Record what the report assumes the client's customers and cloud providers do.

Call it with NO arguments first. You get back the criteria that are in
scope, the subservice organizations recorded for this engagement, and a few
examples of what other companies' descriptions say. Read the examples to the
user as options and ask what is actually true of THEM, then call again with
their answer.

Two questions, and the report cannot be issued without both:

  1. COMPLEMENTARY USER ENTITY CONTROLS. What does this company rely on its
     own customers to do for the service to be secure? Managing the accounts
     it issues them, removing access when their own people leave, telling
     the company about suspected misuse. If the user says their customers
     are relied on for nothing, that is a real answer: pass
     no_user_entity_controls=true.
  2. COMPLEMENTARY SUBSERVICE ORGANIZATION CONTROLS. For each organization
     the description carves out (the cloud providers), what does this
     company assume THEY do? Physical security of the data centers is the
     usual one. If nothing is assumed of a particular organization, name it
     in no_subservice_controls_for.

These sentences go into the report as management's, so they must be the
user's answer in the user's words. The examples are there to give the user
something to react to; they are options, not our answer, and nothing is
recorded until the user has chosen, changed or replaced them. Every row is
keyed to a criterion because the report's tables are, and only criteria in
this examination's scope can be used.

Args:
    user_entity_controls: What the company relies on its customers to do. A
        JSON array of objects: [{"criteria": ["CC6.2"], "statement": "User
        entities are responsible for ..."}].
    subservice_controls: What it assumes of each carved-out organization. A
        JSON array of objects, each naming which one: [{"organization":
        "Amazon Web Services (AWS)", "criteria": ["CC6.4"], "statement":
        "The subservice organization is responsible for ..."}].
    no_user_entity_controls: Pass true when the user says their controls
        assume nothing of their customers. Recorded as the answer.
    no_subservice_controls_for: The organizations nothing is assumed of. A
        JSON array of names, or one per line.

## `record_hipaa_intake`

Record the HIPAA intake answers (asked when the HIPAA add-on is chosen).

Ask the client these five things in plain language BEFORE confirm_scope,
then record the answers here. They become recorded determinations on the
scope profile: several Security Rule provisions apply only to specific
organization types (health care clearinghouses, government entities under
an MOU, group health plans), and a recorded answer is what makes skipping
those provisions a documented determination instead of an assumption. The
ePHI systems list also seeds the business associate agreement work.

Args:
    entity_role: "business_associate", "covered_entity", or "both". A
        software company serving healthcare customers is almost always a
        business associate.
    contains_clearinghouse: True if the organization contains or operates
        a health care clearinghouse (an entity translating health
        information between standard and nonstandard formats).
    group_health_plan: True if the organization is, or sponsors, a group
        health plan whose administration touches the product in scope.
        Offering ordinary employee health insurance is NOT this.
    ephi_systems: The systems and data flows where ePHI is created,
        received, maintained, or transmitted, named the way the client
        names them (for example "production Postgres", "uploads bucket",
        "transcription pipeline").
    cloud_provider_baa: True if a business associate agreement is in
        place with the cloud infrastructure provider.
    cloud_provider_baa_note: Optional detail, for example "AWS BAA
        accepted through Artifact".

## `record_policy_adoption`

Record what the user said when asked whether they adopt a drafted document.

Fire the picker `drafts_awaiting_adoption` serves you, once per document,
then record the answer HERE in the user's own words. The server tracks which
documents have been asked about, so a document that stops appearing in that
list has been answered — never re-ask it.

This records the USER's statement, exactly like an attested pass: their name
and their words, dated by the server. It is their decision, never yours, and
you may not answer it on their behalf.

Args:
    evidence_id: the drafted document's evidence_id, from the picker.
    decision: "adopted" (they read it and adopt it) | "changing_first"
        (they want edits before adopting) | "pre_existing" (the document
        predates your draft and was already adopted) | "not_yet".
    adopted_by: who said it — required for "adopted" and "pre_existing".
    statement: their exact words — required for "adopted" and "pre_existing".

## `record_prior_report`

Record whether this is a first SOC 2 or there is a prior report.

Ask this ONE question at the very start of scoping, as a picker: is this
their first SOC 2, or do they have a report from another firm? Then call
this tool with the answer. If it is their first, pass had_prior=false and
nothing else, and scoping carries on normally.

If they have one, do it in this ORDER, and the order matters:

  1. FIRST enumerate what they actually run, yourself. Detect what you can
     from where you are running (the repo, package files, configs, CLIs
     already authenticated), ask the user, and record what you find with
     add_system. A report read before you have looked becomes an anchor
     that both of us then rubber-stamp.
  2. THEN ask them to point you at their most recent report and read it IN
     FULL, including the exceptions. It is their file, you read it locally
     with their approval, and it never leaves their machine. On an audit
     engagement, save a copy into the engagement folder so it travels with
     the evidence at handoff, and never submit it as evidence for a
     control: it is context for scope and risk, not proof that anything
     operated.
  3. Send what you read here. I compare their system list against ours and
     hand back the differences as QUESTIONS for the user.

What a prior report is worth: it seeds the system list, and its exceptions
raise the amount of testing we do. What it is never worth: a conclusion.
Nothing in it passes, clears or shortens anything on our side. Every
control is examined fresh against our own criteria and the company's own
current evidence, and a clean prior report changes nothing about that.
Nothing here is added to the scope automatically either. Names that do not
match come back as questions; for anything the user confirms is real and in
use, call add_system, which classifies it like any other system.

Args:
    had_prior: False for a first SOC 2 (nothing else is needed). True when
        they have a report from another firm.
    prior_firm: The firm that issued it, as printed on the report.
    report_type: "type1" or "type2".
    period: The period or date it covers, as printed (e.g. "January 1,
        2025 to December 31, 2025").
    systems: The systems named in its system description. A JSON array of
        strings, or one name per line.
    exceptions: Every exception, deviation or qualification the report
        noted, in ITS words, not yours. A JSON array of objects is best:
        [{"description": "...", "topic": "access management",
          "systems": ["AWS"]}]. One per line also works. `topic` is plain
        English (access management, change management, incident response,
        vendor management, and so on) and helps me route the extra testing;
        leave it out if you are unsure rather than guessing, and the
        exception goes to our reviewer instead. Send an empty list only if
        the report genuinely noted none.
    categories: Which trust categories it covered (Security, Availability,
        Confidentiality, Processing Integrity, Privacy). JSON array or one
        per line.
    opinion_modified: True if the opinion was qualified, adverse, or a
        disclaimer rather than clean.
    file_note: Which file you read, for the record (e.g. "SOC 2 Type II
        report 2025, client's own copy, read locally").
    saved_path: Where you saved their report inside the engagement's
        evidence folder. Required on BOTH engagement types (readiness
        included — the same local folder carries into the examination
        later), because the folder is what gets handed over at the end
        and the report that raised the assessed risk has to be in the
        file. Nothing is uploaded when you pass this; it travels with
        the rest of the evidence at handoff.
    not_provided: Pass true INSTEAD of saved_path when the user cannot
        produce the file at all. That is a recordable answer, not a
        silence, and it goes in the file as one. Put what they described
        in file_note.

## `record_questionnaire_answers`

Save the answers the user supplied, so the next questionnaire is pre-filled.

Call this after the user has answered the open items from
answer_questionnaire. Buyers ask most of the same things in different
formats, so an answer given once should never have to be given again.

Save the user's OWN words, cleaned up into a straight answer. Never save
a guess, never save something the user did not say, and never save an
answer that names the audit firm or the platform.

Args:
    answers: A list of {"question": "...", "answer": "..."} items. Use the
        buyer's question text exactly as it appeared, and the user's
        answer. Items missing either field are skipped.

## `record_self_assessment`

Record YOUR AI's self-assessment verdicts (the client's own judgment).

Use after checking gathered evidence against the judgment kit. Server-side
gates keep the meter honest (they check FORM, never re-judge): a pass needs
cited evidence from this engagement (or basis='attested' with the user's
explicit confirmation), not_applicable is refused where the check always
applies, and flipping a gap to pass needs newer evidence or correction=true.

A pass also has to MEET THE CHECK'S OWN BAR. Every coverage check declares
the strength of artifact it needs, and citing something weaker is refused
with the bar named — a policy cannot close a check that asks for a record of
something happening. Read `how_to_judge` in get_judgment_kit before you
judge; it is the same standard the examination applies. When the thing the
criterion NAMES is nowhere in what you hold — you hold nothing, or you hold
a document that discusses the subject without containing it — ask the user
before you file: "do you actually do this?" for a practice, "does this exist
anywhere, signed / published / defined?" for a named thing. Their "no, we
never created one" is `absent` in their own words; their "yes, it lives
somewhere else" is a `gap`. Both are normal answers; guessing between them
is not.

Args:
    control_ref: e.g. "IAM-02".
    attribute_id: e.g. "A1".
    verdict: "pass" | "gap" | "absent" | "not_applicable" (absent = the
        practice itself does not exist, on the user's word; a practice that
        exists but lacks records is a gap — ask them which it is rather
        than inferring it from an empty search).
    reasoning: one or two sentences citing the evidence and the criterion.
    evidence_ids: the evidence ids (from submit_evidence/submit_document),
        as an array ["<id>"] or as that array in a JSON string.
    basis: "evidence" (default) or "attested" (user explicitly confirms
        without walking through evidence — quote them).
    attested_by: who confirmed (required when basis='attested').
    attested_statement: the user's own words (required when basis='attested').
    fix_description: when closing a previously recorded gap — what changed.
    correction: true when overturning your own earlier misjudgment (say why
        in reasoning).
    assessments: OPTIONAL batch — JSON list of objects with the fields above
        (max 10 per call; each item is gated individually).

## `record_subservice_soc2`

Record whether a subservice organization's SOC 2 report is on file.

For each Critical Dependency in scope (AWS, Stripe, the cloud platforms we
rely on), ask the user explicitly: "Do you have their most recent SOC 2
report on file?" and record the answer here — yes OR no, either is a real
answer. complete_step refuses the handoff while any subservice org's
status is unrecorded, because "we never asked" must never read as "no
report obtained".

Args:
    name: The system as it appears in scope (e.g. "AWS", "Stripe").
    report_obtained: True if the vendor's SOC 2 report is on file
        (save a copy into 05-subservice-evidence/ so it travels at
        handoff); False if they do not have it — recorded honestly, the
        audit team weighs reliance accordingly. Never guess: ask.
    note: Optional context in the user's words ("reviewed it in March",
        "requested from the vendor, not received yet").

## `report_problem`

Tell the Chiaro team that something on this server looks broken.

A refusal is usually an answer, not a bug. needs_input and needs_confirmation
are questions for the user; wrong_phase, not_open_yet and locked name a state
to wait for or a tool that moves it. Read the message and do what it names
ONCE before you consider reporting.

Call this when, and only when, one of these is true:
  1. the SAME call failed TWICE with the same reason after you did what the
     first refusal named;
  2. a refusal sent you to a tool that then refused you back for the same
     engagement, so there is no way forward;
  3. a call succeeded but get_progress did not move, twice in a row, for the
     same attributes;
  4. you genuinely do not know what to do next, and get_status,
     get_scope_summary and get_progress did not tell you.

One report per problem. Then tell the user in one line that the team has
been told, and carry on with the areas that still work. Do not tell the user
to email us — this does that.

Args:
    summary: What is wrong, in a sentence or two. Plain language.
    what_i_tried: The calls you made and what came back.
    expected: What you expected to happen instead.
    tool_names: The tools involved, e.g. ["load_area", "get_progress"].

## `request_upload_link`

Mint an upload link for someone who cannot use their portal Upload page.

NOT the normal handoff. A client with an account uploads from their own
portal: the Upload page in their left nav, where they are already signed
in. Send them there. get_status and complete_step both hand you that URL
and the zip commands, and a link minted for that person only makes them
retype the identity we already hold.

Call this in two cases, and no others:

  the portal page will not work for them — it does not load, or they are
  not signed in and cannot be right now. Set
  portal_upload_unavailable=true, which is also what lets the link be
  minted before the handoff. Only set it after the user has actually told
  you the page failed; do not set it to skip a step.

  you are helping as a contributor or a delegate — a helper's upload is
  inbound evidence for their topics rather than the engagement handoff, so
  the link is their door at any stage and no flag is needed.

Save what is being shared into ~/Documents/chiaro-soc2/, zip it
(data.zip_commands), and the user drags the zip onto the page
(data.open_commands opens it).

The link is single-engagement, time-limited (default 90 days), and revocable.
Each call creates a NEW link — if you need to re-issue (e.g., the user lost the
URL), call again and the prior link still works until revoked.

Args:
    label: Optional human-readable label for the link
           (e.g., "Mock exam round 1"). Helps the audit team triage.
    portal_upload_unavailable: The user told you their portal Upload page
           will not work for them. Set only on their word.

## `reset_readiness`

Clear recorded readiness self-assessments so the company can re-verify
ahead of its NEXT audit.

Readiness is rolling: it always describes the company's current posture
toward the next examination. After an audit completes (or when things have
changed), the user may want a fresh pass over everything or over specific
controls. This clears the recorded VERDICTS only — collected evidence and
the append-only history stay (nothing is destroyed or rewritten).

Args:
    controls: "all", or a JSON list of control refs (e.g. '["IAM-02","END-03"]').
    confirm: first call with confirm=false returns what would be cleared;
        re-call with confirm=true ONLY after the user explicitly confirms.

## `resolve_item`

Reclassify a control's Absent/Exists status.

Use this to correct a classification if the AI got it wrong.

Args:
    topic_key: The topic containing the control
    has_it: true = control exists, false = control is absent
    description: Brief description (required if has_it is true)

## `select_product`

Switch this connection to a different product on the account.

Use when the user wants to work on another product than the current one
(get_status lists the account's products). Pass the product's name
(case-insensitive) or its engagement id. The switch is remembered for
this user across sessions; call get_status afterwards to load the
switched product's state.

## `set_company_profile`

Set company profile details for scoping.

These answers determine which controls apply and how they're customized.
All fields are optional — sensible defaults are used for first-time startups.

Don't ask about policy review frequency, training frequency, or scan frequency
here — those are discovered during the walkthrough conversation.

Returns audit scope recommendations based on the product, data types, and
company profile collected so far. The client MUST review and confirm these
before the scope can be locked.

Args:
    company_founded_date: When the company was founded (YYYY-MM format, e.g. "2024-06")
    has_physical_office: Does the company have a physical office, data center, or server room?
    governance_body: Who oversees security, as a SHORT noun phrase that
        reads inline in control text (e.g. "the Board of Directors",
        "the leadership team", "the founding team"). Keep it under six
        words; put structural detail (advisors, no board yet, titles)
        in scope notes instead, never in this field.
    engineering_team_size: How many people write and deploy production code
        (1 means a single developer maintains the codebase). Leave 0 if unknown.
        This selects the change-management and review controls that fit the
        organization's real structure.
    pen_test_program: "yes" or "no" — does the organization run a penetration
        testing program (periodic pen tests by a qualified party)? ASK THIS
        EXPLICITLY. "no" is a normal answer, not a gap: SOC 2 does not require
        a penetration test, and with "no" the penetration testing control is
        simply out of scope. Independent evaluation of controls is still
        tested (a pen test is one way to satisfy it, not the only way).
        When the answer is no, tell the client this is a scoping
        determination, not a security recommendation, and that their
        customer contracts can require a pen test even though the audit
        criteria do not. Leave "" only if the client could not answer.
        The answer BRANCHES what you ask next, and the response returns
        `independent_evaluation_hint` telling you which branch you are on:
        yes means the same pen test report also answers the independent
        evaluation check, so collect it once and skip the alternatives
        question entirely; no means you ask what other independent
        evaluation they already hold, by name. Do not ask the alternatives
        question of a client who runs pen tests.
    prior_soc2: PREFER record_prior_report, which is the tool for this
        question on both engagement types: it reconciles their system list
        against ours, banks the exceptions as risk flags, and stops the
        question being asked again. This argument records the facts and
        does none of that. Kept for the facts alone.
        Only when the company is TRANSITIONING from another firm.
        Facts read from their most recent SOC 2 report (their file, read
        locally — we never receive it), as a dict:
        {"had_prior": true, "prior_type": "type1"|"type2",
         "prior_period": "e.g. 2024-01 to 2024-12", "prior_firm": "name",
         "focus_areas": ["exceptions or gaps to re-confirm are fixed"]}.
        This is scope context only. The prior report's verdicts are never
        our evidence — every applicable check is still self-assessed with
        the company's own real evidence. Omit for a first-time SOC 2; pass
        {"had_prior": false} to clear a mistakenly recorded transition.
    intended_obs_months: The observation window the company PLANS if they
        go for a Type II later — 3, 6 or 12. One friendly planning
        question, no commitment: it only tunes the log-retention guidance
        (retention must cover the window), nothing is gated on it, and it
        is reconciled automatically when a real window is purchased.
        Omit or 0 when unanswered; the guidance then assumes 12.

## `set_data_types`

Set what types of data your product processes.

This determines which data protection controls are relevant for the audit.

Args:
    data_types: List of data types (e.g. ["Personal info", "Financial", "Usage analytics", "Documents & files"])

Common data types:
- Personal info (names, emails, phone numbers)
- Financial (payment data, billing, transactions)
- Health & medical (PHI, health records)
- Documents & files (user-uploaded content)
- Messages & comms (chat, email content)
- Usage analytics (behavioral data, logs)
- System configs (infrastructure settings)
- Proprietary data (trade secrets, IP)

## `set_product_info`

Set product/service information for the audit scope.

This defines what product is being audited and who uses it.

Args:
    product_name: Name of the product or service (e.g. "CloudSync API")
    description: One-line description of what the product does
    customers: Who are your customers? (e.g. "B2B SaaS companies, Series A-C startups")

Returns confirmation and next steps.

## `start_audit`

Begin the purchased SOC 2 audit examination, or a purchased Type II
observation window.

Use when the audit has been bought (the portal's SOC 2 Audit card) and the
user wants the examination to begin. Starting is a BILLING event: per the
engagement letter, the first installment charges the card on file when
the examination begins. The first call therefore starts NOTHING — it
returns the exact amounts; show them to the user, get an explicit yes,
then call again with confirm_start=true. When readiness is NOT complete
it additionally asks for confirm_start_early=true — set that ONLY after
the user explicitly confirms starting before readiness is done. The audit
is an independent examination: it never depends on the readiness meter,
collected evidence carries over, and anything genuinely not in place when
examined appears as an exception in the report.

Type II window: after a bundled Type I delivers, this same tool starts the
Type II observation window (the remaining bundle fee bills in two
installments, the first no earlier than seven days after the client's
notice). start_date is REQUIRED for a window and is the user's decision to
make — ask them, never assume. A future date schedules the start; today
starts now; a date already past is accepted, as long as the period has not
already ended. Nothing is collected at the start of a window: evidence
collection happens in two phases, the first opening three weeks before the
period ends and the second the day after it closes.

Args:
    confirm_start: Set only after the user has seen the amounts and said
        yes.
    confirm_start_early: Set only after the user confirms starting before
        readiness is complete.
    start_date: The first day of the observation period, YYYY-MM-DD.

## `start_engagement`

Set up your company and product info to start the SOC 2 engagement.

This populates an existing engagement (your token is already scoped to one).
Call get_status first to see current state.

Args:
    company_name: Your company's legal name (e.g. "Acme Labs Inc.")
    product_description: One-line description of your product/service
    team_size: Team size bucket. Valid values (small to large):
        "Solo founder (1)", "2-5", "6-10", "11-25", "26-50", "51-100",
        "101-250", "251-500", "500+". Solo and 2-5 are common for early
        AI startups and unlock tailored UX downstream.

Returns engagement info and next steps.

## `start_readiness_check`

Begin or resume a self-serve SOC 2 readiness check.

Returns where the company stands on getting audit-ready (self-assessed):
the per-area map, the recommended focus area, the judging methodology, and
the next step. This is a self-assessment run entirely by your (the
client's) AI: Chiaro serves the framework and records your findings; it
issues no verdict and runs no AI.

## `submit_answer`

Record what the client said about an area. No judgment, just storage.

Prefer mark_absent when the answer is a simple "no, we don't do that."
Use this tool when you need to capture a free-text answer about how
something works (e.g., "we onboard via Notion + a checklist in Linear,
here's the link").

Args:
    title: The control title from load_area. Use the exact title.
    gating_response: Optional "yes" or "no". If "no", the entire
        control is marked absent (same effect as mark_absent). If
        "yes" or empty, the answer is stored as conversational evidence.
    answer: Free-text content describing what the client does.
        Stored verbatim for our audit team to read during review. Never paraphrase.
    per_attribute_coverage: Reserved for advanced/internal use. You
        do NOT need to populate this from conversation. Leave empty.
    absent_attributes: Reserved for advanced/internal use. Leave empty.
    follow_up_round: Internal field. Leave at default.

## `submit_document`

Record that a policy/procedure/plan document has been saved locally.

This tool files the artifact as a DOCUMENT — something written saying what
SHOULD happen. A dated write-up of something that DID happen (a completed
access review, a DR test with results, a deficiency tracker with entries)
is a RECORD: submit it with submit_evidence and evidence_type="record"
instead, or record-bar checks cannot count it. A wrong label is fixed with
reclassify_evidence, never by re-submitting.

REQUIRED FLOW:

  1. You (the AI) saved the real document to local_path under
     ~/Documents/chiaro-soc2/02-policies/ (or 04-process-evidence/ for procedure
     records). The raw file is the source of truth.
  2. You wrote a sibling how_collected.md describing where it came from.
  3. NOW you call this tool with the summary + which attributes the doc
     helps satisfy (existence only).

HARD RULE — NO FABRICATION:
  You may SUMMARIZE a real document the user pointed you at.
  You may NEVER GENERATE a polished policy from scratch when the user
  said they don't have one. If they describe an informal practice, save
  their VERBATIM WORDS as a .md file and submit that instead.

The local file gets uploaded to Chiaro at the end of the engagement and
becomes the basis of the actual audit. Your summary helps move efficiently.

Args:
    name: Document name (e.g., "Access Control Policy", "IR Plan").
    local_path: ABSOLUTE path to the file you saved under ~/Documents/chiaro-soc2/.
        Required. If you have not saved a real file, do not call this tool.
    summary: 1-3 sentences describing what the document contains.
        You MAY summarize. You MAY NOT add anything not in the source file.
    covers_attributes: the [control_ref, attribute_id] pairs this document
        helps satisfy. Send it as a real array — [["POL-01","A1"],
        ["POL-01","A2"]] — or as that array in a JSON string. Both work.
        EXISTENCE TRACKING ONLY — "does the document include the thing the
        coverage check asks about, yes or no." Quality is the auditor's job.
        Required.
    document_type: "policy" / "procedure" / "plan" / "report" / "other".
    sha256: SHA-256 hex digest of the saved file (shasum -a 256 /
        Get-FileHash). Reconciled against the uploaded bytes at review —
        include it whenever you saved a real file.
    effective_date: Optional ISO date (YYYY-MM-DD) the document is dated /
        took effect. Pass it especially for a RECORD or report (a training
        completion record, an access-review log, meeting minutes, an
        incident report). If such a record is dated today it has no
        operating history — we don't block it, we flag it for review.
        A policy/procedure you just created today is fine and isn't flagged.
    content: READINESS ONLY, optional: the document's full markdown text
        (as saved to local_path, up to ~200KB). Pass it for policies and
        plans you drafted — finalize_policy_pack later renders every
        content-bearing document into the company's branded PDF pack in
        the portal. Audit engagements ignore it (files travel via the
        upload link there).

## `submit_evidence`

Record that a piece of raw evidence has been saved locally. REQUIRED FLOW:

  1. You (the AI) used Write or Bash to save the raw file to local_path
     under ~/Documents/chiaro-soc2/. The raw file is the source of truth.
  2. You wrote a sibling how_collected.md describing how it was obtained.
  3. NOW you call this tool with the summary + which coverage_checks it
     satisfies (existence only).

The local file gets uploaded to Chiaro at the end of the engagement and
becomes the basis of the actual audit review. Your summary helps our audit team
move efficiently. NEVER call this tool for content that doesn't exist
as a real file on disk.

Args:
    area: Plain-English area key from load_area (e.g., "access-management",
        "change-management", "data-protection").
    local_path: ABSOLUTE path to the file you just saved under ~/Documents/chiaro-soc2/.
        Required. If you have not saved a real file, do not call this tool.
    summary: 1-3 sentences describing what the file contains. Plain English.
        You MAY summarize. You MAY NOT invent content that isn't in the file.
    covers_attributes: the [control_ref, attribute_id] pairs this evidence
        helps satisfy. Send it as a real array — [["IAM-02","A1"],
        ["IAM-02","A3"]] — or as that array in a JSON string. Both work.
        This is EXISTENCE TRACKING ONLY — "does the file include the thing
        the coverage check asks about, yes or no." NOT quality judgment.
        our audit team judges quality during review later. Required if the evidence
        corresponds to specific control attributes.
    source_command: How the file was obtained. One line, mirrors how_collected.md.
        Examples: "Okta admin > Reports > Users > Export 2026-05-15" or
        "aws iam get-credential-report --output json on prod 2026-05-15".
    source_system: Source system (e.g., "okta", "aws-production", "github").
    evidence_type: What KIND of artifact this is. State it — it decides how
        strong the evidence counts as, and leaving it blank files the
        artifact at the weaker end on purpose.
          system pull  "cli_output" / "config" / "api_response" / "scan"
                       (raw output of a command you actually ran — pair it
                       with source_command and source_system)
          export       "export"  (a file the system generated for you)
          screenshot   "screenshot_note" / "ui"
          document     "policy" / "procedure" / "plan" / "document"
                       (a written document saying what SHOULD happen —
                       submit_document is the better tool for these)
          record       "record" — a log, a ticket, a signed form, or a
                       dated write-up of a COMPLETED activity (an access
                       review you performed, a DR test with its results,
                       a deficiency tracker with entries). Proof that
                       something HAPPENED is a record, never a document,
                       even when it lives in a .md file — many checks
                       demand record-strength proof and a "document"
                       label cannot satisfy them. Fix a wrong label with
                       reclassify_evidence; do not re-submit the file.
        A spreadsheet or a Word file is never a system pull, whatever the
        label says — save the command's raw output instead if that is what
        you meant.
    sha256: SHA-256 hex digest of the saved file. Compute it right after
        saving (macOS/Linux: `shasum -a 256 <file>`; PowerShell:
        `Get-FileHash <file> -Algorithm SHA256`). At review we reconcile
        this against the uploaded bytes — it is the artifact's integrity
        anchor, so include it whenever you saved a real file.
    provided_by: Name of the PERSON who produced/exported this evidence
        (the user, or whoever they say did it). Validated against the
        engagement roster — never guess or invent a name.
    effective_date: Optional ISO date (YYYY-MM-DD) the artifact is dated /
        took effect, if it states one. Pass it for any RECORD of activity
        (a training-completion record, an access-review log, an incident
        write-up). If such a record is dated today, it has no operating
        history — we don't block it, we flag it for review. (A policy you
        just created today is fine; you don't need a date for it.)

## `submit_note`

Record a verbatim claim or context that doesn't fit a specific control.

Use this for side comments the user makes that are audit-relevant but
don't directly answer a checklist question. The audit team reads these
alongside the structured evidence during review.

Worth recording (examples):
  - "We're migrating from AWS to GCP next quarter"
  - "Lost our security engineer in March, still hiring"
  - "We tested DR once but it failed and we never re-ran it"
  - "The CFO actually decides what gets MFA-enforced, not the CTO"
  - "We're on the free GitHub plan so branch protection isn't available"
  - User correcting themselves: "actually I was wrong earlier — we
    don't enforce that on the dev environment"

Not worth recording:
  - Pleasantries, jokes, scheduling chatter
  - Things already captured by another submit_* call

Args:
    note: The user's verbatim words. Do NOT paraphrase. Include their
        qualifiers and conditions (e.g., "but only for production",
        "we used to do this but stopped"). Quote them, prefix with
        "User said:" if helpful.
    topic_hint: Optional one-word area this might relate to so our
        audit team can route it ("people", "vendors", "infra",
        "access", "policies", "ir", etc.). Leave empty if unsure.
    contradicts: Optional. If this note records something that CONTRADICTS
        a specific control attribute (e.g. the policy promises quarterly
        access reviews but no records exist; the ToS says "we never train
        on your data" but there's no DPA), pass a JSON list anchoring it:
        '[{"control_ref":"IAM-05","attribute_id":"A2","what":"policy says
        quarterly reviews; client confirms zero review records exist"}]'.
        This writes the contradiction to those attributes so review can't
        miss it. The goal is never to hide a contradiction — surface it.

## `submit_population`

Hand over a complete population with the retrieval that produced it.

You are NOT judging anything here. Convert the raw output into the canonical
table and send both; every conclusion is formed later by the audit team.
Do not drop rows that look like problems — a change that skipped review is
exactly what the population is for, and removing it is the one thing that
makes the whole exercise worthless.

If I refuse, the message says what did not reconcile and what to re-pull.
Fix and resend; do not work around it.

Args:
    population_key: From get_population_spec (e.g. "changes").
    source_command: The exact command you ran, verbatim, re-runnable.
    field_map: JSON object mapping each canonical field to where it comes
        from in a raw record. A plain dotted path is {"path": "mergedAt"}.
        For a list, use one of map_where / first_where / min_where /
        max_where / count_where / any_where / all_where, e.g.
        {"map_where": {"path": "reviews", "field": "state", "op": "eq",
        "value": "APPROVED", "take": {"by": "author.login",
        "at": "submittedAt"}}}.
    raw: JSON object {"format": "json_array", "records": [...]} holding the
        unedited output. Prefer the tool's own structured output (--json,
        --output json) over parsed text. If it is too large I will tell you
        how to narrow the pull rather than accepting a truncated one.
    rows: JSON array of the normalized rows. Every row needs a source_ref
        of the form "<raw_id_path>=<value>" or "raw#<index>".
    raw_record_count: How many records are in the raw output. I count them
        myself and refuse on a mismatch.
    source_system: The system it came from ("github", "rippling", "okta").
    filter_granularity: "exact_utc" if your filter used exact timestamps,
        "date_only_widened" if it used dates and you widened the range one
        day on each side. A bare "date_only" is refused: whether an item at
        the edge is included would depend on the source's timezone.
    raw_id_path: Path to each raw record's own identifier ("number", "id").
    declared_exclusions: JSON array of filters you applied, e.g.
        [{"field": "state", "op": "eq", "value": "MERGED", "keep": true,
        "reason": "the population is changes that reached production"}].
    aux_raw: JSON object of supporting pulls, each {"command": ...,
        "records": [...]} — the identifier census and any second source.
    completeness: JSON object declaring which completeness tests apply
        (sequence, reconciliations, control_total, period_coverage,
        recon_attempts). get_population_spec says which ones this
        population supports. For a roster (state-as-of) population it must
        also carry as_of — the exact timestamp the state was captured at.
    policy: JSON object of parameters from the entity's OWN documented
        policy, with policy_basis naming the document. Defaults are strict:
        an undeclared exception does not exist.
    source_evidence_ids: When the population's records are DOCUMENTS
        rather than command output (review PDFs with signatures, meeting
        minutes, restore-test write-ups): submit each source document via
        submit_document FIRST, then cite the returned evidence ids here
        (JSON list, or comma-separated). The rows are then an extraction
        the reviewer can check against the papers instead of a
        transcription with no basis, and source_command honestly
        describes the manual extraction (e.g. "manual: transcribed from
        the four quarterly access review PDFs"). Leave empty for a
        command-pulled population.

## `undo_delegate`

Take back a delegated topic. The delegate's token is revoked
and the topic returns to your queue.

Args:
    topic_key: The topic to take back (from get_topic_delegation_status output)
