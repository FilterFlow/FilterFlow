# Mom's Automation #1 — AI Gmail Sorter + Action Item Logger

Status: **DONE AND WORKING** (as of last confirmed test). Real client, real account, live in production.

Client context: Mom is a senior consultant / board co-chair for a nonprofit (AFP Manitoba). Self-employed enough that no employer IT approval was needed — she personally authorized the connection. Real Gmail account: leslie@afpmanitoba.org.

## What it does

Every email that arrives in her inbox gets:

1. Automatically labeled with the correct one of her real, existing Gmail labels (which are specific, e.g. "Board meetings/September Board meeting," "PD sessions/May - Connect and Collaborate" — NOT generic categories)
2. Logged into a Google Sheet safety net (Date, From, Subject, Label Assigned, Action Item text) — so nothing is ever silently lost even if AI mis-judges something
3. Flagged if it contains an action item, with a one-sentence summary of what the action item is

## Why this design (safety net reasoning)

Madden specifically raised the concern: what if the AI misses something and it just doesn't get flagged, with no way to know it was missed? Solution: EVERY email gets logged to the sheet regardless of the AI's labeling decision — the label/action call can be imperfect, but the original email is never hidden or lost, just possibly filed slightly wrong. Mom can periodically scan the sheet.

## Full build (final working structure)

1. **Trigger**: Gmail "Watch Emails" — polling type, NOT instant/webhook. Checks the inbox on a schedule (see scheduling below), does not wait live like Tally/Twilio's webhook triggers do.
2. **Step 2**: Gmail "Make an API call" — `GET /v1/users/me/labels` — pulls her COMPLETE real, current label list fresh on every single run (returns JSON: array of `{id, name, ...}` per label). This is what makes the labeling dynamic/accurate rather than hardcoded guesses.
3. **Step 3**: OpenAI "Simple text prompt" — given the real label list AND the email's full body, told to respond in ONE LINE using pipe-separated format: `LABELTEXT|ACTIONYESNO|ACTIONSENTENCE` (Chose this pipe-delimited single-line format ONLY after JSON parsing and multi-line "LABEL:\\nACTION ITEM:\\n..." formats both proved unreliable to parse — see bug section below.)
4. **Step 4**: Google Sheets "Add a Row" — logs every email, every time, regardless of what happens downstream. Extracts label/action text from the AI's pipe-separated output using `{{get(split(3.result; "|"); 1)}}` etc.
5. **Step 5**: Router — one branch per her ACTUAL real label (currently hardcoded per-label, not fully dynamic — see below), plus:
   - A general "Board meetings" branch and general "PD sessions" branch (for emails that fit the parent category but no specific sub-label) — the PD sessions general branch needed an explicit exclusion filter (`contains "PD sessions"` AND `does not contain "PD sessions/"`) to stop it from double-firing on every PD-specific email too.
   - A catch-all "Needs Review" branch for anything AI can't confidently match (AI outputs `NEW: Needs Review` in that case) — currently mapped to the general "Board meetings" label as a placeholder; could be refined to a dedicated "Needs Review" label later.
6. Each branch: Gmail "Update Email Labels" module, with `messageId` = `{{1.id}}` and the label added via the field discovered to be the REAL correct one: **`addLabels`** (see huge bug section below).

## Scheduling

Final setting: **once every 24 hours** ("Daily"). Mom explicitly said 1 day to 1 week is fine for her needs. Chosen specifically because:

- Free Make.com plan = 1,000 operations/month
- Checking every 15 min (the default) = \~2,880 checks/month — blows the entire budget on checks alone within \~10 days, even with zero real emails
- Daily checking = \~30 checks/month, leaving huge headroom
- "Limit" (max emails processed per run) was raised from default 5 to 20, since daily checks might catch more emails at once.

## THE MAJOR BUG — hours of debugging, root cause and lesson

**Symptom**: every single test, across many different data formats (plain string, JSON, array literal, comma-string, with/without "Map mode" flags), failed identically with: `[400] No label or Classification Label updates provided`

**False leads chased first** (in order, all ruled out):

1. Text-matching typos / capitalization (fixed with `trim()`, not the real fix)
2. `map()`/`get()` function syntax errors (verified correct against Make's own docs — not the issue)
3. Scalar string vs. array data type for the "multiple select" field (tried both — neither fixed it)
4. Missing "Map mode" flag on the field (added `mode: map` explicitly — didn't fix it)
5. OAuth permission scope (reconnected Gmail, explicitly verified "Create, change, or delete your email labels" WAS already granted — ruled out)
6. Stuck backlog reprocessing the same old email repeatedly (real secondary issue, fixed separately via deactivate/reactivate, but not the root cause of the label error itself)

**THE ACTUAL ROOT CAUSE**: the module's outdated-looking `expect`/schema (which Claude could read via the Make API) listed the field as `labelIds`. **This was wrong / stale.** The real, correct field name the "Update Email Labels" module actually uses is **`addLabels`** (and presumably `removeLabels` for the removal equivalent). Every attempt writing to `labelIds` was silently writing to a field that didn't functionally exist, no matter how the VALUE was formatted — hence identical errors regardless of format.

**How it was actually found**: Madden manually clicked the real checkbox picker in Make's UI (Map toggle OFF) for one branch, and it worked/behaved differently than what Claude's API-based edits were producing — this mismatch was the clue that the UI and the assumed schema were using different underlying field names. Confirmed by reading the scenario's live blueprint afterward and seeing `addLabels` was what the UI had actually saved.

**Lesson for future debugging**: if a Make.com module's behavior doesn't match its documented/`expect`-listed field name after reasonable troubleshooting, don't keep varying the VALUE format — check whether the field NAME itself is stale/wrong by comparing against what a manual UI save actually produces.

## Other real lessons from this build

- Router filter conditions can't use functions like `parseJSON()` directly — Make's filter UI only accepts simple field references, not function calls. (Caused a separate, resolved error: "Filter references non-existing module \[0\]".) Keep filters checking plain fields; do any parsing/transformation in the mapper fields of the modules themselves, not in filters.
- `parseJSON(x).property` chaining syntax is NOT valid in Make's formula language — use `get(parseJSON(x); "property")` instead if JSON parsing is ever revisited.
- Manual UI edits and Claude's direct API-pushed edits occasionally went out of sync — each side sometimes showed/saved a different state than the other. When this happens: close and fully reopen the browser tab (not just refresh) before assuming a new bug.
- Claude does NOT have the ability to literally send a test email or click through Gmail's UI — Madden always had to be the one to send the actual test email; Claude's role was checking the result afterward via the Make.com connector.
- Real emails were inadvertently pulled into this chat during debugging (before this was recognized as a privacy concern) — going forward, prefer checking non-sensitive fields (like our own generated label text in a spreadsheet column) rather than pasting raw email content, when diagnosing.

## Automation #2 for Mom — PARKED, waiting on Copilot

**Goal**: Teams meeting → AI-generated summary/action items → automatically create tasks in Microsoft To Do.

- Native Teams "Facilitator" (Microsoft Copilot feature) can already generate a meeting-summary Word doc with action items built-in — IF her organization has a Copilot license on her actual work Microsoft 365 account. As of last update, her Copilot was on the WRONG account (her personal Microsoft login, not her work one) — Teams-specific Copilot features do not transfer via personal-account workarounds, this needed her actual work-tied license, which her employer was expected to provide "in about a week."
- Planned real build (once Copilot is on the right account): OneDrive "Watch Files/Folders" (a dedicated folder she saves the Facilitator-generated doc into) → AI extracts action items → Microsoft To Do creates a task per item.
- A OneDrive connection was tested/authorized during a trial run (Microsoft account sign-in, permissions confirmed fine) but the actual file-watching + AI-extraction + To Do-creation logic was NOT built yet as of last update — this got shelved when the Gmail-sorter debugging took priority, and again when Copilot licensing wasn't ready.
