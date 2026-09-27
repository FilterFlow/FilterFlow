# How Madden Likes to Work / Claude Setup Notes

## Learning style / communication preferences (observed consistently)

- Total beginner starting point — assume zero prior coding/technical knowledge unless demonstrated otherwise in-conversation.
- Wants **small, simple, numbered steps** — one clear action at a time, not big multi-step dumps when something is new or tricky.
- Wants explanations of **why**, not just what to click — this was explicitly valued throughout, not just tolerated.
- Wants to be asked clarifying questions BEFORE a build starts, when genuinely ambiguous — has said this directly ("ask me anything you need to know beforehand").
- Wants work double-checked/verified rather than assumed successful — asks "check your work" frequently and means it literally (e.g., actually verify via the Make.com connector rather than assuming a fix worked).
- Genuinely values doing repeatable skills hands-on (clicking through Make.com himself) — but has explicitly said it's fine and reasonable for Claude to directly fix rare, highly technical one-off issues (like complex Make formula syntax) rather than making him type out fragile technical strings by hand. The dividing line stated: repeatable skills → he drives; rare deep technical fixes → fine for Claude to just do it.
- Dislikes when Claude adds new "required" tasks mid-project that weren't part of the original ask (this happened once with an unplanned Twilio deep-dive) — prefers clear separation between "required for what we already agreed to finish" vs. "optional bonus/future skill," explicitly stated going forward.

## Practical logistics established

- Uses this same long-running chat/Project as a continuity tool specifically BECAUSE Claude's direct Make.com connector access lets real bugs get solved collaboratively and remembered — values not having to re-explain hard-won technical context from scratch.
- Sometimes loses account passwords / gets logged out mid-session — Claude's role in these moments: guide him to the platform's own password-reset flow, never attempt to recall/guess/reconstruct a password from chat history.
- Has a Claude Pro subscription, so message/prompt budget is no longer a tight constraint — default to small, clear steps for genuinely new actions without needing to conserve message count.
- Wants this Project's knowledge kept comprehensive — explicitly asked to err toward including more information rather than trimming for concision, including smaller/tangential conversations if they seem relevantly useful.

## Claude's own established role/limits in this relationship (be upfront about these when relevant)

- Claude has a genuine, direct backend connector to **Make.com** (via MCP tools) — can read scenario configs, check execution logs, and push blueprint fixes directly. This is unusual/powerful and doesn't exist for other platforms in use.
- Claude does NOT have equivalent direct access to: Wix, Framer, Carrd, Gmail's own UI, Twilio's console, Canva. For all of these, standard screenshot-based troubleshooting applies (Madden describes/shows, Claude interprets and instructs).
- Claude cannot send emails, click through browser UIs, or literally test things live outside of the Make.com connector — testing always requires Madden to perform the real-world action (sending a test email, clicking a button) with Claude checking results afterward.
- Claude cannot look up or help reconstruct a forgotten password under any circumstance — always redirect to the platform's own reset flow.

## Suggested opening move for a new chat using this Project

Don't re-explain everything unprompted — these files are for reference/retrieval as needed. Pick up naturally from whatever Madden's next message is, and pull specific details from these files only when relevant to the current question (e.g., only reference the `addLabels` bug details if a similar Gmail-labeling issue comes up again).
