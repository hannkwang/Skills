---
name: incident-reporting
description: Generate a WhatsApp group message, an email to a senior stakeholder, and a phone-call brief to update PS for a live security or operational incident, all from one shared set of facts. Use this skill whenever the user describes an incident and wants to report it upward - including requests to "draft an incident update", "write the WhatsApp message for this incident", "brief PS on this", or "email the stakeholder about this incident". Do not use for post-incident reports, SITREP write-ups beyond a single update, or incident root-cause/lessons-learned documents.
---

# Incident Reporting

Turn a freeform description of a live incident into three channel-specific renders - WhatsApp, email, phone brief - drawn from one shared incident record, so the facts can never drift between channels. Each invocation is self-contained: it works from whatever the user describes in this session and does not carry a record forward from an earlier invocation.

## Core principles

1. **One record, three renders.** Build the incident record first; produce all three renders from it in the same pass. Never draft a render independently of the record.
2. **Confidence-tagged facts.** Every fact in the record is tagged `confirmed`, `reported`, or `unconfirmed`. This is the operational meaning of "based on what is known at the time" - state what's known, and flag what isn't.
3. **Small required core, freeform overflow.** Only four fields are required: date/time, what happened, confirmed/unconfirmed status, and severity (if the user can state one). Everything else - actions taken, proposed actions, named individuals, next steps - is freeform notes. Don't force an incident into a fixed shape beyond the core.
4. **Extract first, ask once.** Pull every fact the user already gave before asking anything. If a required core field is still missing or ambiguous, ask for it in ONE consolidated message - never one question at a time, never silently left blank.
5. **No invented facts.** Anything still unknown after clarification is marked `[TBC]` in the record, not guessed.
6. **No auto-classification.** Capture severity as a stated fact (e.g., "Medium per SN incident guidelines") if the user gives it. Do not compute a severity level or decide who must be notified.
7. **No sensitivity guardrail.** Render whatever the record holds - names, asset details included - matching how these reports are actually sent into a senior-management group chat. Do not redact or flag content for channel sensitivity.

## The incident record

Build this once per invocation, before any render:

| Field | Required | Notes |
|---|---|---|
| Date/time (happened / discovered / reported) | Yes | Core field - triggers clarification if missing |
| What happened | Yes | Core field - triggers clarification if missing |
| Confirmed/reported/unconfirmed status, per fact | Yes | Core field - tag every fact below as it's captured, not as an afterthought |
| Severity | Yes, if the user is in a position to state one | Core field - if the user genuinely doesn't know yet, mark `[TBC]`, don't block on it indefinitely |
| Freeform notes | No | Actions taken, proposed actions, named individuals, who reported it, who it was reported to, next steps - everything else |

**Confidence tags.** As you extract or record each fact, tag it:
- `confirmed` - established beyond doubt (e.g., "account disabled at 2115hrs" once done)
- `reported` - stated by a named source but not independently verified (e.g., "officer reports wallet was misplaced in public")
- `unconfirmed` - still being checked (e.g., "no suspicious login activity detected" pending full log review)

## Gathering the answers

1. Extract every fact the user's freeform description already gives you - date/time, what happened, severity, and the confidence each fact deserves. Do not re-ask anything already answered.
2. Check the four required core fields (date/time, what happened, confirmed/unconfirmed status, severity). If any are missing or ambiguous, ask for all of them in ONE consolidated message using the platform's blocking question tool (tappable options where natural, free text otherwise). Do not ask about freeform-note detail (who reported it, who it was reported to, actions taken, proposed actions, named individuals) - that stays freeform and is never batch-asked.
3. Anything still unknown after this one round is marked `[TBC]` in the record - do not invent it, and do not ask a second round.

## Renders

Produce all three from the same record, in one pass. A fact's confirmed/reported/unconfirmed status and severity rating must read identically across all three - never state a different severity or status for the same fact in different renders.

### WhatsApp render

Short and scannable, for an immediate update to a senior-management group.

- Bracketed header: `[Incident] [date time] [title]`
- Numbered items grouped under plain labels as they apply: **Incident Details**, **Actions Taken**, **Proposed Actions**, **Next steps**
- A line surfacing what's still unconfirmed, where any fact is `reported` or `unconfirmed` (e.g., "Still confirming: ...")
- State severity once, plainly (e.g., "Incident severity: Medium (per SN incident guidelines)")

### Email render

Formal and fuller than the WhatsApp render, for a senior stakeholder who needs complete context, not just the headline.

- Subject line naming the incident and date
- Opening line stating what happened and when, in full sentences (not bracketed shorthand)
- Body organized into short paragraphs or labelled sections covering what's known, actions taken, and proposed actions
- A clearly labelled line or short section for what remains unconfirmed
- Closing line naming the proposed next step and who owns it, when known

### Phone-brief render

Spoken-style prose - short paragraphs a caller can say aloud while updating PS within minutes. Distinct from both other renders, not a read-aloud copy of either.

- Opens with the headline in one spoken sentence: what happened, when, and current severity
- Follows with what's been done so far, in plain spoken phrasing
- States uncertainty in its own words (e.g., "We're still checking whether...", "We haven't yet confirmed...") rather than a bracketed list
- Closes with what PS should expect next (e.g., a SITREP update, a follow-up call)

## Output

Present the incident record (fields and confidence tags) first, then the three renders in order: WhatsApp, email, phone brief. If any required core field is `[TBC]`, surface that plainly above the renders, not buried inside them.

## Scope boundaries

Out of scope for this skill - say so if asked rather than improvising:

- Auto-classifying severity or recommending escalation/notification routing
- Sensitivity or redaction guardrails on any render
- Tracking an incident's lifecycle or producing delta-only follow-up updates across invocations
- Detecting recurrence (e.g., "second incident of this type in two months") to feed systemic recommendations
- Persisting the record across invocations so a different person can pick up the same incident later
