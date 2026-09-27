# Joe's Plumbing — Practice Portfolio Automations

Fake demo business built in Make.com to learn the fundamentals and create a portfolio piece. Site was built in Carrd (joesplumbingdemo@gmail.com account), now being partly superseded by the FilterFlow/Wix portfolio.

## Core skill learned (stated repeatedly as the throughline)

Every automation = a **trigger** (what starts it) + one or more **actions** (what happens next). Once this pattern clicks, every new automation is just applying it with different apps.

## 1. Contact form → spreadsheet

- Tools: Carrd (site) + Tally (form) + Make.com + Google Sheets
- Flow: Tally "Watch New Responses" (webhook-based, instant) → Google Sheets "Add a Row"
- Purpose: every lead captured automatically, no manual entry.

## 2. Missed-text auto-reply

- Tools: Twilio (SMS) + Make.com
- Flow: Twilio "Watch Messages" → filter to exclude messages FROM the business's own Twilio number (avoids replying to its own outgoing texts) → Twilio "Send a Message"
- Key bug solved: initial version replied to its own auto-replies in a loop; fixed with a filter checking the sender number does not equal the Twilio number.
- Scheduling: polls periodically (not instant by default in this version).

## 3. Review-request automation

- Tools: Google Sheets ("Completed Jobs" sheet) + Twilio + Make.com
- Flow: Sheets "Watch New Rows" → Twilio "Send a Message" asking for a review
- Key bugs solved: phone numbers in the sheet were auto-converted to broken numeric formulas by Google Sheets (fixed by forcing text format with a leading apostrophe); field reference syntax errors when mapping Sheets columns with spaces in their names.

## 4. AI-generated customer reply (with real business knowledge)

- Tools: Tally + Google Docs + OpenAI + Make.com
- Flow: Tally "Watch New Responses" → Google Docs "Get Content of a Document" (reads a real business-info doc: hours, pricing, service area) → OpenAI "Simple text prompt" (writes a personalized reply using both the customer's message AND the business info) → (log/send)
- This is the one that proves genuine AI understanding, not just app-connecting — AI reads real business details and gets specifics right (hours, service radius) in its replies.

## 5. Router / "emergency" detection — KNOWN LIMITATION, don't oversell to clients

- Added a Router splitting into "Emergency" vs "Non-emergency" paths, each triggering a differently-toned AI reply.
- **This is keyword-based only** — checks if the message literally contains the word "emergency," not true AI judgment of urgency. A message like "my pipe broke, come quick" would NOT trigger the emergency path since it lacks the literal keyword.
- Real fix (AI-based urgency judgment instead of keyword matching) was discussed as a good future upgrade but never built. Do not present this specific feature as fully reliable to a real client without upgrading it first.

## 6. Solo practice automation (Sheets → Gmail) — not portfolio-worthy

- Built specifically as a hands-on exercise in mapping data between modules (was the biggest early struggle). Uses fake test data (Name/Email columns). Not a real business use case, don't include in client-facing portfolio.

## Twilio Studio — missed-call auto-text (harder, PARKED)

Separate, more advanced attempt at TRUE missed-call detection (not just text-reply):

- Built a Twilio Studio Flow: Trigger (Incoming Call) → "Connect Call To" (dials a real number) → on "Caller Hung Up" outcome → "Send Message"
- Real limitation discovered: Twilio's simplified Studio widget only exposes "Connected Call Ended" and "Caller Hung Up" as outcomes — no clean "Timeout/No Answer" event like a true missed-call system would need. "Caller Hung Up" was used as an imperfect proxy.
- Hit real friction with Twilio trial-account behavior (mandatory "this is a trial account" disclaimer message, call-testing quirks) that made testing confusing and slow.
- Status: parked, not required for any current client work. Would need proper revisiting (possibly a real paid Twilio account, and/or a "Split Based On" step checking actual call status codes) to make it production-reliable.

## General Make.com lessons learned across all of these

- "Run once" behaves differently depending on trigger type:
  - Webhook/instant triggers (Tally, Twilio SMS) actively LISTEN for a short window after clicking Run once — send the test AFTER clicking.
  - Polling triggers (Gmail, Google Sheets Watch Rows) check ONCE INSTANTLY — send the test BEFORE clicking/checking, not after.
- Deactivating then reactivating a scenario resets its "from now on" checkpoint — useful to skip a stuck backlog of old items.
- Free Make.com plan: **1,000 operations/month, max 2 scenarios active at once.** Real constraint — had to deactivate other automations to test new ones during building. For multiple simultaneous real clients, will eventually need a paid plan or to combine automations into fewer scenarios.
- Browser tab / cached-session issues occasionally caused manual UI edits to not actually save, or to appear reverted — worth a hard refresh/reopen before assuming a new bug when something seems to have "undone" itself.
