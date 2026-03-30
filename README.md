# 🤖 MinMik IT Assistant
**AI-powered IT troubleshooting chatbot for L1 to L3 support engineers**

Live at: [minmik.com](https://minmik.com)

---

## Overview

MinMik IT Assistant is a fully client-side AI chatbot built for IT professionals. It provides structured, step-by-step troubleshooting guidance across all IT support tiers — from basic password resets to advanced infrastructure debugging — powered by Anthropic's Claude AI.

### Features
- ✅ L1 → L3 troubleshooting coverage
- ✅ Step-by-step diagnostic workflows with exact commands
- ✅ Covers: Networking, Windows, Linux, macOS, Cloud (Azure/AWS/M365), Hardware, Security, Virtualization
- ✅ Dark terminal-style UI optimized for IT engineers
- ✅ 12 quick-start topic cards for common issues
- ✅ Persistent conversation history per session
- ✅ Mobile responsive

---

## Project Structure

```
minmik-it-assistant/
├── index.html       # Main HTML page
├── style.css        # All styles (dark terminal theme)
├── app.js           # Chat logic + Anthropic API integration
└── README.md        # This file
```

---

## Setup & Deployment

### Option 1: GitHub Pages (Recommended — Free)

1. **Create a GitHub repository** (e.g. `minmik-it-assistant`)
2. **Upload** `index.html`, `style.css`, and `app.js`
3. Go to **Settings → Pages**
4. Set Source to `main` branch, `/ (root)`
5. Your site will be live at `https://yourusername.github.io/minmik-it-assistant`

> **Custom domain**: Add a `CNAME` file containing `minmik.com` to the repo root, then set up a CNAME DNS record pointing to `yourusername.github.io`.

---

### Option 2: Netlify / Vercel (Recommended for custom domain)

1. Push the repo to GitHub
2. Connect to [Netlify](https://netlify.com) or [Vercel](https://vercel.com)
3. Set deploy directory to root
4. Add your custom domain `minmik.com` in the dashboard

---

## API Key Configuration

This app calls the Anthropic Claude API directly from the browser.

### Development / Testing

For quick testing, you can temporarily add your API key in `app.js`:

```javascript
// In app.js — the fetch call inside sendMessage()
const response = await fetch('https://api.anthropic.com/v1/messages', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'x-api-key': 'YOUR_ANTHROPIC_API_KEY',        // ← Add this
    'anthropic-version': '2023-06-01',              // ← Add this
    'anthropic-dangerous-direct-browser-access': 'true'  // ← Required for browser
  },
  ...
```

> ⚠️ **Never commit your API key to a public GitHub repo.**

---

### Production: Secure Setup with a Proxy (Recommended)

For production on `minmik.com`, use a lightweight backend proxy to keep your API key secret.

#### Option A: Netlify Function (Free tier available)

Create `netlify/functions/chat.js`:

```javascript
const Anthropic = require('@anthropic-ai/sdk');

exports.handler = async (event) => {
  if (event.httpMethod !== 'POST') return { statusCode: 405 };

  const client = new Anthropic({ apiKey: process.env.ANTHROPIC_API_KEY });
  const body = JSON.parse(event.body);

  const msg = await client.messages.create({
    model: 'claude-sonnet-4-20250514',
    max_tokens: 1000,
    system: body.system,
    messages: body.messages,
  });

  return {
    statusCode: 200,
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(msg),
  };
};
```

Then in `app.js`, change the fetch URL from:
```
https://api.anthropic.com/v1/messages
```
to:
```
/.netlify/functions/chat
```

Set `ANTHROPIC_API_KEY` as an environment variable in your Netlify dashboard.

#### Option B: Vercel Serverless Function

Create `api/chat.js`:

```javascript
import Anthropic from '@anthropic-ai/sdk';

export default async function handler(req, res) {
  if (req.method !== 'POST') return res.status(405).end();

  const client = new Anthropic({ apiKey: process.env.ANTHROPIC_API_KEY });
  const msg = await client.messages.create({
    model: 'claude-sonnet-4-20250514',
    max_tokens: 1000,
    system: req.body.system,
    messages: req.body.messages,
  });

  res.json(msg);
}
```

Then update the fetch URL in `app.js` to `/api/chat`.

---

## Customisation

### Changing the AI Persona / Domain Focus

Edit the `SYSTEM_PROMPT` constant at the top of `app.js` to change how the AI behaves, what it focuses on, or to add company-specific knowledge.

### Adding Quick Topic Cards

Edit the `TOPICS` array in `app.js`:

```javascript
{ icon: "🔧", title: "My New Topic", level: "L2", prompt: "Detailed prompt about this issue..." },
```

### Branding

- Change `MinMik` references in `index.html` and `app.js`
- Update colors via CSS variables in `style.css` (`:root` block)
- Swap fonts by changing the Google Fonts import in `index.html`

---

## Tech Stack

| Component | Technology |
|---|---|
| Frontend | Vanilla HTML, CSS, JavaScript |
| AI Engine | Anthropic Claude (claude-sonnet-4) |
| Fonts | JetBrains Mono + Syne (Google Fonts) |
| Hosting | GitHub Pages / Netlify / Vercel |
| Backend (optional) | Netlify Functions / Vercel API routes |

No frameworks, no build step, no dependencies — pure HTML/CSS/JS.

---

## License

MIT — free to use and modify.

---

*Built for IT professionals by minmik.com*
