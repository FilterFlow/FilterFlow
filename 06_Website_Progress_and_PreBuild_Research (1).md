# Website Progress + What To Research Before Building in Claude Code

## Where things stand

- Platform decision: moving off Carrd/Wix-in-progress to **Claude Code**, specifically because it gives real file-level control (unlike Wix/Framer/Canva Sites, where Claude can only work from screenshots, not a real connector) while still being included in the existing Claude Pro subscription.
- A **temporary capability-demo site** was already built and published (`filterflow-demo.html`, live at `https://claude.ai/artifact/5xFbeACcsCp31WumVbqbzM`) to prove out what Claude Code / Artifacts can do. It is NOT the real production site — it was explicitly built fast and "doesn't have to look good," just to demonstrate capability.
  - Built with a custom SVG recreation of the logo concept (swirl-into-wave), Quicksand + Source Sans 3 fonts, a navy/orange color scheme, and sections for hero / services / proof-of-work / contact.
  - **Real technical limitation discovered**: a published Claude Artifact page can only load images from a small allowlist of external hosts, and Canva's export/asset hosting is NOT on that list. This is why the real Canva logo file couldn't be pulled into the demo directly — a WebFetch of the signed Canva export URL failed as unauthorized, and a Bash curl also failed because Canva's export-download domain isn't reachable from the sandbox. Workaround used: rebuild the logo as SVG code directly, matching the concept rather than importing the actual exported file. **For the real site, this means: either recreate the logo in code (as was done here), or host the actual logo image file somewhere on the allowlist / bring it in as an uploaded asset, rather than expecting a live pull from Canva.**
- Branding decisions to carry into the real build (see also file 02):
  - Avoid the "glossy/gradient AI-generated" look on the logo itself — flatter and simpler reads as more genuinely professional for a small local-facing service brand.
  - Target audience: older/non-tech-savvy local business owners — tone should be warm and plain-spoken, not jargon-heavy, even though the underlying service is technical.
- Domain: the live demo currently shows as hosted under Claude's own domain. For the real production site, a custom domain will need to be connected — this is a separate step from building the HTML in Claude Code (Claude Code produces real files; where/how they're deployed with a real domain, e.g. via Netlify or similar static hosting, is a next decision once the actual build starts).

---

### 

---

## Sources used for this research

- [Prompting for frontend aesthetics — Claude Cookbook](https://platform.claude.com/cookbook/coding-prompting-for-frontend-aesthetics)
- [Why Your AI Keeps Building the Same Purple Gradient Website](https://prg.sh/ramblings/Why-Your-AI-Keeps-Building-the-Same-Purple-Gradient-Website)
- [How to Make AI UI Look Less Generic: 5 Fixes (2026) — Superdesign](https://superdesign.dev/blog/how-to-make-ai-ui-look-less-generic)
