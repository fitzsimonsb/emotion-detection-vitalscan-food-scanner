# VitalScan — AI Heart Rate, HRV & Blood Pressure Detection

Optical photoplethysmography (PPG) in the browser. Cover your phone's camera lens with your fingertip to measure heart rate, heart rate variability, and estimated blood pressure — no wearable, no app, no hardware required.

![Demo](https://img.shields.io/badge/demo-live-brightgreen) ![License](https://img.shields.io/badge/license-MIT-blue) ![No Backend](https://img.shields.io/badge/backend-none-lightgrey) ![Mobile First](https://img.shields.io/badge/mobile-first-orange)

---

## What it does

Place your fingertip firmly over your phone's rear camera (with torch on). The demo samples the red channel of the video feed for 20 seconds, detecting the subtle pulsing of blood through your capillaries. It then analyses the signal to output:

- **Heart Rate** (BPM) — with bradycardia/normal/tachycardia classification
- **HRV** (RMSSD in ms) — heart rate variability index
- **Estimated Blood Pressure** (systolic/diastolic mmHg) — with stage classification

A live PPG waveform renders in real time so you can see the pulse signal as it's being captured.

---

## How it works

### Optical PPG

Photoplethysmography works by shining light through tissue and measuring how much is absorbed. Blood absorbs more red light than surrounding tissue, and absorption changes with each heartbeat as blood volume fluctuates. With a torch illuminating your fingertip and the camera reading the reflected red channel, the signal is strong enough to detect individual pulse peaks.

### Signal processing pipeline

1. **Frame capture** — video frames are drawn to a hidden canvas at ~30fps
2. **Coverage detection** — average red/green ratio determines if a finger is present (`red > 100` and `red/green > 1.8`)
3. **Red channel averaging** — mean red value per frame is recorded with a timestamp over 20 seconds (~600 samples)
4. **Smoothing** — a moving average filter (window=5) reduces noise
5. **Peak detection** — threshold-crossing algorithm identifies pulse peaks from the smoothed signal
6. **BPM calculation** — mean inter-peak interval converted to beats per minute
7. **HRV (RMSSD)** — root mean square of successive differences between adjacent RR intervals
8. **BP estimation** — systolic/diastolic heuristics derived from BPM and HRV (not clinically validated)

All processing runs synchronously in JavaScript on the main thread — no WebWorkers, no WASM, no external libraries.

### Torch activation

On supported devices, the [Image Capture API](https://developer.mozilla.org/en-US/docs/Web/API/ImageCapture) is used to enable the torch automatically when scanning starts. This significantly improves signal quality. Falls back gracefully on devices that don't support torch control.

---

## Technical stack

- **Vanilla HTML/CSS/JS** — zero dependencies, zero build tools
- **MediaDevices API** — rear camera access via `getUserMedia` with `facingMode: environment`
- **Canvas API** — per-frame pixel sampling via `getImageData`
- **Image Capture API** — torch/flashlight control on mobile
- **`requestAnimationFrame`** — main render/sample loop
- **`performance.now()`** — high-resolution timestamps for accurate interval measurement

---

## Running locally

```bash
git clone https://github.com/YOUR_USERNAME/vitalscan.git
cd vitalscan

# Serve locally — camera requires HTTPS or localhost
npx serve .
```

Then open on your **phone** (not desktop) for best results — you need a rear camera with a torch.

For HTTPS on local network (to test on phone from laptop):

```bash
npx serve . --ssl-cert cert.pem --ssl-key key.pem
# or
npx local-ssl-proxy --source 443 --target 3000
```

---

## Usage tips

- **Use a phone**, not a laptop — rear camera + torch gives a much stronger signal
- **Press firmly** — loose contact reduces signal quality significantly
- **Stay still** — movement introduces noise that degrades accuracy
- **Good lighting conditions** don't matter — the torch provides its own illumination
- If the waveform looks flat, reposition your finger and ensure full lens coverage

---

## Accuracy & limitations

| Metric | Accuracy |
|---|---|
| Heart Rate | ±3–5 BPM under good conditions |
| HRV (RMSSD) | Approximate — useful for relative comparison only |
| Blood Pressure | **Not clinically accurate** — heuristic estimate only |

Blood pressure estimation via PPG without a pressure cuff is an active research area. The values here are derived from BPM/HRV correlations and should not be used for any medical purpose. This is a technology demonstration only.

---

## Browser support

- **iOS Safari 15+** — torch support via `getUserMedia` constraints
- **Android Chrome** — full support including torch
- **Desktop Chrome/Firefox** — works for HR only (no torch, weaker signal)

Camera access requires HTTPS or localhost in all browsers.

---

## Project structure

```
vitalscan/
└── index.html    # Single file — HTML, CSS, signal processing JS
```

---

## Part of a larger project

This demo was built as part of a personal portfolio site exploring browser-native sensing capabilities. All three demos live in the same repo:

- **[emotion-detection-vitalscan-food-scanner](https://github.com/fitzsimonsb/emotion-detection-vitalscan-food-scanner)**
  - [AI Vision Emotion Detection](https://github.com/fitzsimonsb/emotion-detection-vitalscan-food-scanner/tree/main/emotion-detection) — real-time facial emotion classification via webcam
  - [AI Food Scanner](https://github.com/fitzsimonsb/emotion-detection-vitalscan-food-scanner/tree/main/food-scanner) — meal identification and nutrition estimation via Claude Vision

---

## License

MIT — use it, fork it, build on it.
