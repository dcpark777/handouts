---
name: slack-reply
description: Clean up a draft Slack reply so it can be copy-pasted as-is. Use whenever the user provides a Slack message they received plus their rough draft reply and wants it fixed, polished, tightened, proofread, or made to sound better — even if they just paste two messages and say "fix this" or "clean up". Also triggers on /slack-reply.
---

# Slack reply cleanup

Turn the user's rough draft into the message they should send. Same person, better day.

## Input

The user provides some or all of:

- **Their message** — what the other person sent (may be a thread, may be absent)
- **My draft** — the user's rough reply (always present; if only one block is pasted, it is the draft)
- **Tone word** — optional, as an argument or in the ask: `tighter`, `warmer`, `direct`. Default is `keep` (preserve voice).

Accept loose formatting. Labels like "them:" / "me:", quoted blocks, or two pastes separated by a blank line are all fine. Don't ask for clarification unless it's impossible to tell which block is the draft.

## Before writing

Read `references/voice.md`. It holds the user's own style rules, phrases to use or avoid, and example replies. Those examples define the target voice; the rules there override anything below where they conflict.

## Rules

1. Fix typos, grammar, clarity, and awkward phrasing.
2. Make sure the reply actually addresses what the other person asked or raised. If the draft skips something they asked, leave the gap — do not invent an answer. Mention the gap in the changes note instead.
3. Slack-native: no formal greetings or sign-offs, no "hope this finds you well", short paragraphs, plain text. Bullets only if the draft already had a list or has three or more parallel items. Slack markdown (`*bold*`, `` `code` ``) only if the draft used it.
4. Never add facts, commitments, dates, ETAs, or promises the draft doesn't contain.
5. Never change technical terms, names, repo/pipeline/table names, or code identifiers. Wrap code identifiers in backticks only if the draft did.
6. Don't make it sound corporate or AI-written. No "certainly", no "great question", no "I'd be happy to".
7. Tone modifiers:
   - `keep` — preserve wording as much as possible; touch only what needs fixing
   - `tighter` — cut noticeably; remove hedging, filler, repetition; keep every real point
   - `warmer` — a bit friendlier and more considerate; no fluff, no exclamation points
   - `direct` — lead with the answer or the ask; drop softeners; stay respectful

## Output

Exactly this, nothing before or after:

````
```
<the cleaned reply>
```
Changed: <1–4 short notes, comma-separated or one per line>
````

The fenced block has no language tag so it copies cleanly. If nothing needed changing, return the draft unchanged and say "Changed: nothing".

If a clipboard command is available (`pbcopy`, `wl-copy`, `xclip -selection clipboard`, `clip.exe`), pipe the reply into it and append one line: `Copied to clipboard.` If none works, say nothing about it.
