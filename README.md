# Atlantic Tours: live Google Sheet chatbot

A customer chatbot for a fictitious Irish tour operator. Every answer is read live from the
[Atlantic Tours Google Sheet](https://docs.google.com/spreadsheets/d/1balBGf8QhZ5dc-RCCAPt2kcrcf6m_YRh0HL_r8bBtJw/edit):
edit a price or a slot count in the sheet and the next answer changes. No redeploy.

```
Browser (index.html, GitHub Pages)
   │  POST /chat, GET /tours
   ▼
Cloudflare Worker (worker/worker.js)
   ├─ fetches the sheet as CSV on every request (export?format=csv, cache-busted)
   └─ sends the rows + the conversation to the model
        Claude (claude-sonnet-5-5) if ANTHROPIC_API_KEY is set,
        otherwise Cloudflare Workers AI (Llama 3.3 70B, no key needed)
```

The API key never reaches the browser; GitHub Pages only serves static files.

## Deploy

```bash
cd worker
npx wrangler deploy
npx wrangler secret put ANTHROPIC_API_KEY   # optional: switches the brain to Claude
```

The front end is `index.html`, served by GitHub Pages from `main`.

The sheet must stay shared as "Anyone with the link: Viewer" or the Worker cannot read it.
