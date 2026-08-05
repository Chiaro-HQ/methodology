# The HIPAA Security Rule crosswalk

How the HIPAA Security Rule maps onto this control library: every
standard and every implementation specification of 45 CFR 164.308
through 164.316, one row each, with the controls that carry it and
the reasoning. The same data, with a rationale for every row, is in
[`framework/hipaa-crosswalk.json`](../framework/hipaa-crosswalk.json).

The numbers: 64 rows. 61 apply to the population this
method serves (software companies handling electronic protected
health information, almost always as business associates). 7 of
those resolve to three HIPAA-specific controls; the rest are carried
by the SOC 2 library, 6 of them through five HIPAA-gated
attributes added to existing controls. The 3 rows that do not
apply (health care clearinghouses, government entities contracting
under a memorandum of understanding, group health plans) are
recorded determinations in scoping, never silent assumptions.

## What this mapping is, and is not

It covers the Security Rule only (Subpart C). The Privacy Rule and
the Breach Notification Rule are legal obligations outside a
controls mapping, and no row here claims them. In an examination,
the mapping is delivered alongside the SOC 2 report as additional
information: the opinion is on the Trust Services Criteria, and
nothing in this crosswalk is a certification of HIPAA compliance.

The mapping targets the rule in force, verified against the current
regulatory text. The January 2025 proposed rule would make every
specification required and add explicit technical controls; final
action is anticipated in 2027. If it lands, this crosswalk changes
here before it changes in any engagement, like every other method
change.

Addressable specifications are handled the way the rule intends: an
addressable row may be met by an equivalent mechanism, and the
documented choice is the compliance artifact. Rows resolved that
way say so in their rationale.

## The three HIPAA controls

Applied only when the HIPAA add-on is in scope; defined in full in
[`framework/controls.json`](../framework/controls.json) alongside
every other control.

| Ref | Control | What it carries |
|---|---|---|
| HIP-01 | Business Associate Agreements | 164.308(b)(1), 164.308(b)(3), 164.314(a)(1), 164.314(a)(2)(i), 164.314(a)(2)(iii) |
| HIP-02 | Emergency Access Procedure | 164.312(a)(2)(ii) |
| HIP-03 | Security Documentation Retention | 164.316(b)(2)(i) |

## The HIPAA-gated attributes

Five attributes on existing controls that apply only with the HIPAA
add-on, so the ePHI-specific requirement is tested rather than
advised.

| Host control | Carries | Tests |
|---|---|---|
| RSK-01 (Annual Risk Assessment) | 164.308(a)(1)(ii)(A) | Risk assessment identifies the systems and data flows where ePHI is created, received, maintained, or transmitted |
| GOV-01 (Code of Conduct & Ethics) | 164.308(a)(1)(ii)(C) | The disciplinary process explicitly covers failures to comply with security policies and procedures, including those protecting ePHI |
| GOV-03 (Organizational Structure & Responsibilities) | 164.308(a)(2) | A security official is designated by name as responsible for developing and implementing the policies and procedures that protect ePHI (the HIPAA Security Officer role) |
| BCP-01 (BCP/DR Plan & Testing) | 164.308(a)(7)(ii)(C) | The contingency plan addresses how security protections for ePHI continue during emergency mode operations |
| DAT-03 (Data Retention & Deletion) | 164.310(d)(2)(iii), 164.310(d)(2)(iv) | Policy addresses local storage of ePHI on endpoints and removable media |

## The crosswalk

| Provision | Cite | Type | Resolution | Controls |
|---|---|---|---|---|
| Security management process | 164.308(a)(1)(i) | Required | Met through its specifications | RSK-01, RSK-02, EVL-02, POL-01 |
| Risk analysis | 164.308(a)(1)(ii)(A) | Required | Carried, with a HIPAA-gated attribute | RSK-01, AST-01 |
| Risk management | 164.308(a)(1)(ii)(B) | Required | Carried by the library | RSK-02, EVL-02 |
| Sanction policy | 164.308(a)(1)(ii)(C) | Required | Carried, with a HIPAA-gated attribute | GOV-01, PPL-04 |
| Information system activity review | 164.308(a)(1)(ii)(D) | Required | Carried by the library | MON-01, MON-02, IAM-05 |
| Assigned security responsibility | 164.308(a)(2) | Required | Carried, with a HIPAA-gated attribute | GOV-03, GOV-04 |
| Workforce security | 164.308(a)(3)(i) | Required | Met through its specifications | IAM-03, PPL-01, IAM-04 |
| Authorization and/or supervision | 164.308(a)(3)(ii)(A) | Addressable | Carried by the library | IAM-03, IAM-06 |
| Workforce clearance procedure | 164.308(a)(3)(ii)(B) | Addressable | Carried by the library | PPL-01 |
| Termination procedures | 164.308(a)(3)(ii)(C) | Addressable | Carried by the library | IAM-04 |
| Information access management | 164.308(a)(4)(i) | Required | Met through its specifications | IAM-03, IAM-05, IAM-06 |
| Isolating health care clearinghouse functions | 164.308(a)(4)(ii)(A) | Required | Not applicable, recorded determination | None |
| Access authorization | 164.308(a)(4)(ii)(B) | Addressable | Carried by the library | IAM-03, IAM-06 |
| Access establishment and modification | 164.308(a)(4)(ii)(C) | Addressable | Carried by the library | IAM-03, IAM-04, IAM-05 |
| Security awareness and training | 164.308(a)(5)(i) | Required | Carried by the library | PPL-03 |
| Security reminders | 164.308(a)(5)(ii)(A) | Addressable | Carried by the library | PPL-03, POL-02 |
| Protection from malicious software | 164.308(a)(5)(ii)(B) | Addressable | Carried, with prep guidance | END-01, PPL-03 |
| Log-in monitoring | 164.308(a)(5)(ii)(C) | Addressable | Carried by the library | MON-01, MON-02 |
| Password management | 164.308(a)(5)(ii)(D) | Addressable | Carried, with prep guidance | IAM-01, IAM-02 |
| Security incident procedures | 164.308(a)(6)(i) | Required | Carried by the library | INC-01 |
| Response and reporting | 164.308(a)(6)(ii) | Required | Carried by the library | INC-02, MON-02 |
| Contingency plan | 164.308(a)(7)(i) | Required | Carried by the library | BCP-01 |
| Data backup plan | 164.308(a)(7)(ii)(A) | Required | Carried by the library | BCP-02 |
| Disaster recovery plan | 164.308(a)(7)(ii)(B) | Required | Carried by the library | BCP-01, BCP-02 |
| Emergency mode operation plan | 164.308(a)(7)(ii)(C) | Required | Carried, with a HIPAA-gated attribute | BCP-01, BCP-02 |
| Testing and revision procedures | 164.308(a)(7)(ii)(D) | Addressable | Carried by the library | BCP-01 |
| Applications and data criticality analysis | 164.308(a)(7)(ii)(E) | Addressable | Carried by the library | BCP-01 |
| Evaluation | 164.308(a)(8) | Required | Carried by the library | EVL-01, RSK-01 |
| Business associate contracts and other arrangements | 164.308(b)(1) | Required | Resolved by a HIPAA control | HIP-01, VND-01 |
| Written contract or other arrangement | 164.308(b)(3) | Required | Resolved by a HIPAA control | HIP-01 |
| Facility access controls | 164.310(a)(1) | Required | Met through its specifications | NET-03, VND-02 |
| Contingency operations (facility access) | 164.310(a)(2)(i) | Addressable | Provider-inherited, monitored | VND-02, BCP-01 |
| Facility security plan | 164.310(a)(2)(ii) | Addressable | Provider-inherited, monitored | NET-03, VND-02 |
| Access control and validation procedures | 164.310(a)(2)(iii) | Addressable | Provider-inherited, monitored | NET-03, VND-02 |
| Maintenance records | 164.310(a)(2)(iv) | Addressable | Provider-inherited, monitored | VND-02, NET-03 |
| Workstation use | 164.310(b) | Required | Carried by the library | POL-01, END-02 |
| Workstation security | 164.310(c) | Required | Carried by the library | END-01 |
| Device and media controls | 164.310(d)(1) | Required | Met through its specifications | DAT-02, AST-01, DAT-03 |
| Disposal | 164.310(d)(2)(i) | Required | Carried by the library | DAT-02 |
| Media re-use | 164.310(d)(2)(ii) | Required | Carried by the library | DAT-02 |
| Accountability (media movement) | 164.310(d)(2)(iii) | Addressable | Carried, with a HIPAA-gated attribute | AST-01, DAT-03 |
| Data backup and storage (before movement) | 164.310(d)(2)(iv) | Addressable | Carried, with a HIPAA-gated attribute | BCP-02, DAT-03 |
| Access control | 164.312(a)(1) | Required | Met through its specifications | IAM-01, IAM-02, IAM-06, IAM-07 |
| Unique user identification | 164.312(a)(2)(i) | Required | Carried by the library | IAM-01 |
| Emergency access procedure | 164.312(a)(2)(ii) | Required | Resolved by a HIPAA control | HIP-02, IAM-07 |
| Automatic logoff | 164.312(a)(2)(iii) | Addressable | Carried, with prep guidance | END-01, IAM-01 |
| Encryption and decryption (at rest) | 164.312(a)(2)(iv) | Addressable | Carried by the library | DAT-01 |
| Audit controls | 164.312(b) | Required | Carried by the library | MON-01, IAM-07 |
| Integrity | 164.312(c)(1) | Required | Carried, with prep guidance | DAT-01, MON-01, BCP-02 |
| Mechanism to authenticate ePHI | 164.312(c)(2) | Addressable | Carried, with prep guidance | DAT-01, BCP-02 |
| Person or entity authentication | 164.312(d) | Required | Carried by the library | IAM-01, IAM-02 |
| Transmission security | 164.312(e)(1) | Required | Carried by the library | DAT-01 |
| Integrity controls (transmission) | 164.312(e)(2)(i) | Addressable | Carried by the library | DAT-01 |
| Encryption (transmission) | 164.312(e)(2)(ii) | Addressable | Carried by the library | DAT-01 |
| Business associate contracts or other arrangements (content) | 164.314(a)(1) | Required | Resolved by a HIPAA control | HIP-01 |
| Business associate contract content | 164.314(a)(2)(i) | Required | Resolved by a HIPAA control | HIP-01 |
| Other arrangements | 164.314(a)(2)(ii) | Required | Not applicable, recorded determination | None |
| Business associate contracts with subcontractors | 164.314(a)(2)(iii) | Required | Resolved by a HIPAA control | HIP-01 |
| Requirements for group health plans | 164.314(b) | Required | Not applicable, recorded determination | None |
| Policies and procedures | 164.316(a) | Required | Carried by the library | POL-01 |
| Documentation | 164.316(b)(1) | Required | Carried, with prep guidance | POL-01, POL-02 |
| Time limit (6 year retention) | 164.316(b)(2)(i) | Required | Resolved by a HIPAA control | HIP-03 |
| Availability (of documentation) | 164.316(b)(2)(ii) | Required | Carried by the library | POL-02 |
| Updates (to documentation) | 164.316(b)(2)(iii) | Required | Carried by the library | POL-01, RSK-01 |

Every row's rationale is in the JSON. If you think a mapping is
wrong, that is a concrete thing to point at: open an issue naming
the cite and the control.
