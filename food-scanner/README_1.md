# AI Computer Vision Food Scanner

Point your camera at any meal and get instant food identification and nutritional breakdown, powered by Claude's vision model. One tap — no typing, no barcode scanning, no manual logging.

![Demo](https://img.shields.io/badge/demo-live-brightgreen) ![License](https://img.shields.io/badge/license-MIT-blue) ![Powered by Claude](https://img.shields.io/badge/powered%20by-Claude%20Sonnet-blueviolet)

---

## What it does

Capture a single frame from your camera and the demo will:

- **Identify every food item** visible in the image with a confidence rating (high / medium / low)
- **Classify the meal type** — Breakfast, Lunch, Dinner, Snack
- **Estimate macronutrients** — calories, protein, carbohydrates, fat
- **Add a brief observation** about the meal's nutritional profile

Results appear in a structured panel alongside the captured image with a ↩ Retake option if you want a better shot.

---

## How it works

1. **Camera capture** — `getUserMedia` opens the rear camera (rear-facing preferred, ideal for pointing at food)
2. **Frame snapshot** — a single frame is drawn to a hidden canvas on tap
3. **Compression** — the frame is scaled to max 800px wide and encoded as JPEG at 82% quality (~100–300kb typically)
4. **API call** — the base64-encoded image is sent to `/api/scan-food`, a serverless Vercel function
5. **Claude Vision** — the function forwards the request to `claude-sonnet-4-20250514` with a structured JSON prompt
6. **Result rendering** — the JSON response is parsed and displayed as a meal card with macro grid

The frontend calls `/api/scan-food` rather than the Anthropic API directly — this keeps the API key server-side and avoids CORS issues in the browser.

---

## Technical stack

- **Frontend** — Vanilla HTML/CSS/JS, single `index.html`, zero dependencies
- **AI model** — [Claude Sonnet](https://anthropic.com) (`claude-sonnet-4-20250514`) via Anthropic API
- **Backend** — Vercel serverless function (`api/scan-food.js`) as API proxy
- **Camera** — `MediaDevices.getUserMedia` with `facingMode: environment`
- **Image encoding** — Canvas API → `toDataURL('image/jpeg', 0.82)` → base64

---

## Setup & deployment

### Prerequisites

- A [Vercel](https://vercel.com) account
- An [Anthropic API key](https://console.anthropic.com) (free to create; cost per scan is a fraction of a cent)

### Deploy to Vercel

```bash
# Clone the repo
git clone https://github.com/YOUR_USERNAME/food-scanner.git
cd food-scanner

# Install Vercel CLI
npm i -g vercel

# Deploy
vercel
```

### Add your API key

In the Vercel dashboard for your project:

1. Go to **Settings → Environment Variables**
2. Add: `ANTHROPIC_API_KEY` = `your-key-here`
3. Redeploy

The serverless function at `api/scan-food.js` reads `process.env.ANTHROPIC_API_KEY` — it is never exposed to the browser.

### Running locally

```bash
# Install Vercel CLI if not already installed
npm i -g vercel

# Run locally with environment variables
ANTHROPIC_API_KEY=your-key-here vercel dev
```

This starts both the static frontend and the serverless function locally.

---

## Project structure

```
food-scanner/
├── index.html          # Frontend — HTML, CSS, camera + result logic
└── api/
    └── scan-food.js    # Vercel serverless function — Anthropic API proxy
```

---

## Cost

Each scan makes one API call to Claude Sonnet. At current Anthropic pricing, a single food scan costs approximately **$0.002–$0.004** depending on image size and response length. For personal demo use this is negligible.

---

## Accuracy

Food identification accuracy is generally very good for common dishes, packaged food, and restaurant meals. Nutritional estimates are approximations based on typical portion sizes — not a replacement for a food scale or verified database.

Results are less reliable for:
- Mixed dishes where individual ingredients are hard to distinguish
- Unusual or regional foods outside common training data
- Very small portions or low-quality images

---

## Browser support

- **iOS Safari 15+** — full support
- **Android Chrome** — full support
- **Desktop Chrome/Firefox** — works, but rear camera preferred for food shots

Camera requires HTTPS or localhost. Vercel deployments are HTTPS by default.

---

## Part of a larger project

This demo was built as part of a personal portfolio site exploring AI-powered browser capabilities. See also:
- [AI Vision Emotion Detection](https://github.com/YOUR_USERNAME/emotion-detection) — real-time facial emotion classification, fully on-device
- [VitalScan](https://github.com/YOUR_USERNAME/vitalscan) — optical PPG heart rate & blood pressure via phone camera

---

## License

MIT — use it, fork it, build on it.
