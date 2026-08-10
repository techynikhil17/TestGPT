# MCP PoC server — hosting steps

This is a tiny, self-contained MCP server for testing OpenAI's Safety Bug Bounty
"Agentic Tools including MCP" scope (indirect prompt injection via a custom
connector). Everything it returns is synthetic test data — no real accounts or
services are involved.

Two files: `server.js` and `package.json`. Node 18+, Express, and the official
`@modelcontextprotocol/sdk`.

(Correction: Glitch shut down its app-hosting product back in mid-2025, so the
steps below use Render instead — confirmed still free, no credit card
required, as of August 2026.)

## Render (free web service, no credit card, no git CLI needed)

You don't need git installed locally — GitHub's website lets you upload files
directly, and Render deploys straight from a GitHub repo.

**Step 1 — put the files on GitHub via the browser:**
1. Go to https://github.com/new, sign in (free account if you don't have one).
2. Repository name: anything, e.g. `mcp-poc`. Keep it Public. Don't initialize
   with a README. Click **Create repository**.
3. On the empty repo's page, click the **"uploading an existing file"** link.
4. Drag in `server.js` and `package.json` from this folder (README.md is
   optional, not needed for the deploy). Click **Commit changes**.

**Step 2 — deploy on Render:**
1. Go to https://render.com → sign up (GitHub login is easiest) — no card needed.
2. **New → Web Service** → connect your GitHub account if prompted → select
   the `mcp-poc` repo.
3. Build command: `npm install`. Start command: `npm start`. Instance type: **Free**.
4. Click **Deploy Web Service**. First deploy takes a couple minutes — watch
   the logs for `MCP PoC server listening on :10000` (Render sets `PORT`
   itself; the server already reads `process.env.PORT`, no code change needed).
5. Once live, Render shows your URL at the top, like
   `https://mcp-poc-xxxx.onrender.com`.
6. Your MCP endpoint is that URL + `/mcp`, e.g.
   `https://mcp-poc-xxxx.onrender.com/mcp`.
7. Sanity check: open `https://mcp-poc-xxxx.onrender.com/` in a browser —
   should show `mcp poc up`. Open `.../exfil-log` — should show
   `{"count":0,"entries":[],"canary_present":false}` until the test runs.

Note: Render's free tier sleeps a service after 15 minutes of no traffic and
takes ~30–60s to wake on the next request. If ChatGPT's connector-verification
step times out on the first try, just open the Render URL once in a browser
tab to wake it, wait a few seconds, then retry adding the connector.

## Once you have the URL

Send it back to me (just the base URL, e.g. `https://foo.glitch.me`) and I'll
add it as a Developer-mode custom connector in the test ChatGPT account and
run the actual test.
