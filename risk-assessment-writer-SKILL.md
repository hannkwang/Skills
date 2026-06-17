---
name: risk-assessment-writer
description: Draft, write, structure, or review risk assessment papers, risk acceptance requests, and remediation timeline extension requests for security vulnerabilities. Use this skill whenever the user wants to produce or critique such a document - including requests to "write up", "draft a paper for", "review this risk acceptance", or "justify an extension" for a CVE, VAPT finding, VMS finding, or bug bounty finding. Do not use for general CVE analysis or triage that does not involve producing or reviewing a risk paper.
originator: Hannkwang
---

# Risk Assessment Writer

Help engineers write decision-ready risk assessment papers requesting an extension of a vulnerability remediation timeline (or formal risk acceptance). The reader is GRC, who needs to decide in minutes, not reverse-engineer the engineer's thinking. Draft a complete paper from whatever inputs the user provides; mark any missing fields `[TBC]` and list all TBCs at the end.

## Dependencies

Before proceeding, also load:
- `mitigations-catalog.md` — approved controls catalog for column 7; load when filling Mitigation Measures, not before

## Core principles

1. **BLUF.** The Aim states the decision requested in one or two sentences. No throat-clearing.
2. **Traceability.** Every residual rating must read as Original Risk -> specific mitigations -> Residual Risk; a drop must be mechanically explained by reduced likelihood, impact, or both.
3. **Specificity over reassurance.** A mitigation names a control, who operates it, and what it blocks or detects - "we will monitor closely" is not one.
4. **Honest justification.** State a real constraint (vendor dependency, regression risk, change freeze, resourcing), not a preference.

## Inputs

Use whatever the user provides. Do not ask for missing information upfront — proceed immediately and mark any gaps `[TBC]` inline, then list all TBCs at the end of the draft. Useful fields:

| Input | Why needed |
|---|---|
| System name, function, classification/criticality, exposure (internet-facing / intranet / air-gapped), user base | Drives Impact anchoring |
| Vulnerability: CVE + CVSS (or weakness class if no CVE) | Column 2 |
| Discovery source (VAPT / VMS / bug bounty / vendor advisory / threat intel) and date reported | Background 2.3 |
| **Original remediation deadline** (user-supplied) | Baseline for the ask |
| **Requested new deadline** (user-supplied) | Defines extension duration |
| Mitigations in place or planned (with dates) | Column 7 |
| Committed remediation plan and owner | Justification 3.1 |

**Extension sanity check**: compute the extension duration (new deadline minus original deadline) and state it explicitly in the Aim. Apply these flags in the draft so the approver sees them without asking:

| Condition | Action |
|---|---|
| Residual risk High and extension > 1 month | Add a highlighted note: extended carriage of High residual risk; recommend interim re-review at the 1-month mark |
| Any second or subsequent extension for the same finding | State the extension history (count + cumulative duration) in the Aim |
| Extension > 6 months | Recommend converting to formal risk acceptance with scheduled re-assessment instead of a timeline extension |

## Document structure

ALWAYS use this exact four-section structure:

```
1. Aim
2. Background
3. Justification
4. Impact, Risk Assessment and Mitigation
```

## Section guidance

### 1. Aim

State in 1 line:
- What approval is sought (extension of remediation timeline / risk acceptance)
- The residual risk level to be carried (High / Medium / Low)
- Original deadline, requested deadline, and the resulting extension duration

Example: "To seek approval from GRC for a 3-month extension of the remediation timeline (from 30 Jun 2026 to 30 Sep 2026) for one Medium residual risk vulnerability on System X."

### 2. Background

One line each: **System** (name, function, classification, exposure, users); **Vulnerability** (CVE+CVSS, or weakness class if no CVE, plus what it enables on THIS system); **Source/dates** (how found, date reported, original deadline). Multiple vulns: one line per CVE/source; don't bundle unrelated risks.

### 3. Justification

One line: why can't the team fix this within the original timeline? Pick a category below, cite the evidence (vendor advisory, test results, freeze memo), and state the committed remediation date + owner. "Team is busy" without sequencing context is not a justification.

| Category | Example |
|---|---|
| Vendor dependency | Patch not yet released; fix scheduled in vendor's next release |
| Regression/operational risk | Patch breaks a critical integration; requires full regression cycle |
| Change constraints | Production freeze, audit period, peak operational period |
| Architectural work | Fix requires redesign (e.g., auth rework), not a patch |
| Resource/sequencing | Same team remediating a higher-severity finding first |

Always include a **review trigger**: conditions forcing immediate re-assessment regardless of the approved deadline (public PoC/exploit release, observed exploitation, change in exposure).

### 4. Impact, Risk Assessment and Mitigation

Present as a table with EXACTLY these 10 columns:

| # | Column | What goes in it |
|---|---|---|
| 1 | No | Row number |
| 2 | Vulnerability Assessment | Short description + CVE; what exploitation looks like on THIS system |
| 3 | Likelihood | Pre-mitigation 5-level rating, with one-line reason anchored to the definitions below |
| 4 | Impact | Pre-mitigation 5-level rating, with one-line reason anchored to the definitions below |
| 5 | Original Risk | High/Medium/Low derived from the 5x5 matrix |
| 6 | Impact Assessment | Concrete worst-case consequence if exploited (what data, what service, what obligations - e.g., PDPA breach reporting) |
| 7 | Mitigation Measures | Numbered, specific, in-place-vs-planned marked with dates. Select from `mitigations-catalog.md` by control ID (load that file when filling this column, not before). Any likelihood/impact drop in cols 8-9 must use a control the catalog's "Reduces" column lists for that axis. Do not invent products or controls not in the catalog; if no entry fits, write the mitigation and mark it [NON-STANDARD] for reviewer attention |
| 8 | Residual Likelihood | Post-mitigation 5-level rating + which control ID(s) caused the change |
| 9 | Residual Impact | Post-mitigation 5-level rating + which control ID(s) caused the change |
| 10 | Residual Risk | High/Medium/Low derived from the same 5x5 matrix |

**Multi-vulnerability papers**: give each vulnerability its own row. Where mitigations are shared across rows, list the full mitigation once (first row where it applies) and reference it by control ID in subsequent rows (e.g., "NW-01 as per row 1"). Never restate full mitigation text across rows.

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

Both Original Risk (column 5) and Residual Risk (column 10) MUST be derived from this matrix. After drafting, explicitly verify each row: look up the (Likelihood, Impact) cell and confirm it matches the stated risk level. Do the same for the residual pair.

If the user's organisation supplies a different matrix or anchors, use theirs and note the substitution.

## Workflow

1. **Read** `mitigations-catalog.md` when filling column 7.
2. **Draft** using the four-section structure and 10-column table. Apply the extension sanity-check flags.
3. **Flag gaps** rather than inventing facts: mark `[TBC]` inline and list all TBCs at the end.

## Output

Default to a Markdown document with the table. If the user asks for a Word document or formal submission, follow the docx skill and render the table properly there.

## Expert critique

After the RECOMMENDATION, always append a `### Critique` block. Write it in the voice of a senior cybersecurity reviewer or internal auditor stress-testing the paper before it reaches the approving authority. The goal is to surface weaknesses the approver would ask about — before they ask.

Present the critique as a bulleted list of exactly **3 items** — the three most significant challenges for this specific paper. Rank by potential to change the approver's decision. Each bullet leads with a short label in bold, then one or two sentences of challenge. Do not soften findings; an auditor who finds nothing to challenge is not doing their job.

Draw from the following angles when selecting the top 3:

**Residual risk rating**
- Is the residual rating mechanically supported, or does it rely on a control that has not been operationally validated in this environment?
- Would failure of any single mitigation restore the original risk level? If yes, the residual rating may be overstated.
- Challenge any two-level drops (e.g., High → Low) as requiring unusually strong evidence.

**Justification scrutiny**
- Is the stated constraint externally verifiable (vendor advisory, test results, freeze memo) or self-asserted by the team requesting the extension?
- Does the extension duration match the constraint? A 2-month vendor delay does not justify a 4-month extension.
- Is this the first extension for this finding? If not, challenge why the prior extension did not result in remediation.
- "Authenticated" as a prerequisite: challenge whether a large registered user base (e.g., 500,000 accounts) is a meaningful barrier, given the viability of credential stuffing, phishing, or insider misuse.s

Omit angles that are clearly not applicable (e.g., do not probe WAF bypass if no WAF is in the paper). Keep the critique honest but proportionate — a Low residual risk with strong, specific mitigations warrants fewer challenges than a High residual risk with thin controls.
