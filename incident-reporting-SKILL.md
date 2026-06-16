---
name: incident-reporting
description: Generate a WhatsApp group message, an email to a senior stakeholder, and a situation-brief to verbally update PS or another very senior officer on a live situation, all from one shared set of facts. Use this skill whenever the user describes an incident and wants to report it upward - including requests to "draft an incident update", "write the WhatsApp message for this incident", "brief PS on this", or "email the stakeholder about this incident". Do not use for post-incident reports, SITREP write-ups beyond a single update, or incident root-cause/lessons-learned documents.
originator: Hannkwang
---

# Incident Reporting

Turn a freeform description of a live incident into three channel-specific renders - WhatsApp, email, situation-brief - drawn from one shared incident record, so the facts can never drift between channels. Each invocation is self-contained: it works from whatever the user describes in this session and does not carry a record forward from an earlier invocation.

## Core principles

1. **One record, three renders.** Build the incident record first; produce all three renders from it in the same pass. Never draft a render independently of the record.
2. **Confidence-tagged facts.** Every fact in the record is tagged `Incident` or `Alert`. This is the operational meaning of "based on what is known at the time" - state what's known, and flag what isn't.
3. **Small required core, freeform overflow.** Only four fields are required: date/time, what happened, incident/alert status, and severity (if the user can state one). Everything else - actions taken, proposed actions, named individuals, next steps - is freeform notes. Don't force an incident into a fixed shape beyond the core.
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
| Incident/Alert status, per fact | Yes | Core field - tag every fact below as it's captured, not as an afterthought |
| Severity | Yes, if the user is in a position to state one | Core field - if the user genuinely doesn't know yet, mark `[TBC]`, don't block on it indefinitely |
| Freeform notes | No | Actions taken, proposed actions, named individuals, who reported it, who it was reported to, next steps - everything else |

**Confidence tags.** As you extract or record each fact, tag it:
- `Incident` - established beyond doubt (e.g., "account disabled at 2115hrs" once done)
- `Alert` - stated by a named source but not independently verified (e.g., "officer reports wallet was misplaced in public")

**Message classification (header).** Derive the bracketed header tag from the "what happened" fact's tag: `[Incident]` when that fact is tagged `Incident` (established), `[Alert]` when it's still tagged `Alert` (reported but not yet established). This is the only header derived from a tag - all other facts keep their own per-fact tag in the body.

## Gathering the answers

1. Extract every fact the user's freeform description already gives you - date/time, what happened, severity, and the confidence each fact deserves. Do not re-ask anything already answered.
2. Check the four required core fields (date/time, what happened, Incident/Alert status, severity). If any are missing or ambiguous, ask for all of them in ONE consolidated message using the platform's blocking question tool (tappable options where natural, free text otherwise). Do not ask about freeform-note detail (who reported it, who it was reported to, actions taken, proposed actions, named individuals) - that stays freeform and is never batch-asked.
3. Anything still unknown after this one round is marked `[TBC]` in the record - do not invent it, and do not ask a second round.

## Renders

Produce all three from the same record, in one pass. A fact's Incident/Alert status and severity rating must read identically across all three - never state a different severity or status for the same fact in different renders. **Severity is the only rating shown in any render.** If the record's freeform notes include a separate risk-level verdict (e.g., "risk assessed as low"), drop the verdict from every render - keep the underlying reasoning (e.g., why it's considered contained) if it's otherwise relevant, but never restate it as a rating distinct from severity.

### WhatsApp render

Short and scannable, for an immediate update to a senior-management group.

- Bracketed header: `[Incident]` or `[Alert]` (per the Message classification rule above) followed by `[date time] [title]`
- Numbered items grouped under plain labels as they apply: **Incident Details**, **Actions Taken**, **Proposed Actions**, **Next Steps**
- A line surfacing any fact still tagged `Alert` (e.g., "Still verifying: ...")
- State severity once, plainly (e.g., "Severity: Medium" - scale: Low/Medium/High/Severe/Very Severe)

### Email render

Formal and fuller than the WhatsApp render, for a senior stakeholder who needs complete context, not just the headline - this is the render where freeform notes earn their place; draw on every relevant detail the record holds, not just one line per heading. Use this fixed structure, in this order:

```
Subject: [Incident/Alert] [date time] [title]

Dear PS
(cc: DSes, Dirs)

Aim
Background
Findings and Assessment
Actions Taken
Impact Assessment
Next Steps

Regards,
[Sender]
```

- **Subject line** - mirrors the WhatsApp header (`[Incident]`/`[Alert]`, date/time, title) so the email is identifiable at a glance in an inbox.
- **Dear PS / cc line** - addressed to PS, cc'ing DSes and Dirs by default; adjust names if the user specifies different recipients.
- **Aim** - one line stating why this email is being sent (e.g., "To update PS on...").
- **Background** - the fuller narrative: what happened, when and where, who is involved (named individuals from freeform notes), how it was discovered or reported, and - when the record holds it - the asset, system, or data classification affected. Do not compress this into one line if the record has more to say.
- **Findings and Assessment** - everything currently known: severity, every fact still tagged `Alert` stated plainly, the scope of what's affected, and any pattern or trend noted in freeform notes (e.g., a prior similar incident) - this is where recurrence context belongs, not buried in Next Steps.
- **Actions Taken** - every action taken to date, one per point, with the actor and timing when the record states them (e.g., "Disabled by SOC at 2115hrs") rather than collapsing several actions into one sentence.
- **Impact Assessment** - the practical consequence of the incident as stated by the source - what is or isn't affected, and why (e.g., "no suspicious activity has been detected, and the account was disabled immediately") - capture the full reasoning given, do not compute, upgrade, or shorten it to a single word (per Core Principle 6). Severity is the only rating carried into this section - omit any separate risk-level verdict (e.g., "risk assessed as low") even when the source states one.
- **Next Steps** - every proposed or planned next step, each with its owner and timing when known (e.g., reporting to SITREP, a proposed coordination action, a follow-up update to other stakeholders) - do not collapse multiple distinct next steps into one line.
- **Sign-off** - close with "Regards," and a sender placeholder (or the named sender, if the record gives one).

Always use all sections in this order; only omit one if genuinely nothing applies (rare - Subject, Aim, Background, and Next Steps should virtually always have content). When the record's freeform notes contain more detail than the WhatsApp render surfaced, that detail belongs here, not dropped.

**Number every paragraph, sequentially across the whole email.** Each paragraph (one per heading at minimum; a heading with several distinct points gets one numbered paragraph per point) carries the next number in one continuous sequence from "1." at the Aim through the end of Next Steps - the numbering does not restart per heading. For example: "1. To update PS on..." (Aim) / "2. On 10 Jan..." (Background) / "3. This incident is classified..." (Findings and Assessment), continuing through Actions Taken, Impact Assessment, and Next Steps.

**Plain-language register.** Write for a smart, non-technical executive - assume intelligence, not domain knowledge. Spell out or briefly gloss acronyms and technical terms on first use (e.g., "PKI card" -> "PKI (security access) card" if the record doesn't already define it; "GIROC" -> "GIROC (the government incident reporting centre)" only if the expansion is actually known - never invent an expansion you're not sure of, just don't assume the reader knows the term). Prefer plain, consequence-focused phrasing over technical mechanism (e.g., "her access was cut off immediately" rather than "the account was deactivated at the directory level"). The WhatsApp and situation-brief renders may keep operational shorthand for an in-the-know audience; the email may not assume that audience.

### Anticipated executive questions

After drafting the email, add a short block beneath it: the top 3 questions a senior executive (PS or CE level) is most likely to actually ask after reading it - not a generic checklist, but the questions that follow from what stands out in *this* record (a recurring incident invites "why is this happening again," an `Alert`-tagged fact invites "are we sure that's everything," a vague next step invites "who exactly is accountable and by when"). For each question:

- State it the way an executive would actually ask it - plain, direct, not a category label.
- Give a one-line answer pointer to where the email already answers it, or - if the record genuinely doesn't answer it - say so plainly as a gap rather than inventing a reassuring answer.

This block is a self-check as much as a prep aid: if all 3 questions point to "already answered in [section]," the email is in good shape; any pointing to a gap is worth fixing before sending, not just flagging.

### Situation-brief

A direct, one-directional verbal briefing to PS (or another very senior officer) - declarative and economical, the way a briefing officer would actually speak, not a phone chat. Deliverable aloud in well under 30 seconds (roughly 75-90 words).

- **No preamble.** Open with direct address to PS by title (e.g., "PS, a situation update on...") immediately followed by the headline in one sentence: what happened, when, and current severity. Skip "hi", "quick update", "Sir/Ma'am", or any other throat-clearing.
- **Facts only, declarative.** State what's been done so far in short, complete sentences. No filler words ("just", "basically", "I think"), no hedging on facts already tagged `Incident`.
- **Surface open risk in one sentence.** If any fact is still tagged `Alert`, state it plainly once (e.g., "We have not yet confirmed whether..."). Do not list every unresolved detail - a senior officer needs the headline risk, not the working notes.
- **Close with the next step stated, not asked.** End with exactly what the officer should expect next and when (e.g., "This will be reported at tonight's SITREP" / "A full report follows by 1800hrs"). Never end on a question back to the officer or a vague "let me know if you need anything."
- **Register check.** Read it aloud before finalizing: if it sounds like a casual phone call rather than a briefing handed to a senior officer, tighten it - cut transitions, soften nothing, state outcomes plainly.

## Output

Present the incident record (fields and confidence tags) first, then the three renders in order: WhatsApp, email, situation-brief, followed by the email's anticipated executive questions block. Separate the record and each block with a plain divider line (`--------`) so they don't visually run together. If any required core field is `[TBC]`, surface that plainly above the renders, not buried inside them.

## Scope boundaries

Out of scope for this skill - say so if asked rather than improvising:

- Auto-classifying severity or recommending escalation/notification routing
- Sensitivity or redaction guardrails on any render
- Tracking an incident's lifecycle or producing delta-only follow-up updates across invocations
- Detecting recurrence (e.g., "second incident of this type in two months") to feed systemic recommendations
- Persisting the record across invocations so a different person can pick up the same incident later
