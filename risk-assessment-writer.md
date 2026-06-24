---
name: risk-assessment-writer
description: Generate a risk assessment paper, risk acceptance request, or remediation timeline extension request from a freeform description. Use this skill whenever the user describes a risk and wants it turned into a decision-ready risk paper - including requests to "generate a risk assessment", "write up", "draft a paper for", or "justify an extension" for a CVE, VAPT finding, VMS finding, or bug bounty finding.
originator: Hannkwang
---

# Risk Assessment Writer

Generate a decision-ready risk assessment paper (timeline extension or formal risk acceptance) from whatever freeform description the user provides. The reader is the GRC committee. Draft a complete paper from whatever inputs the user gives; mark any missing fields `[TBC]`.

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

Keep the narrative sections tight: the **Aim is exactly one line** (BLUF). **Background and Justification are at most 2 lines each** — succinct, no padding; one line is fine if it covers the facts. (The §4 table carries the detail.) For a multi-vulnerability paper the 2-line limit applies *per vulnerability*.

**Write in complete sentences.** Every statement the reader sees — the narrative sections and the descriptive table cells (cols 2, 6, and 7) — must be a full grammatical sentence. Do not splice independent clauses with semicolons, and do not write in telegraphic note-form: no sentence fragments, no trailing "— basis for X" tags, no "(Category: …)" labels. Keep sentences short, but keep them whole. (The rating columns 3-5 and 8-10 are the exception: they carry a single band word only.)

### 1. Aim

One line stating: the approval sought (timeline extension / formal risk acceptance), the residual risk level to be carried (Low / Medium / Medium-High / High / Very High), and — if a timeline extension — original deadline → requested deadline and the resulting extension duration. BLUF only; no caveats.

Example: "To seek GRC approval for risk acceptance of one Medium residual risk vulnerability on System X."
Example: "To seek GRC approval for a 3-month extension of the remediation timeline (from 30 Jun 2026 to 30 Sep 2026) for one Medium residual risk vulnerability on System X."

For a multi-vulnerability paper, state the **count and the highest residual band carried** (with the distribution): e.g. "To seek GRC approval for risk acceptance of 3 vulnerabilities (1 High, 2 Medium residual) on System X."

### 2. Background

Succinct, up to 2 lines, covering: the **system** (name, function, classification, exposure, users), the **vulnerability** (CVE+CVSS or weakness class, plus what it enables on THIS system), and **how/when found** (source, date reported, original deadline).

Example: "HRConnect is a CONFIDENTIAL, intranet-facing HR and payroll portal serving about 2,500 staff. It runs Apache OFBiz 18.12.x, which is affected by CVE-2024-38856 (CVSS 9.8), an unauthenticated authentication bypass that allows remote code execution as the service account. The vulnerability was found by a VMS scan on 20 May 2026, against an original remediation deadline of 20 Jun 2026."

### 3. Justification

Succinct, up to 2 lines, stating: why the team cannot remediate within the original timeline, the committed remediation date and owner, or why a risk acceptance is required. Pick a category below and cite the evidence; "team is busy" without sequencing context is not a justification.

| Category | Example |
|---|---|
| Vendor dependency | Patch not yet released; fix scheduled in vendor's next release |
| Regression/operational risk | Patch breaks a critical integration; requires full regression cycle |
| Change constraints | Production freeze, audit period, peak operational period |
| Architectural work | Fix requires redesign (e.g., auth rework), not a patch |
| Resource/sequencing | Same team remediating a higher-severity finding first |

Example: "The vendor patch is due in Aug 2026, so remediation is committed by 30 Sep 2026 under Technical Services."

### 4. Impact, Risk Assessment and Mitigation

Present as a table with EXACTLY these 10 columns:

| # | Column | What goes in it |
|---|---|---|
| 1 | No | Row number |
| 2 | Vulnerability Assessment | A flowing, complete-sentence assessment that weaves four components together **without labelling them**: the *weakness/vulnerability* (the flaw and where it sits on THIS system, with CVE and CVSS if available, otherwise the weakness class), the *threat* (the realistic threat actor and attack path given the system's exposure and configuration), the *consequence* (what the attacker achieves technically on success), and the *impact* (the resulting harm across the data, operational, and public/reputational dimensions). Follow this shape: "In the event that &lt;weakness&gt; is exploited by an attacker via &lt;threat&gt;, &lt;consequence&gt; may occur, which would result in &lt;impact to system/users&gt;." Where the impact spans several dimensions, extend into a second sentence. This column also carries the rationale for the Likelihood band. Cols 3-5 and 8-10 hold bands only |
| 3 | Likelihood | Pre-mitigation band only — one of Highly Likely / Likely / Possible / Unlikely / Rare (per the anchors below). No reasoning here; it lives in col 2 |
| 4 | Impact | Pre-mitigation band only — one of Very Severe / Severe / Moderate / Minor / Negligible (per the anchors below). No reasoning here; it lives in col 6 |
| 5 | Original Risk | Band only — one of Low / Medium / Medium-High / High / Very High, derived from the 5x5 matrix |
| 6 | Impact Assessment | The concrete worst case that anchors the Impact band, stated in specifics rather than the qualitative dimensions of col 2: the volume and sensitivity of data at stake, the criticality of the service affected, and the formal obligations triggered (for example regulatory breach-notification timelines, card-scheme fines, or contractual penalties) |
| 7 | Mitigation Measures | Numbered, specific, in-place-vs-planned marked with dates. Each mitigation names the control (Preventive/Detective/Responsive), who operates it, and what it blocks or detects. Any likelihood/impact drop in cols 8-9 must be traceable to a specific mitigation listed here that credibly acts on that axis |
| 8 | Residual Likelihood | Post-mitigation band only (same scale as col 3); the mitigation(s) driving the change are named in col 7, not here |
| 9 | Residual Impact | Post-mitigation band only (same scale as col 4); the mitigation(s) driving the change are named in col 7, not here |
| 10 | Residual Risk | Band only — one of Low / Medium / Medium-High / High / Very High, derived from the same 5x5 matrix |

**Multi-vulnerability papers**: give each vulnerability its own row. Where mitigations are shared across rows, list the full mitigation once (first row where it applies) and reference it by name/number in subsequent rows. Never restate full mitigation text across rows.

## Risk matrix (5x5 likelihood x impact -> 5-band risk: Low / Medium / Medium-High / High / Very High)

The same anchors below apply to both the pre-mitigation ratings (cols 3-4) and the residual ratings (cols 8-9) — one scale, used twice.

### Likelihood anchors (informed by discoverability, exploitability, and exposure)

| Level | Anchor |
|---|---|
| Highly Likely | Exploitation expected: internet-facing, no auth required, weaponised exploit in active use in the wild |
| Likely | Public exploit/PoC available AND system reachable by the relevant threat actor; low attacker prerequisites |
| Possible | Exploit technique documented but requires meaningful prerequisites (auth, specific config, chaining) OR exposure is limited (intranet only) |
| Unlikely | No public exploit; high prerequisites (local access, privileged account); or strong compensating control directly blocks the vector |
| Rare | Exploitation requires insider access or physical access, or the vulnerable code path is unreachable in deployed configuration |

### Impact anchors on Confidentiality x Integrity x Availability

| Level | Anchor |
|---|---|
| Very Severe | Bulk loss/exposure of sensitive or RESTRICTED-classified data; sustained outage of a critical service; statutory breach-notification obligations near-certain |
| Severe | Significant personal data exposure or material service degradation affecting external users; likely regulatory reporting |
| Moderate | Limited data exposure (small scope or low sensitivity) or internal service disruption; contained blast radius |
| Minor | Very low data sensitivity; brief degradation; no external user impact |
| Negligible | No meaningful confidentiality/integrity/availability consequence in deployed context |

### Risk derivation matrix

| Likelihood \ Impact | Negligible | Minor | Moderate | Severe | Very Severe |
|---|---|---|---|---|---|
| **Highly Likely** | Medium | Medium | High | Very High | Very High |
| **Likely** | Low | Medium | Medium-High | High | Very High |
| **Possible** | Low | Medium | Medium | Medium-High | High |
| **Unlikely** | Low | Low | Medium | Medium | Medium-High |
| **Rare** | Low | Low | Low | Medium | Medium |

Both Original Risk (column 5) and Residual Risk (column 10) MUST be derived from this matrix. Residual risk must not be higher than original. If the residual risk band equals the original band despite axis movement, state this explicitly in the Impact Assessment column — do not present unchanged risk as if it had been mitigated. Keep this disclosure out of the Aim.

A **High or Very High residual** risk should not be presented as routinely acceptable — it tests the organisation's risk appetite, not merely who signs, and typically requires elevated approval (e.g. CISO) before GRC can act.

## Workflow

1. **Gather** the inputs the user gave; do not ask for more upfront.
2. **Draft** the four-section paper and the 10-column §4 table.
3. **Derive** Original Risk (col 5) and Residual Risk (col 10) from the matrix.
4. **Self-check before output:**
   - Every residual drop traces to a named mitigation in col 7 that credibly acts on that axis.
   - Residual band ≤ Original band; if equal despite axis movement, state so in col 6.
   - Planned (not-yet-in-place) mitigations are flagged as contingent.
   - Any two-level drop carries explicit justification.
5. **Write** the post-break Residual Risk Statement, then the Critique.
6. **Flag gaps** rather than inventing facts: mark `[TBC]` inline and list all TBCs at the end.

## Output

Default to a Word document for formal submission: if a docx/Word-generation skill is available, use it and render the §4 table properly there; otherwise deliver a well-formed Markdown document with the §4 table intact.

## Residual Risk Statement

Append a **one-line residual risk statement**. It is **neutral and not part of the paper** GRC signs against — its only job is to translate the residual rating (col 10) into plain descriptive language so the reader understands what the outstanding residual risk actually *is*. Describe, do not argue: neither downplay nor amplify the risk, and make no recommendation.

Derive it from §4 — introduce no new facts. State the **residual band** and then describe, in plain terms, what remains: what could still happen, to what (data / service / system), and how exposed it is after the mitigations. For a multi-vulnerability paper, describe the highest residual band carried and note the count at that band.

Example: "Residual risk is Medium: after WAF virtual-patching and restricted network access, the unauthenticated RCE on HRConnect (CVE-2024-38856) remains exploitable only by an actor already on the internal network, who could on success read or alter payroll records for ~2,500 staff."

## Expert critique

The Critique is an **internal pre-submission stress-test for the author**, not part of the paper GRC signs against. Always produce it, but keep it separate from the submission body — place it after a page break (a `---` separator in a Markdown deliverable). It exists to let the author find and fix weaknesses before GRC does, not to hand the approver pre-written objections.

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
- Has the correct approving authority been identified? A residual risk on a system processing regulated data for a large user base may require CISO sign-off before GRC can act.
- Does the extension duration align with documented constraints, or is the total duration self-asserted by the requesting team? A change freeze alone does not justify the full extension period if remediation work could begin in parallel.
- If this is a repeat extension, challenge why the prior extension did not result in remediation and what is materially different this time.

**Residual risk defensibility**
- Could the approver reconstruct the rationale for the residual rating from this paper alone, without asking the author follow-up questions? If not, the paper is not decision-ready.
