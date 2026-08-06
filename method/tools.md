# The tools

The 40 tools a connected client's AI can call. Generated from the
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

GATED: collection must be complete before this succeeds. Three gates:
  1. Every in-scope attribute is in a terminal state (collected, absent, or N/A)
  2. Every in_scope_tool system has at least one real (non-conversation)
     evidence artifact referencing it
  3. Every collected sample-type attribute has a real artifact, not just
     a verbal submit_answer
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
applicability (when it is not applicable), calibration principles, the
coverage checks, and remediation guidance. This is reference DATA — you (the
client's AI) make the judgment and decide on fixes with the user; Chiaro
renders no verdict.

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

Args:
    area: The area to load (e.g., "Access Management", "Governance",
          "Incident Response"). Required for normal flow.
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
    check_ids: JSON list of specific check ids to gap (from the
        still_missing payloads), e.g. '["review_log_exists"]'.
        Empty = every still-open check on this attribute.
    contradicts: Optional JSON list anchoring a contradiction this gap
        embodies to specific attributes, e.g.
        '[{"control_ref":"IAM-05","attribute_id":"A2","what":"policy promises
        quarterly access reviews but no review records exist"}]'. When the
        reason for a gap is that reality conflicts with a stated commitment,
        anchor it so /review surfaces the contradiction, not just the gap.

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

Args:
    control_ref: e.g. "IAM-02".
    attribute_id: e.g. "A1".
    verdict: "pass" | "gap" | "absent" | "not_applicable" (absent = the
        practice itself does not exist; a practice that exists but lacks
        records is a gap).
    reasoning: one or two sentences citing the evidence and the criterion.
    evidence_ids: JSON list of evidence ids (from submit_evidence/submit_document).
    basis: "evidence" (default) or "attested" (user explicitly confirms
        without walking through evidence — quote them).
    attested_by: who confirmed (required when basis='attested').
    attested_statement: the user's own words (required when basis='attested').
    fix_description: when closing a previously recorded gap — what changed.
    correction: true when overturning your own earlier misjudgment (say why
        in reasoning).
    assessments: OPTIONAL batch — JSON list of objects with the fields above
        (max 10 per call; each item is gated individually).

## `request_upload_link`

Generate the secure upload link the client uses to deliver their evidence folder.

Main engagement flow: call this AFTER evidence collection is complete
(all areas terminal in get_progress). Returns a chiarohq.com URL. Tell
the user to open it in a browser and drag the entire
~/Documents/chiaro-soc2 folder onto the page.

Contributor/delegate flow: available at ANY stage — a helper's upload is
inbound evidence for their topics, not the engagement handoff. Save what
they share into ~/Documents/chiaro-soc2/, zip it (data.zip_commands),
open the link (data.open_commands), and they drag the zip in.

The link is single-engagement, time-limited (default 90 days), and revocable.
Each call creates a NEW link — if you need to re-issue (e.g., the user lost the
URL), call again and the prior link still works until revoked.

Args:
    label: Optional human-readable label for the link
           (e.g., "Mock exam round 1"). Helps the audit team triage.

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
    prior_soc2: Only when the company is TRANSITIONING from another firm.
        Facts read from their most recent SOC 2 report (their file, read
        locally — we never receive it), as a dict:
        {"had_prior": true, "prior_type": "type1"|"type2",
         "prior_period": "e.g. 2024-01 to 2024-12", "prior_firm": "name",
         "focus_areas": ["exceptions or gaps to re-confirm are fixed"]}.
        This is scope context only. The prior report's verdicts are never
        our evidence — every applicable check is still self-assessed with
        the company's own real evidence. Omit for a first-time SOC 2; pass
        {"had_prior": false} to clear a mistakenly recorded transition.

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

Type II window: after a bundled Type I delivers, this same tool starts
the Type II observation window (the remaining bundle fee bills in two
installments, the first no earlier than seven days after the client's
notice). start_date: an ISO date (YYYY-MM-DD) IN THE FUTURE schedules the
window start instead of starting now — forward-only, never a past date.

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
    covers_attributes: JSON list of [control_ref, attribute_id] pairs this
        document helps satisfy. EXAMPLE: '[["POL-01","A1"],["POL-01","A2"]]'.
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
    covers_attributes: JSON list of [control_ref, attribute_id] pairs this
        evidence helps satisfy. EXAMPLE: '[["IAM-02","A1"],["IAM-02","A3"]]'.
        This is EXISTENCE TRACKING ONLY — "does the file include the thing
        the coverage check asks about, yes or no." NOT quality judgment.
        our audit team judges quality during review later. Required if the evidence
        corresponds to specific control attributes.
    source_command: How the file was obtained. One line, mirrors how_collected.md.
        Examples: "Okta admin > Reports > Users > Export 2026-05-15" or
        "aws iam get-credential-report --output json on prod 2026-05-15".
    source_system: Source system (e.g., "okta", "aws-production", "github").
    evidence_type: Short label: "cli_output" / "config" / "api_response" /
        "screenshot_note" / "verbal_description" / "export".
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
    snapshot: Type II observation windows only. Pass "opening" when this
        artifact is part of the opening configuration snapshot — the
        config state of an in-scope system captured when the window
        starts. Leave empty for all other evidence.

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
    segment_start: Optional ISO timestamp — only for a checkpoint pull that
        covers part of the window rather than all of it.
    segment_end: Optional ISO timestamp, as above.

## `undo_delegate`

Take back a delegated topic. The delegate's token is revoked
and the topic returns to your queue.

Args:
    topic_key: The topic to take back (from get_topic_delegation_status output)
