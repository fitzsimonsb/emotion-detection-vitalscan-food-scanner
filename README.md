# AI Vision Emotion Detection

Real-time facial emotion classification running entirely in your browser. No server, no API key, no data ever leaves your device.

![Demo](https://img.shields.io/badge/demo-live-brightgreen) ![License](https://img.shields.io/badge/license-MIT-blue) ![No Backend](https://img.shields.io/badge/backend-none-lightgrey)

---

## What it does

Point your webcam at a face and the model detects one of seven emotional states in real time at approximately 15 frames per second. A live probability bar shows the full emotional spectrum simultaneously — not just the dominant emotion, but the confidence level across all seven states.

**Detected states:** Happy · Sad · Angry · Fearful · Disgusted · Surprised · Neutral

---

## How it works

The demo uses two lightweight neural network models from [face-api.js](https://github.com/justadudewhohacks/face-api.js):

| Model | Purpose | Size |
|---|---|---|
| **TinyFaceDetector** | Locates and tracks the face in each frame | ~190kb |
| **FaceExpressionNet** | Classifies the expression from the detected face region | ~310kb |

Both models run via **WebAssembly** directly in the browser — loaded on demand from jsDelivr CDN on first use. There is no backend server. No image data is transmitted anywhere. Everything happens locally on your device.

The detection loop runs every 80ms, drawing a bounding box overlay on the video canvas and updating the emotion bars on each frame.

---

## Technical stack

- **Vanilla HTML/CSS/JS** — zero build tools, zero dependencies beyond face-api.js
- **face-api.js v0.22.2** — TensorFlow.js-based face analysis library
- **WebAssembly** — model inference runs natively in the browser
- **MediaDevices API** — camera access via `getUserMedia`
- **Canvas API** — face bounding box overlay drawn per frame

---

## Running locally

No build step required. Just open the file:

```bash
# Clone the repo
git clone https://github.com/YOUR_USERNAME/emotion-detection.git
cd emotion-detection

# Open directly in browser (models load from CDN)
open index.html

# Or serve locally to avoid any CORS issues with some browsers
npx serve .
```

The models are fetched from CDN on first click — roughly 500kb total, loaded once and cached by the browser.

---

## Browser support

Works in any modern browser with webcam access. Requires:
- `getUserMedia` API (Chrome, Firefox, Safari 14+, Edge)
- WebAssembly support (all modern browsers)
- HTTPS or localhost (browsers block camera on plain HTTP)

---

## Project structure

```
emotion-detection/
└── index.html       # Everything in one file — HTML, CSS, JS
```

---

## Limitations

- Designed for single-face detection. Multiple faces will track whichever scores highest confidence.
- Accuracy degrades in low light or at oblique angles.
- This is a demo — not suitable for clinical, legal, or surveillance use.
- The models were trained primarily on frontal-facing faces.

---

## Part of a larger project

This demo was built as part of a personal portfolio site exploring browser-native AI capabilities. See also:
- [VitalScan](https://github.com/YOUR_USERNAME/vitalscan) — optical PPG heart rate & blood pressure via phone camera
- [AI Food Scanner](https://github.com/YOUR_USERNAME/food-scanner) — meal identification and nutrition estimation via Claude Vision

---

## License

MIT — use it, fork it, build on it.
