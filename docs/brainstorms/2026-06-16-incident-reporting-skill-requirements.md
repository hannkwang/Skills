---
date: 2026-06-16
topic: incident-reporting-skill
---

# Incident Reporting Skill — Requirements

## Summary

A skill that turns a freeform description of a live incident into three style-differentiated artifacts in one pass — a terse WhatsApp group message, a formal email to senior stakeholders, and a spoken-style phone brief for updating PS — drawn from one shared incident record so the facts can never drift between channels.

## Problem Frame

During a live incident, the same set of facts currently has to be re-keyed by hand into three different formats under time pressure: an immediate WhatsApp alert to senior management, a fuller email to a senior stakeholder, and a phone call to update PS within minutes. Each re-keying is a chance for the severity rating, a confirmed-vs-still-checking fact, or a date to drift between artifacts — exactly the kind of inconsistency that erodes trust in a fast-moving incident report. Different people may also pick up reporting duties for the same incident at different points, each starting from whatever facts they were told, which compounds the drift risk.

## Key Decisions

- **One shared record, three channel renders.** The skill maintains a single incident record for the duration of one invocation and projects it into the WhatsApp, email, and phone outputs in the same pass, rather than drafting each artifact independently. This is the structural fix for cross-channel drift.
- **Small required core, freeform overflow.** Only a few facts are required to stay identical across all three renders (when it happened, what happened, severity if known, and the confirmed/unconfirmed status of each fact). Everything else — proposed actions, named individuals, incident-specific detail — lives in a freeform notes section that doesn't force every incident into one fixed shape.
- **Confidence-tagged facts.** Every fact in the record carries a confirmed / reported / unconfirmed tag, and each render surfaces what's still unconfirmed rather than presenting everything with false certainty. This is the operational meaning of reporting "based on what is known at the time."
- **Freeform intake with batched clarification.** The user describes the incident in their own words. The skill extracts what it can, then — only when a required core field is missing or ambiguous — asks for the rest in one consolidated message, matching the batched-intake pattern already used elsewhere in this skill set. It does not walk the user through a rigid checklist, and it does not silently leave required facts blank.
- **No persistence across invocations.** Each invocation is self-contained: it works from whatever the user describes in that session. Carrying a record forward when a different person picks up the same incident later is explicitly deferred.
- **No auto-classification of severity or escalation routing.** The skill captures severity as a stated fact (e.g., "Medium per SN incident guidelines") if the user gives it; it does not compute or recommend a severity level or decide who must be notified.
- **No sensitivity guardrail on the WhatsApp render.** The render includes whatever the record holds — names, asset details — matching how the worked example itself reports into a senior-management group chat. The skill does not flag or redact content for channel sensitivity.

## Requirements

**Record**

- R1. The skill builds one incident record per invocation from the user's freeform description, holding: when the incident happened/was discovered and reported, what happened, severity (if stated), confirmed/reported/unconfirmed status per fact, and a freeform notes section for everything else (actions taken, proposed actions, names, next steps).
- R2. The skill extracts every fact it can from what the user already provided before asking anything.
- R3. The required core fields that trigger clarification when missing or ambiguous are exactly: date/time, what happened, confirmed-vs-unconfirmed status per fact, and severity (if the user is in a position to state one). All other detail — who reported it, who it was reported to, actions taken, proposed actions, named individuals — lives in freeform notes and is never batch-asked about. When a required core field is missing, the skill asks for it in one consolidated message rather than one question at a time.
- R4. Any required core field still unknown after clarification is marked `[TBC]` in the record rather than invented.

**Renders**

- R5. The skill produces all three renders — WhatsApp, email, phone brief — from the same record in one pass.
- R6. The WhatsApp render is short and scannable: a bracketed header (`[Incident] [date time] [title]`) followed by numbered items, matching the style of the worked example.
- R7. The email render is formal and fuller than the WhatsApp render, written for a senior stakeholder who needs complete context, not just the headline.
- R8. The phone-brief render is spoken-style prose — short paragraphs a caller can say aloud — distinct from both the WhatsApp and email register, not a read-aloud copy of either.
- R9. Every render visibly distinguishes unconfirmed facts from confirmed ones (e.g., the WhatsApp and email both carry a "what we don't yet know" line where unconfirmed facts exist; the phone brief states uncertainty in its own spoken phrasing).
- R10. A fact's confirmed/reported/unconfirmed status and severity rating read identically across all three renders — no render may state a different severity or status for the same fact than another.

## Scope Boundaries

**Deferred for later**

- Severity/escalation auto-classification and routing recommendations (which audiences must be told, by when) — the skill captures severity as given, it does not decide it.
- Sensitivity/redaction guardrails on the WhatsApp render.
- Incident lifecycle tracking and delta-only follow-up updates as an incident evolves.
- Recurrence detection (e.g., flagging "second incident of this type in two months") feeding into proposed systemic actions.
- Persisting the record across invocations so a different person can pick up the same incident later.

