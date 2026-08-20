---
name: wave
description: Search and synthesize the user's Wave meetings, calls, and recordings — meeting prep, weekly reviews, verbatim quotes, and action items. Use when the user asks what was said or decided in a meeting or call, what they committed to, what happened with a person or topic, or wants a recap, digest, or follow-up drafted from their recordings. Not for scheduling, and not for documents or notes outside Wave.
---

# Wave

Wave records the user's meetings, phone calls, and other conversations, then
stores a transcript, a summary, and structured action items for each one. These
tools read and organize that library. They only ever see the authenticated
user's own data.

## Pick the right entry point

Almost every request starts with one of two tools, and choosing wrong is the
most common way to give a bad answer:

- **`search_sessions`** — semantic search across summaries and transcripts. Use
  it whenever the question is about *content*: "what did we decide about
  pricing", "when did I last talk to Dana about the contract", "what have I said
  about hiring". It matches meaning, so the user's words need not appear.
- **`list_sessions`** — reverse-chronological browsing. Use it when the question
  is about *recency or range*: "what were my meetings this week", "recap
  yesterday", "how many calls did I have in October".

If the user names a person, project, or topic, prefer `search_sessions` even
when they also mention a timeframe — then filter by date from the results.

## Work in two passes

Sessions can be long. Fetching full transcripts for everything is slow and
usually unnecessary.

1. **Widen**: get 10–20 candidates from `search_sessions` or `list_sessions`.
   Their titles and summaries alone answer many questions.
2. **Narrow**: call `get_session` only for the sessions that actually matter,
   and `get_transcript` only when you need exact words.

Request 100 sessions only when the user explicitly asks for something
exhaustive. Paginate rather than inflating a single request.

## Verbatim vs. summary

Use `get_transcript` when the user wants a direct quote, exact phrasing, or
speaker attribution ("who said that?", "quote her exactly"). It supports
`format: plain | segments | speaker_labeled` — use `speaker_labeled` whenever
attribution matters.

For everything else the session summary from `get_session` is enough, and quotes
you construct from a summary are paraphrases. Never present a paraphrase as a
quotation; if the user asks what someone said word for word, fetch the
transcript.

## Meeting prep

When the user is about to meet someone and wants to be caught up:

1. `search_sessions` for the person or the project name.
2. Sort the hits by recency and read the summaries of the most recent few.
3. `get_action_items` on those sessions to surface open commitments.
4. Report: what was last discussed, what was decided, what either side owes, and
   what is still unresolved.

Lead with the open loops — those are why the user asked.

## Weekly review or digest

1. `list_sessions` over the period.
2. Group by theme rather than replaying the calendar in order; a chronological
   list of titles is something the user can already see in the app.
3. Pull action items for the sessions that produced commitments.
4. Deliver decisions made, commitments outstanding, and anything raised but left
   unresolved.

## Action items

`get_action_items` returns structured items plus a version. To change them, send
the **complete** replacement list to `update_action_items` — it is a full
replacement, not a patch, so include the items you are keeping.

Always pass the `expected_version` you just read. If the update is rejected as
stale, the user edited the list in the Wave app in the meantime: re-read, merge,
and try again rather than forcing an overwrite.

## Folders and tags

Folders are user-created groupings and are **non-exclusive** — a session can
belong to several at once. `list_folders` discovers them; pass `folder` (id or
name) to `list_sessions` or `search_sessions` to scope a search, which is how
users separate work from personal on one account.

`create_folder` and `add_session_to_folder` are idempotent, so re-running them is
safe. `remove_session_from_folder` removes only the folder relationship — it
never deletes the session.

Tags are free-form labels the user typed. Both list and search accept `tag` with
`tag_mode: any | all`, matched exactly and case-sensitively.

## Phone calls

Sessions with `type: "phone"` carry a `phone` object: direction, the other
party's number, status, and whether the call is still ongoing. Inbound calls
arrive over a bridge, so the caller is unknown and `from_number` is null — do not
describe an inbound call as coming from the user's own number, which is what
`to_number` holds.

## Boundaries

- Read tools and write tools alike are scoped to the authenticated user.
- Nothing here can delete a session or alter a recording or transcript. The four
  write tools only edit action items and folder membership.
- Before overwriting action items the user has not seen, show them what will
  change.
- `search` and `fetch` exist for deep research and return thinner data. In
  conversation prefer `search_sessions`, `get_session`, and `get_transcript`.
