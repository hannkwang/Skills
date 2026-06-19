---
name: risk-assessment-writer
description: Generate a risk assessment paper, risk acceptance request, or remediation timeline extension request from a freeform description of a security vulnerability. Use this skill whenever the user describes a vulnerability and wants it turned into a decision-ready risk paper - including requests to "generate a risk assessment", "write up", "draft a paper for", or "justify an extension" for a CVE, VAPT finding, VMS finding, or bug bounty finding. Do not use for general CVE analysis or triage that does not involve producing a risk paper.
originator: Hannkwang
---

# Risk Assessment Writer

Generate a decision-ready risk assessment paper (timeline extension or formal risk acceptance) from whatever freeform description the user provides. The reader is GRC, who needs to decide in minutes, not reverse-engineer the engineer's thinking. Draft a complete paper from whatever inputs the user gives; mark any missing fields `[TBC]`.

## Core principles

1. **BLUF.** The Aim states the decision requested in one or two sentences. No throat-clearing.
2. **Traceability.** Every residual rating must read as Original Risk -> specific mitigations -> Residual Risk; a drop must be mechanically explained by reduced likelihood, impact, or both.
3. **Specificity over reassurance.** A mitigation names a control, who operates it, and what it blocks or detects - "we will monitor closely" is not one.
4. **Honest justification.** State a real constraint (vendor dependency, regression risk, change freeze, resourcing), not a preference.

## Inputs

Use whatever the user provides. Do not ask for missing information upfront — proceed immediately and mark any gaps `[TBC]` inline. Useful fields:

| Input | Why needed |
|---|---|
| System name, function, classification/criticality, exposure (internet-facing / intranet / air-gapped), user base | Drives Impact anchoring |
| Vulnerability: CVE + CVSS (or weakness class if no CVE) | Column 2 |
| Discovery source (VAPT / VMS / bug bounty / vendor advisory / threat intel) and date reported | Background |
| **Original remediation deadline** (user-supplied) | Baseline for the ask |
| **Requested new deadline** (user-supplied) | Defines extension duration |
| Mitigations in place or planned (with dates) | Column 7 |
| Committed remediation plan and owner | Justification |

## Document structure

ALWAYS use this exact four-section structure:

```
1. Aim
2. Background
3. Justification
4. Impact, Risk Assessment and Mitigation
```

## Section guidance

Keep the narrative sections tight: the **Aim is exactly one line** (BLUF). **Background and Justification are at most 3 lines each** — succinct, no padding; one line is fine if it covers the facts. (The §4 table carries the detail.) For a multi-vulnerability paper the 3-line limit applies *per vulnerability*.

### 1. Aim

One line stating: the approval sought (timeline extension / formal risk acceptance), the residual risk level to be carried (High / Medium / Low), and — if a timeline extension — original deadline → requested deadline and the resulting extension duration. BLUF only; no caveats.

Example: "To seek GRC approval for formal risk acceptance of one Medium residual risk vulnerability on System X."
Example: "To seek approval from GRC for a 3-month extension of the remediation timeline (from 30 Jun 2026 to 30 Sep 2026) for one Medium residual risk vulnerability on System X."

### 2. Background

Succinct, up to 3 lines, covering: the **system** (name, function, classification, exposure, users), the **vulnerability** (CVE+CVSS or weakness class, plus what it enables on THIS system), and **how/when found** (source, date reported, original deadline).

Example: "HRConnect — CONFIDENTIAL intranet-facing HR/payroll portal for ~2,500 staff — runs Apache OFBiz 18.12.x affected by CVE-2024-38856 (CVSS 9.8), an unauthenticated auth bypass allowing RCE as the service account; found via VMS scan on 20 May 2026, original deadline 20 Jun 2026."

### 3. Justification

Succinct, up to 3 lines, stating: why the team cannot remediate within the original timeline (pick a category below, cite the evidence), the committed remediation date and owner, **and** the review trigger (conditions forcing immediate re-assessment regardless of the approved deadline — public PoC/exploit release, observed exploitation, change in exposure). "Team is busy" without sequencing context is not a justification.

| Category | Example |
|---|---|
| Vendor dependency | Patch not yet released; fix scheduled in vendor's next release |
| Regression/operational risk | Patch breaks a critical integration; requires full regression cycle |
| Change constraints | Production freeze, audit period, peak operational period |
| Architectural work | Fix requires redesign (e.g., auth rework), not a patch |
| Resource/sequencing | Same team remediating a higher-severity finding first |

Example: "The vendor patch is due in Q3 2026, so remediation is committed by 30 Sep 2026 under the Platform Engineering Lead, and will be re-assessed immediately if a public PoC appears, exploitation is observed, or exposure changes."

### 4. Impact, Risk Assessment and Mitigation

Present as a table with EXACTLY these 10 columns:

| # | Column | What goes in it |
|---|---|---|
| 1 | No | Row number |
| 2 | Vulnerability Assessment | Short description + CVE; what exploitation looks like on THIS system |
| 3 | Likelihood | Pre-mitigation 5-level rating, with one-line reason anchored to the definitions below |
| 4 | Impact | Pre-mitigation 5-level rating, with one-line reason anchored to the definitions below |
| 5 | Original Risk | High/Medium/Low derived from the 5x5 matrix |
| 6 | Impact Assessment | Concrete worst-case consequence if exploited (what data, what service, what obligations - e.g., applicable regulatory reporting, contractual breach penalties) |
| 7 | Mitigation Measures | Numbered, specific, in-place-vs-planned marked with dates. Each mitigation names the control, who operates it, and what it blocks or detects. Any likelihood/impact drop in cols 8-9 must be traceable to a specific mitigation listed here that credibly acts on that axis |
| 8 | Residual Likelihood | Post-mitigation 5-level rating + which mitigation(s) caused the change |
| 9 | Residual Impact | Post-mitigation 5-level rating + which mitigation(s) caused the change |
| 10 | Residual Risk | High/Medium/Low derived from the same 5x5 matrix |

**Multi-vulnerability papers**: give each vulnerability its own row. Where mitigations are shared across rows, list the full mitigation once (first row where it applies) and reference it by name/number in subsequent rows. Never restate full mitigation text across rows.

## Risk matrix (5x5 likelihood x impact -> High/Medium/Low risk)

### Likelihood anchors (pre- or post-mitigation, same scale)

| Level | Anchor |
|---|---|
| Almost Certain | Exploitation expected: internet-facing, no auth required, weaponised exploit in active use in the wild |
| Likely | Public exploit/PoC available AND system reachable by the relevant threat actor; low attacker prerequisites |
| Possible | Exploit technique documented but requires meaningful prerequisites (auth, specific config, chaining) OR exposure is limited (intranet only) |
| Unlikely | No public exploit; high prerequisites (local access, privileged account); or strong compensating control directly blocks the vector |
| Rare | Exploitation requires insider access or physical access, or the vulnerable code path is unreachable in deployed configuration |

### Impact anchors

| Level | Anchor |
|---|---|
| Severe | Bulk loss/exposure of sensitive or RESTRICTED-classified data; sustained outage of a critical service; statutory breach-notification obligations near-certain |
| Major | Significant personal data exposure or material service degradation affecting external users; likely regulatory reporting |
| Moderate | Limited data exposure (small scope or low sensitivity) or internal service disruption; contained blast radius |
| Minor | Negligible data sensitivity; brief degradation; no external user impact |
| Insignificant | No meaningful confidentiality/integrity/availability consequence in deployed context |

### Risk derivation matrix

| Likelihood \ Impact | Insignificant | Minor | Moderate | Major | Severe |
|---|---|---|---|---|---|
| **Almost Certain** | Medium | Medium | High | High | High |
| **Likely** | Low | Medium | Medium | High | High |
| **Possible** | Low | Medium | Medium | Medium | High |
| **Unlikely** | Low | Low | Medium | Medium | Medium |
| **Rare** | Low | Low | Low | Medium | Medium |

Both Original Risk (column 5) and Residual Risk (column 10) MUST be derived from this matrix. Residual risk must not be higher than original. If the residual risk band equals the original band despite axis movement, state this explicitly in the Impact Assessment column — do not present unchanged risk as if it had been mitigated. Keep this disclosure out of the Aim.

If the user's organisation supplies a different matrix or anchors, use theirs and note the substitution.

## Workflow

1. **Draft** using the four-section structure and 10-column table. Apply the extension sanity-check flags.
2. **Flag gaps** rather than inventing facts: mark `[TBC]` inline and list all TBCs at the end.

## Output

Default to a Word document for formal submission, follow the docx skill and render the table properly there.

## Expert critique

The Critique is an **internal pre-submission stress-test for the author**, not part of the paper GRC signs against. Always produce it, but keep it separate from the submission body — place it after a page break (or deliver it as a separate section the author can strip before sending). It exists to let the author find and fix weaknesses before GRC does, not to hand the approver pre-written objections.

Append a `### Critique` block with two expert sub-sections. Each expert raises exactly **2 challenges** — the most significant for this specific paper. Each bullet leads with a **bold label**, then 1–2 sentences of challenge. Do not soften findings. Omit angles that are clearly inapplicable to this paper.

---

#### Cybersecurity Expert

Voice: A threat analyst stress-testing the technical substance of the paper. Goal: surface where the risk or vulnerability has been under-scoped, or where the mitigations do not credibly justify the residual rating.

Pick the **2 most significant** from these angles:

**Vulnerability scoping**
- Has the vulnerability been assessed in the context of THIS system's configuration and exposure, or is the CVSS base score being applied uncritically without environmental adjustment?
- Are chained exploitation paths (e.g., combining this CVE with another known weakness on the same system) considered, or dismissed without justification?
- Is the threat actor realistically scoped? Challenge "Unlikely" ratings that cite authentication as a barrier without accounting for the registered user base size and viability of credential stuffing, phishing, or insider misuse.

**Mitigation sufficiency and residual rating**
- Would failure of any single mitigation restore the original risk level? If yes, the residual rating overstates control effectiveness.
- Are the cited controls operationally validated in this environment, or listed on paper only?
- Challenge any two-level drops (e.g., High → Low) as requiring unusually strong, independently verifiable evidence.
- Are planned (not yet in-place) mitigations being credited in the residual rating? If so, the residual risk is forward-looking and contingent — flag this explicitly.

---

#### Risk and Compliance Expert

Voice: A Risk and Compliance officer stress-testing whether the paper satisfies the organisation's risk management framework — that the residual risk is formally accepted at the right level, that escalation thresholds are correctly applied, and that the paper gives the approver sufficient basis to own the decision.

Pick the **2 most significant** from these angles:

**Risk governance and escalation**
- Is the residual risk level consistent with the organisation's risk appetite and policy thresholds — i.e. is this risk one the organisation should accept at all, not merely who signs for it?
- Has the correct approving authority been identified? A residual risk on a system processing regulated data for a large user base may require DPO or CISO sign-off before GRC can act.
- Does the extension duration align with documented constraints, or is the total duration self-asserted by the requesting team? A change freeze alone does not justify the full extension period if remediation work could begin in parallel.
- If this is a repeat extension, challenge why the prior extension did not result in remediation and what is materially different this time.

**Residual risk defensibility**
- Could the approver reconstruct the rationale for the residual rating from this paper alone, without asking the author follow-up questions? If not, the paper is not decision-ready.
- Is the review trigger clause specific and enforceable (named events, named owner, named action), or a generic catch-all that provides no real governance value?
