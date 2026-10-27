# HealthGuard AI

An AI-powered personal healthcare ecosystem with nine collaborating agents and **AI Early Disease Risk Detection**, which spots unhealthy trends before they become problems.

## What's in this folder
| File | Purpose |
|------|---------|
| `index.html` | Working front-end demo (no build step). Open it in a browser. |
| `requirements.md` | Full functional and non-functional requirements |
| `readme.md` | This guide |
| `tasks.md` | Delivery plan and checklist |

## Try the demo
1. Open `index.html`.
2. On **Dashboard**, save a new reading. Charts, the health score and the agent feed update.
3. Open **Risk detection** to see 14-day trend slopes, flags and a 7-day forecast.
4. Try the **Assistant** (type or use the mic). Typing "chest pain" triggers emergency guidance.
5. Use **Emergency** for SOS with live location, **Medicines** for missed-dose alerts, **Records** for search and PDF export (browser print).
6. Toggle dark mode, high contrast and font size at the top.

The demo keeps data in memory with sample values. The assistant is rule-based; production swaps in an LLM API.

## How early risk detection works
For each metric, the last 14 days get a least-squares slope. A slope above a threshold means *Watch*, above twice the threshold means *Needs attention*. The flag goes to the agent bus: Nutrition, Fitness, Mental Wellness and Appointment agents respond with preventive steps. It is preventive guidance only, not a diagnosis.

## Production architecture
```
React + TS + Tailwind (PWA)  →  Express API (JWT, RBAC, rate limit)  →  MongoDB (encrypted fields)
                                        │
                          Agent orchestrator + event bus  →  LLM API
```
- **Auth:** JWT access/refresh tokens, Google OAuth, roles: patient, doctor, admin.
- **Privacy:** AES-256 field encryption, audit log on every record access, HIPAA-inspired controls.
- **Agents:** Monitoring, Symptom, Medication, Nutrition, Fitness, Appointment, Emergency, Records, Mental Wellness.

## Suggested folder layout
```
/client  (components, hooks, pages, charts)
/server  (routes, controllers, agents, middleware, models)
/shared  (types)
```

## Accessibility
Skip link, keyboard-operable controls, ARIA live regions for chat and SOS, high-contrast theme, adjustable font size, voice input and output, reduced-motion support.

## Medical disclaimer
HealthGuard AI provides general information and is not a medical device or a substitute for professional care. In an emergency, call your local emergency number.

## Version 2 additions
| Feature | Where | What it does |
|---|---|---|
| Multi-agent AI | Agents tab | "Run consultation" makes all nine agents report on shared state; the orchestrator ranks, links and plans |
| Predictive risk detection | Risk detection tab | 14-day trend slopes, condition risk indicators (hypertension, diabetes, burnout), 7-day forecast |
| Fall detection | Fall detection tab | Phone motion sensor, 15-second countdown, automatic SOS. "Simulate a fall" works on any device |
| Medical report analysis | Report analysis tab | Paste lab values; each is flagged low, normal or high with plain-language guidance |
| Strong security | Security tab | AES-256-GCM encrypted vault, roles, hash-chained audit log with tamper demo, CSP, escaping, idle lock |
| Automated testing | Tests tab | 28 tests run on load and on demand |

### Try the security demo
1. Add a record, open Security, enter a passphrase and press Encrypt and lock. Records show as locked.
2. Unlock with the passphrase. A wrong passphrase fails, and three failures pause unlocking for 30 seconds.
3. Switch role to admin, press Simulate tampering, then Verify chain.

### Honest limits
The demo does its security work in the browser. Real protection (JWT, OAuth, server-side encryption, server-enforced RBAC) belongs to the Express backend in `tasks.md`. The meta-tag CSP allows inline scripts, so a production build should use a header CSP with nonces. Risk indicators are trend heuristics, not validated clinical models.

## Version 3: elder-care platform
| Area | Where | What it does |
|---|---|---|
| Elder care | Elder care tab | Caregiver list, daily check-in, STEADI fall-risk screen, elder mode |
| Caregiver alerts | Emergency tab | SOS and fall alerts start an ordered workflow: caregiver 1, then caregiver 2, then 112, with an "acknowledged" button that stops escalation |
| Wearables | Wearables tab | Pair a Bluetooth heart-rate device or blood-pressure cuff (Chrome or Edge, https) or simulate readings |
| Real AI model | Assistant, "Connect a real AI model" | Send questions to your own server that calls a model |
| Encrypted storage | Security tab | Records are saved on the device only as AES-256-GCM ciphertext |
| Clinical review | Clinical review tab | Clinician feedback form, JSON export and validation plan |

## Where it honestly stands
| Gap | Status |
|---|---|
| Wearable integration | Heart-rate and blood-pressure profiles implemented and unit-tested against the Bluetooth byte formats. Not yet tried with physical hardware. Glucose meters not supported. |
| Real AI model | The connection is built; a model answers only once you deploy the proxy below. Red-flag symptoms are always handled locally. |
| Test coverage | 28 tests. Node coverage of the logic module: 100% lines, 97.2% branches, 97.2% functions. The UI is not covered; production needs Playwright tests. |
| Encrypted storage | Done in the browser (ciphertext in localStorage). Production should use server-side encryption with managed keys. |
| Caregiver workflows | The alert chain, acknowledgement and escalation work. Messages go out through the phone's SMS and dialler apps. Automatic delivery needs Twilio or FCM on a server. |
| Clinical validation | Not done. The form and plan exist to collect it. |

### Reproduce the coverage report
Save this as `hg-coverage.js` next to `index.html` and run `node hg-coverage.js` (Node 20 or newer).
```js
// Run from the folder that holds index.html:  node hg-coverage.js   (Node 20+)
const fs=require('fs'),{execSync}=require('child_process');
const h=fs.readFileSync('index.html','utf8'),cut=(a,b)=>h.slice(h.indexOf(a)+a.length,h.indexOf(b));
const pre=['slope','last','esc'].map(n=>h.match(new RegExp('const '+n+'=[^\\n]*'))[0]).join('\n');
const pure=cut('/*PURE-START*/','/*PURE-END*/'),tests=cut('/*TESTS-START*/','/*TESTS-END*/');
const names=[...new Set([...(pre+'\n'+pure).matchAll(/^(?:const|function|async function) ([A-Za-z_]\w*)/gm)].map(m=>m[1]))].join(',');
fs.writeFileSync('hg-logic.js',pre+'\n'+pure+'\nmodule.exports={'+names+'};');
fs.writeFileSync('hg-logic.test.js',"const {test}=require('node:test'),assert=require('node:assert');const {"+names+"}=require('./hg-logic.js');\n"+tests+"\nfor(const [n,f] of tests) test(n,async()=>assert.ok(await f()));");
execSync('node --test --experimental-test-coverage hg-logic.test.js',{stdio:'inherit'});
```

### Example server for the real AI model
```js
import express from 'express';
const app = express();
app.use(express.json({ limit: '10kb' }));
app.use((req, res, next) => {
  res.set('Access-Control-Allow-Origin', 'https://YOUR-NAME.github.io');
  res.set('Access-Control-Allow-Headers', 'Content-Type');
  if (req.method === 'OPTIONS') return res.sendStatus(204);
  next();
});
app.post('/api/chat', async (req, res) => {
  const r = await fetch('https://api.anthropic.com/v1/messages', {
    method: 'POST',
    headers: { 'content-type': 'application/json', 'x-api-key': process.env.ANTHROPIC_API_KEY, 'anthropic-version': '2023-06-01' },
    body: JSON.stringify({
      model: process.env.MODEL, max_tokens: 500,
      system: 'You give careful general health information to older adults. Never diagnose. Tell the user to seek emergency care for red-flag symptoms.',
      messages: [{ role: 'user', content: String(req.body.message).slice(0, 1000) }]
    })
  });
  const j = await r.json();
  res.json({ reply: j.content?.[0]?.text || 'Sorry, I could not answer that.' });
});
app.listen(8787);
```
Keep the API key in an environment variable on the server, add rate limiting and authentication before real use, and set `MODEL` to a current model name from the provider's docs.

## Portfolio case study
**Pitch:** HealthGuard AI is an elder-care platform where nine cooperating agents watch health trends, catch falls and escalate alerts to caregivers.
**Problem:** Older adults living alone have slow-moving health drift and sudden emergencies, and families find out late.
**Solution:** Trend-based early risk flags, fall detection, lab-report explanations, an encrypted record vault and a timed caregiver escalation chain.
**What to show in a 90-second demo:**
1. Log a rising BP reading and open Risk detection.
2. Run the multi-agent consultation.
3. Simulate a fall, wait for the countdown and show the escalation steps.
4. Paste a lab report and show the flags.
5. Lock the vault, then show the tamper-evident audit log.
6. Open the Tests tab: 28 of 28 passing.
**Links to add:** live demo, demo video, GitHub repository.
