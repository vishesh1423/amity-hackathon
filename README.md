# amity-hackathon


#  AURA-Civic

**Real-Time Civic Intelligence & Spatial Anomaly Engine**

AURA-Civic is a single-page, browser-based dashboard that simulates the fusion 
of multiple municipal data streams into one real-time operational picture. 
It combines 311 complaints, traffic speed, air quality (AQI), ambient noise, 
and transit delay data across six city districts, applies statistical anomaly 
detection, and generates plain-language AI briefings for city staff.

![status](https://img.shields.io/badge/status-prototype-orange)
![stack](https://img.shields.io/badge/stack-vanilla%20JS%20%2B%20Tailwind-38bdf8)
![license](https://img.shields.io/badge/license-MIT-green)

---

##  Features

-  **Interactive hex-grid spatial map** (Leaflet.js) — six districts colored live by anomaly status: Normal, Caution, Critical
-  **Real-time telemetry charts** (Chart.js) across five civic data streams: 311 complaints, traffic speed, AQI, noise, transit delay
-  **Cross-stream Pearson correlation matrix** — surfaces hidden relationships between streams (e.g. water main break → traffic slowdown)
-  **Statistical anomaly engine** — rolling Z-scores per stream, fused into a single per-district isolation score with adjustable sensitivity threshold
-  **AI-generated neighborhood narratives** — plain-language executive summaries with optional Google Gemini API integration, falling back to a built-in heuristic engine when no key is supplied
-  **Scenario injection** — trigger simulated events (water main break, heatwave, concert noise, industrial emissions) to test anomaly response
-  **Text-to-speech briefings** via the Web Speech API
-  **Export** to PDF / JSON
-  Cyberpunk-styled dark UI (Tailwind CSS + glassmorphism)

---

##  Tech Stack

| Layer      | Library |
|------------|---------|
| Styling    | [Tailwind CSS](https://tailwindcss.com/) (CDN) |
| Mapping    | [Leaflet.js](https://leafletjs.com/) |
| Charts     | [Chart.js](https://www.chartjs.org/) |
| Icons      | [Font Awesome](https://fontawesome.com/) |
| Fonts      | Inter, JetBrains Mono (Google Fonts) |
| AI (optional) | Google Gemini API |

No build step, no package manager, no framework — it's a single self-contained HTML file.

---

##  Getting Started

1. Clone the repo:
```bash
   git clone https://github.com/<your-username>/aura-civic.git
   cd aura-civic
```
2. Open `index.html` in a browser. That's it — no server or build step required.

For full map tile support, you may want a local static server (e.g. `npx serve` or `python3 -m http.server`) instead of opening the file directly via `file://`.

---

##  Configuration

Click the **gear icon** (top right) to open the config panel:

| Setting | Purpose |
|---|---|
| **Google Maps API Key** | Enables Roadmap / Satellite / Hybrid / Terrain map layers. Falls back to a public Carto dark-mode basemap if omitted. |
| **Google Gemini API Key** *(optional)* | Enables live AI-generated narratives via the Gemini API. If left blank, a built-in heuristic engine emulates the narrative output. |
| **Gemini Model** | Choose the target model for narrative generation. |
| **Anomaly Sensitivity (\|Z\| ≥)** | Adjusts how sensitive the anomaly detector is to statistical deviation (1.5 = very sensitive, 3.5 = conservative). |

>  **Security note:** API keys entered here are stored in browser `localStorage` and used directly from client-side JavaScript. This is fine for local/personal use, but **do not deploy this publicly with real API keys** — anyone with browser dev tools can read them. For production use, proxy API calls through a backend.

---

##  How the Anomaly Engine Works

1. Each district maintains a rolling window of the last 20 telemetry ticks per stream.
2. A **Z-score** is computed per stream: `(current value − rolling mean) / rolling stddev`.
3. The max absolute Z-score across all streams feeds a synthetic **isolation score** (0–1), which determines district status:
   - `< 0.35` → **Normal**
   - `0.35–0.65` → **Caution**
   - `≥ 0.65` → **Critical**
4. Streams whose Z-score exceeds the configured sensitivity threshold are logged to the live anomaly feed.
5. A **Pearson correlation matrix** is computed across streams every tick to detect cross-stream
