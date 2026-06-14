---
name: im8-applicability-check
description: Determine whether IM8 applies to a system, and guide the user through the questions needed to make that call. Use this skill whenever the user asks "does IM8 apply to...", "is this system in IM8 scope", "do we need IM8 compliance for...", "is this exempt from IM8", or describes a system and wants to know its IM8 obligations - including borderline cases involving systems built outside GEN, commercial/cloud systems, or systems operated by a CLG (commercial-linked / third party). Do not use for assessing compliance against specific IM8 controls once applicability is already settled.
---

# IM8 Applicability Check

Decide whether IM8 applies to a system. The common failure is deciding on the wrong basis - people assume "built outside GEN" or "run by a vendor" means IM8 does not apply. It does not work that way.

This skill answers ONE gate question: *does IM8 apply at all?* It does not assess which controls apply, or whether a waiver is justified - those come after, and are out of scope here.

> CLG = commercial-linked / third-party entity operating or building on the agency's behalf (e.g., a vendor or appointed operator).

## Governing principle

Decide on three substantive factors - **Government data, agency function, and accountability** - NOT on where the system was built or who operates it.

If a user wants the reasoning behind any part of this logic (e.g., "why doesn't vendor-hosting remove applicability?"), read and draw on `references/rationale.md`.

## The scope test (IM8 clauses 1.1-1.3)

IM8 applies if the system meets **any one** of these:

| # | Trigger | Clause | Plain meaning |
|---|---|---|---|
| 1 | Handles Government data | 1.1 | The system stores, processes, or transmits data the Government owns or is responsible for |
| 2 | Connects to GEN | 1.2 | The system has a network connection to the Government enterprise network |
| 3 | Supports the agency's functions | 1.3 | The system enables or underpins work the agency is mandated to do |

Triggers are joined by OR - tripping any one is sufficient. Default position: **if the agency owns the asset, or remains accountable for the system or its risk, IM8 applies.**

## What does NOT remove applicability

State these clearly when relevant - they are the usual misconceptions:

- **Built outside GEN** - may support a waiver or alternative controls, but does not make IM8 non-applicable.
- **Operated by a CLG / commercial third party** - may justify alternative controls or a waiver, but accountability for the data and risk typically remains with the agency.
- **Hosted on commercial cloud** - hosting location changes *which* controls apply, not *whether* IM8 applies.

The distinction that matters: applicability (does IM8 apply at all) is separate from the control set / waiver question (which controls, or what alternative). A system can be in scope AND qualify for a waiver or alternative controls. Do not collapse the two - conflating them is the root of most flawed exemption claims.

## Gathering the answers

First, extract what the user has already told you - do NOT re-ask anything their description already answers. Then, for whatever remains unanswered among the four core questions, ask them in ONE batch using the `ask_user_input` tool (tappable options, not prose). Do not interrogate one question at a time.

Core questions (triggers + accountability):

1. **Government data**: Does the system store, process, or transmit any Government data (including personal data the agency is responsible for)? [Yes / No / Unsure]
2. **GEN connectivity**: Does the system connect to GEN, or exchange data with any GEN-connected system? [Yes / No / Unsure]
3. **Agency function**: Does the system support or enable any of the agency's official functions or services? [Yes / No / Unsure]
4. **Ownership/accountability**: Does the agency own the asset, or remain accountable for the system or its risk (e.g., via contract, MOU, or as data controller)? [Yes / No / Partly]

Context questions - ask ONLY if the determination is "Applies" and the user needs the waiver/alternative-controls note. They do NOT affect applicability:

5. Where was the system built / where does it run? [Within GEN / Outside GEN / Commercial cloud / Vendor-hosted]
6. Who operates it day to day? [Agency / CLG or third party / Mixed]

## How to decide

Evaluate in this order:

```
1. If ANY core question = Unsure
   -> IM8 APPLIES (pending confirmation). List each Unsure item to confirm.
      (Unsure is not No. Scope in, then waive later if justified.)

2. Else if ANY of Q1, Q2, Q3 = Yes
   -> IM8 APPLIES (clear trigger). Cite which clause(s).

3. Else if Q4 = Yes / Partly
   -> IM8 APPLIES (accountability default).

4. Else (all of Q1-Q4 = No)
   -> IM8 likely DOES NOT APPLY. Rare - verify each "No" is genuine before
      recording a negative determination.
```

Why Unsure scopes in: wrongly scoping in costs a review cycle; wrongly scoping out leaves an ungoverned system handling Government data. The errors are not symmetric, so default to the recoverable one.

## Output format

ALWAYS produce this short structured result - this is a gate decision, not a report:

```
IM8 APPLICABILITY: [Applies / Applies pending confirmation / Does not apply]
Strength of call: [Clear trigger / Accountability default only / Negative - verify]

Basis:
- [Which trigger(s) met and the clause (1.1/1.2/1.3), or the accountability
   default, in one line each]

Built/operated outside GEN or by a CLG?
- [If yes: note this may support a waiver or alternative controls, but does NOT
   change applicability. If no/not provided: state not applicable to this call.]

Items to confirm:
- [Any "Unsure" answers, or [TBC] facts needed to finalise. "None" if clean.]

Recommended next step:
- [If applies: proceed to control mapping; raise a waiver case if built outside
   GEN or CLG-operated. If does not apply: document the basis for the negative
   determination, since it is the exception.]
```

If the user then asks about waiver grounds or which specific controls apply, that is a separate step beyond this skill's scope - say so and stop.
