# Risk Assessment Paper - Template and Worked Examples

## Template

```
RISK ASSESSMENT: [System Name] - [Vulnerability short name / CVE]
Date: [date]    Prepared by: [name/team]    Approving authority: [per tiering table]

1. AIM
To seek approval from [approver] for [extension of remediation timeline / risk
acceptance] for [N] [High/Medium/Low] residual risk vulnerability(ies) on
[System], from [original deadline] to [requested deadline] ([X]-month extension).
[If applicable: This is the [2nd/3rd] extension for this finding; cumulative
extension [Y] months.]

2. BACKGROUND
2.1 System: [name], [function], [classification/criticality], [exposure],
    [user base].
2.2 Vulnerability: [CVE-YYYY-NNNNN, CVSS X.X]. [Plain-language description of
    what exploitation enables on this system.]
2.3 Source: Identified via [VAPT / VMS scan / bug bounty / vendor advisory /
    threat intel] on [date]. Original remediation deadline: [date].

3. JUSTIFICATION
3.1 [Constraint category]: [specific reason with evidence].
3.2 Committed remediation plan: [action] by [requested deadline], owned by [team].
3.3 Review trigger: re-assess immediately upon [public PoC/exploit release /
    observed exploitation in the wild / change in system exposure].

4. IMPACT, RISK ASSESSMENT AND MITIGATION
[10-column table]

RECOMMENDATION
[One sentence restating the ask, the residual risk to be carried, the expiry
date, and any sanity-check notes (High-residual >1 month, repeat extension,
>6-month conversion-to-risk-acceptance recommendation).]
```

## Worked example 1 - web application CVE

Scenario: internet-facing appeals portal, SSRF in an embedded library, vendor patch due next quarter. Original deadline 30 Jun 2026, requested 30 Sep 2026 (3-month extension).

| No | Vulnerability Assessment | Likelihood | Impact | Original Risk | Impact Assessment | Mitigation Measures | Residual Likelihood | Residual Impact | Residual Risk |
|---|---|---|---|---|---|---|---|---|---|
| 1 | CVE-2026-1234 (CVSS 8.1) - SSRF in PDF rendering library used by the portal upload function. Exploitation allows requests to internal endpoints from the app server. | Likely - public exploit available; internet-facing; no auth required to reach upload function. | Major - app server zone has reachability to internal APIs holding personal data; significant exposure plausible with likely regulatory reporting. | High | Worst case: unauthorised retrieval of personal data of appeal applicants via internal API enumeration, triggering PDPA breach assessment and notification obligations; reputational impact to the agency. | 1. (In place, 02 Jun 2026) WA-01 WAF virtual patch blocking URL-bearing payloads to the upload endpoint. 2. (In place) NW-01 egress ACL restricting app server outbound calls to an allowlist of 2 required internal endpoints. 3. (In place) MN-01 SOC alert on denied egress attempts, 15-min triage SLA. 4. (Planned, 15 Jul 2026) AP-02 disable server-side PDF preview; serve uploads download-only pending vendor patch. | Possible - WA-01 raises exploitation difficulty, but SSRF filter bypasses are documented; meaningful prerequisites now required. | Moderate - NW-01 caps reachable internal surface to 2 endpoints, neither exposing bulk personal data; contained blast radius. | Medium |

Matrix verification: Likely x Major = High (original). Possible x Moderate = Medium (residual). Both match the 5x5 matrix.

Why this row passes review:
- Likelihood drop justified by a Likelihood-reducing control (WA-01); impact drop by an Impact-reducing control (NW-01). MN-01 is supporting only - it is not credited with the rating change (RA-09).
- Each mitigation carries a control ID, status, and date (RA-10).
- Worst case names the data, the obligation (PDPA), and the consequence - not generic "data breach".

## Worked example 2 - OS/infrastructure patching extension

Scenario: fleet of 40 intranet application servers on an OS version with a kernel privilege-escalation CVE. Vendor patch available but breaks the middleware agent in testing; middleware vendor fix due in 2 months. Original deadline 15 Jul 2026, requested 15 Sep 2026 (2-month extension). This is the most common extension pattern: the mitigation toolset differs from web-app cases - EDR, segmentation, and hardening rather than WAF.

| No | Vulnerability Assessment | Likelihood | Impact | Original Risk | Impact Assessment | Mitigation Measures | Residual Likelihood | Residual Impact | Residual Risk |
|---|---|---|---|---|---|---|---|---|---|
| 1 | CVE-2026-5678 (CVSS 7.8) - local privilege escalation in OS kernel across 40 intranet app servers. Requires existing low-privilege code execution on the host; grants root. | Possible - public PoC exists, but exploitation requires prior foothold on an intranet host (meaningful prerequisite); servers not internet-facing. | Major - root on app servers grants access to service accounts and lateral movement paths toward data tier hosting personal data. | Medium | Worst case: attacker with an initial foothold escalates to root, harvests service credentials, and pivots to the database tier; potential bulk personal data exposure and PDPA notification obligations. | 1. (In place) EP-02 EDR with auto-isolate policy on all 40 servers; kernel-exploit behavioural detection enabled. 2. (In place) NW-02 server zone segmentation; data-tier access restricted to application service ports only, no interactive protocols. 3. (In place, 20 Jun 2026) HD-01 removal of unnecessary local accounts and SUID binaries per hardening baseline review. 4. (Planned, 15 Sep 2026) OS patch deployment fleet-wide once middleware vendor fix (due 15 Aug 2026) passes regression. | Unlikely - EP-02 behavioural detection plus HD-01 hardening directly raise the cost of the escalation step; no change in exposure but strong compensating controls block the vector. | Moderate - NW-02 confines post-escalation movement to service ports; bulk data-tier access requires further exploitation; contained blast radius. | Low |

Matrix verification: Possible x Major = Medium (original). Unlikely x Moderate = Low (residual). Both match the 5x5 matrix.

Notes specific to infra cases:
- The justification category is regression/operational risk, evidenced by failed test results and the middleware vendor's committed fix date.
- Multi-row variant: if several CVEs on the same fleet share mitigations 1-3, state them fully in row 1 and reference "EP-02, NW-02, HD-01 as per row 1" in later rows (RA-11).

## Counter-example (would be rejected)

| ... | Mitigation Measures | Residual Risk |
|---|---|---|
| ... | 1. Team will monitor the system closely. 2. Antivirus is installed. 3. Will patch when vendor releases fix. | Low |

Rejected because: monitoring alone cannot lower likelihood (RA-09); AV is a pre-existing baseline control restated as if new (RA-08) and is not catalogued as effective against this vulnerability class; "patch later" is the justification, not a mitigation; the High -> Low drop fails matrix traceability (RA-05/06/07).
