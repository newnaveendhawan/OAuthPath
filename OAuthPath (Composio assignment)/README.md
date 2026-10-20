# OAuthPath

Look up an app and get an OAuth setup guide: how hard it is, how long it takes, what blocks you, and what an agent can do versus what you must do. Built for the Composio APM Integrations take-home.

## Run it
Static site, no build step, no backend.

    python3 -m http.server 8000     # then open http://localhost:8000
Or drag this folder onto Netlify Drop / push to GitHub Pages.

## What is in it
- Library of 5 guides on purpose-picked different setups: Notion (easy, self-serve), Slack (self-serve but rate-limited unless Marketplace approved), HubSpot (install caps, need real customers to list), LinkedIn (gated product, company-only), Gmail (restricted scopes, yearly security assessment).
- Directory check: `data/directory.json` is a snapshot of Composio's managed OAuth app directory (123 managed, 211 bring-your-own, read 2026-10-20 from https://docs.composio.dev/toolkits/managed-auth). Each guide shows whether Composio already provides the OAuth app.
- Live generation: search any app not in the library. The browser calls the Gemini API with the user's own key (localStorage only). Step 1: the model lists the provider's official doc URLs. Step 2: Gemini fetches those pages (url_context) and writes the guide. Step 3: the app checks which pages were really fetched, keeps only those as sources, and downgrades any 'verified' claim that no fetched page backs. Generated guides are labelled "not reviewed" and can be shared as a link (the guide is encoded in the URL hash, no server needed).

## Key decisions
1. **Every claim carries an evidence label**: verified (an official page says it, linked), inferred, or unverified. Developers get the answer and how far to trust it. Unknowns such as review times and fees are stated as not found, not guessed.
2. **Difficulty is about waiting and gatekeepers**, not code. Almost every OAuth flow is the same code; the real variance is review, caps, and who is eligible. So the guide leads with time, approval wait, and blockers.
3. **"Agent can" vs "You take over"** is explicit in every guide. Agents can draft config, policy text and code. Humans must own accounts, domains, legal identity, recordings, and pay for assessments.
4. **No backend.** A key-in-browser flow keeps the project deployable anywhere and keeps secrets out of the repo.
5. **Focused scope**: 5 guides, not 100. Depth on varied hard cases over breadth.

## Limitations (honest list)
- I did not create real provider apps or run OAuth flows. All guides are from docs read on 2026-10-20. Each guide says "Tested: docs read only".
- Live generation was tested against the real Gemini API with a free-tier key (Zoom, Asana). Findings: gemini-2.5-flash is closed to new users; Google Search grounding was refused for a free key; url_context works. In tests the model proposed some doc URLs that could not be fetched (e.g. all 4 for Zoom) and still tagged claims verified. That is why step 3 exists. Retrieval is only as good as the URLs the model guesses; unfamiliar apps often get mostly 'unverified' claims. Model name is editable in settings.
- The agent does not attempt setup on provider consoles. That needs the developer's login.
- Review durations are only given where a provider states them (Google). Others say not found.
- Provider docs change. Each guide lists its sources; re-check before relying.

## Verification log (sample check of guidance against sources)
| Item | Result |
|---|---|
| Google verification times | Source gives 2-3 business days (brand), 10 business days (sensitive), about 6 weeks (restricted). Source says estimates are not guaranteed. Guide says so. |
| Google 100-user cap | Source: lifetime of the project, cannot be reset. Added as a blocker with advice to keep production and test projects separate. |
| Slack rate limit | Corrected my first reading: it hits only commercially distributed, non-Marketplace apps created after 2025-05-29 (and new installs of older ones). Internal apps and older installs are not affected. |
| Slack Marketplace review time | Could not find a stated duration. Marked unverified. |
| HubSpot active installs | Source: 3 to submit a listing, 60 for certification, counted as active (not total) installs from 2026-08-19. Guide uses active installs. |
| HubSpot review time | Not found. Marked unverified. |
| LinkedIn tiers | Source confirms Development and Standard tiers, legal organizations only, screencast for Standard. Sign-in and Share product access not checked: marked unverified. |
| Notion approval | No approval step found in two official pages. Marked inferred, not verified. |
| Security assessment price | Not stated by Google. Marked unverified. |
