# Worked Example — Risk Assessment Paper

One complete, realistic example to anchor the output shape. Load on demand when drafting; do not copy the facts — they are illustrative. The point is the structure, the traceability (Original → mitigations → Residual), and the dual-expert critique.

---

# Risk Assessment — Remediation Timeline Extension Request
**System:** Claims Processing Portal | **Date:** 17 Jun 2026

## 1. Aim

To seek approval from GRC for a **3-month extension** of the remediation timeline (from **31 May 2026** to **31 Aug 2026**) for one **Medium** residual risk vulnerability on the Claims Processing Portal.

## 2. Background

Claims Processing Portal — a CONFIDENTIAL, internet-facing web application for ~50,000 registered users (agents and policyholders) processing personal, financial, and health data — runs Apache Tomcat 10.1.x affected by CVE-2025-4471 (CVSS 9.8), a partial PUT request smuggling flaw letting an unauthenticated attacker upload and execute arbitrary JSP files (full RCE on the app servers); identified via VMS scan on 1 May 2026, original deadline 31 May 2026.

## 3. Justification

Cannot remediate by 31 May because the patch (Tomcat 10.1.35) changes HTTP/1.1 chunked transfer handling and conflicts with the ClaimsSession v2.3 library, requiring full regression across 47 transaction flows (regression/operational risk), compounded by the 1–28 Jun financial year-end change freeze (Freeze Memo REF-2026-047) — committed by 31 Aug 2026 (regression from 1 Jul, UAT by 25 Jul), owner Platform Engineering Team Lead [TBC name]; re-assess immediately on public PoC release for CVE-2025-4471, observed exploitation, or any change to internet exposure.

## 4. Impact, Risk Assessment and Mitigation

| # | Vulnerability Assessment | Likelihood | Impact | Original Risk | Impact Assessment | Mitigation Measures | Residual Likelihood | Residual Impact | Residual Risk |
|---|---|---|---|---|---|---|---|---|---|
| 1 | **CVE-2025-4471** — Tomcat partial PUT RCE (CVSS 9.8). Unauthenticated attacker uploads and executes JSP files, achieving OS-level code execution on the portal app servers. | **Likely** — internet-facing, no auth to reach the vulnerable endpoint; technique publicly documented; no confirmed weaponised exploit in the wild yet. | **Severe** — OS-level access to servers holding CONFIDENTIAL data for 50,000 users; bulk exfiltration plausible in a single event; statutory breach-notification obligations near-certain. | **High** | Full server compromise enabling bulk exfiltration of personal/financial data for ~50,000 users; mandatory regulatory breach notification; potential shutdown of claims processing. | 1. **(In place, 5 May 2026)** WAF rule WA-2025-4471-01 blocking malformed PUT patterns to all portal endpoints — WA-01. 2. **(In place, 3 May 2026)** IPS signature CVE-2025-4471-SIG blocking known exploitation traffic at the perimeter — NW-04. 3. **(In place)** Egress ACL on app servers restricting outbound to 6 allowlisted endpoints — NW-01. 4. **(In place)** Network segmentation: DB tier reachable only from app tier on port 5432; no interactive protocols — NW-02. 5. **(In place)** SOC alert on WAF PUT-block events, 15-min triage SLA, containment: auto-block source IP and isolate host — MN-01. | **Possible** — WA-01 + NW-04 reduce exploitation probability; WAF bypass risk is documented and neither validates server-side execution, so a determined actor retains a viable path. | **Major** — NW-01 limits exfiltration to 6 known endpoints; NW-02 limits lateral movement to the DB tier; bulk exfiltration probability reduced but significant data exposure remains plausible. | **Medium** |

**Matrix verification:**
- Original: Likely × Severe → **High** ✓
- Residual: Possible × Major → **Medium** ✓
- Residual (Medium) is lower than Original (High) — genuine reduction, both axes moved. ✓
- Mitigation validity: WA-01/NW-04 reduce Likelihood and apply to web-app/network-exploitable CVEs ✓; NW-01/NW-02 reduce Impact and apply to exfiltration/lateral-movement ✓.

---

### Critique

#### Cybersecurity Expert

- **WAF as a single point of failure for the likelihood drop:** The Likely → Possible drop rests on WA-01 and NW-04. The catalog notes WAF rules carry documented bypass risk and rarely justify more than a one-level drop. If either is circumvented (HTTP/2 path, encoding variation, mutated payload), likelihood returns to Likely and residual risk returns to High. The paper does not state whether bypass variants were tested against the rules.
- **NW-01 egress ACL credited without validation evidence:** The Severe → Major impact drop depends on NW-01 restricting outbound to 6 endpoints. The paper does not state whether the ACL was independently validated from within the app-server zone. A misconfigured allowlist entry would leave the bulk-exfiltration (Severe) scenario reachable, overstating the Major rating.

#### Risk and Compliance Expert

- **Extension duration not fully evidenced by the constraints cited:** The change freeze justifies ~1 month (to late June); the remaining 2 months rest on a 47-flow regression cycle whose 9-week duration is not explained (resourcing? sequencing?). An auditor would find the 3-month total asserted rather than evidenced. Attach or reference the regression test plan.
- **Approving authority for CONFIDENTIAL data not confirmed:** The paper requests GRC approval for Medium residual risk on a system holding personal, financial, and health data for 50,000 users over 3 months. It does not state whether CISO or data-protection sign-off is required before GRC can act. The approval chain should be confirmed and stated explicitly.

---

**[TBC]**
- Platform Engineering Team Lead name (Section 3, remediation owner)
