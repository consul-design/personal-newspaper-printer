# Execution Plan
## Personal Newspaper Printer — Phased Build

**Date:** March 15, 2026

---

## Phase 1: Software MVP (Days 1-4)

**Goal:** A working content pipeline that generates and prints a beautiful daily newspaper on off-the-shelf hardware.

### Day 1: Content Pipeline

- [ ] Project scaffolding: Python project, FastAPI backend, SQLite database, config
- [ ] RSS feed ingestion: fetch and parse 3-5 configurable feeds (Guardian, BBC, HN, Reuters)
- [ ] Story selection: rank stories by recency and source priority, deduplicate, select top 5-7
- [ ] Claude Haiku integration: system prompt for news summarization, 80-100 word summaries per story
- [ ] Weather data: fetch from OpenWeatherMap API, extract current conditions + daily high/low
- [ ] "Delight" content: quote-of-the-day API or curated list of poems/facts
- [ ] Store daily edition as structured JSON (headline, summary, source, section, weather, delight)

**End of Day 1:** Running the pipeline script produces a JSON file with a complete daily edition.

---

### Day 2: Layout Engine

- [ ] Bitmap renderer: Python + Pillow, 576px wide (80mm at 203 DPI), 1-bit output
- [ ] Masthead: date, edition number, weather summary with icon, custom wordmark/title
- [ ] Section rendering: headline (bold, larger font) + summary body (regular, 9-10pt sans-serif)
- [ ] Horizontal rules and section dividers
- [ ] Delight section: styled differently from news (italic, indented, or boxed)
- [ ] Footer: small print with source attributions and "AI-generated summaries" note
- [ ] Font selection and testing: try Inter, IBM Plex Sans, or Source Sans at 203 DPI on actual thermal paper
- [ ] Output: PNG file ready for ESC/POS raster printing

**End of Day 2:** The pipeline produces a PNG image that looks like a tiny newspaper — readable, well-typeset, with clear hierarchy.

---

### Day 3: Print Delivery + Device Setup

- [ ] Raspberry Pi Zero W setup: flash Raspbian Lite, enable WiFi, SSH
- [ ] USB thermal printer connection: detect and configure 80mm printer via python-escpos
- [ ] Print agent: lightweight script that polls server for new editions, downloads PNG, prints via raster mode
- [ ] Cron configuration: server generates edition at configured time, device checks every 5 minutes
- [ ] Offline fallback: cache the last successful edition, print it with a "we're offline" header if server is unreachable
- [ ] Basic error handling: paper out detection (if supported by mechanism), printer disconnect recovery
- [ ] Device setup script: one-command install on a fresh Pi (`curl ... | bash` or similar)

**End of Day 3:** A Raspberry Pi + thermal printer prints a daily newspaper automatically. The core magic works.

---

### Day 4: Configuration Web Page + Polish

- [ ] Simple web page (single HTML file + vanilla JS, or minimal Next.js):
  - Topic selection (checkboxes for preset categories + custom RSS feed input)
  - Print time (time picker, default 7:00 AM)
  - Print length (Short / Medium / Long radio buttons)
  - Weather location (city name input)
  - Device pairing (display pairing code)
- [ ] API endpoints: GET/POST settings, GET latest edition, GET device status
- [ ] Deploy backend to Railway or Fly.io
- [ ] Deploy web page to Vercel or serve from the same backend
- [ ] End-to-end test: configure via web → wait for print time → newspaper prints
- [ ] Content quality tuning: refine summarization prompts, adjust layout spacing, test with real multi-day content
- [ ] Recruit 3-5 alpha users (founder's network) — ship them a Pi + printer + setup instructions

**End of Day 4:** Complete working product. Configure on web, newspaper prints every morning. Alpha users onboarded.

---

## Phase 2: Better Layout & Content (Weeks 2-4)

**Goal:** Make the newspaper genuinely beautiful and the content genuinely useful.

- [ ] Typography refinement: test 5+ fonts at 203 DPI, optimize line spacing, character spacing, margins
- [ ] Richer layout elements: weather blocks with multi-day forecast, small section icons, decorative borders
- [ ] Content expansion: add more feed sources, topic categories, and content types (sports scores, stock tickers, comics/puzzles)
- [ ] Multiple editions: support morning + evening prints with different content mixes
- [ ] Smart story selection: avoid repeating stories across editions, track what's been printed
- [ ] Image support: dithered 1-bit images for key stories (test visual quality on thermal paper)
- [ ] Editorial voice tuning: experiment with different AI personalities (neutral, wry, casual) — potentially user-configurable
- [ ] Print length optimization: measure actual paper usage, calibrate content-to-length accurately
- [ ] User feedback mechanism: QR code on each print linking to "rate today's edition" web page
- [ ] Multi-user support: household profiles with different topic preferences, printed as sections in one edition

---

## Phase 3: Custom Hardware & Enclosure (Months 2-4)

**Goal:** Turn the prototype into a product someone would be proud to have on their kitchen counter.

### Hardware Design

- [ ] Switch from Raspberry Pi to ESP32 (cost reduction: $10 → $3)
- [ ] Custom PCB design: ESP32 + thermal mechanism + USB-C power + status LED + WiFi antenna
- [ ] Enclosure industrial design: commission from an ID firm or freelance industrial designer
- [ ] Material selection: matte plastic, wood accent, or metal — match the design language decision
- [ ] Paper loading mechanism: easy drop-in roll replacement, visible paper level indicator
- [ ] Auto-cutter integration (if included based on qualifying questions)
- [ ] Status LED: breathing pattern when printing, color change for errors
- [ ] Physical prototype: 3D print enclosure, hand-assemble 5-10 units for extended testing

### Production Prep

- [ ] PCB manufacturing: get quotes from JLCPCB/PCBWay for 100-unit and 1,000-unit runs
- [ ] Enclosure tooling: injection mold quotes (expect $5,000-15,000 for tooling)
- [ ] FCC pre-scan: test WiFi emissions before full certification ($3,000-5,000)
- [ ] Packaging design: unboxing experience, quick-start guide, branded paper roll included
- [ ] Supply chain: identify thermal mechanism supplier, paper supplier, PCB assembler

### Software for Production

- [ ] OTA firmware updates: device can receive software updates over WiFi
- [ ] Device provisioning: factory flash + cloud registration flow
- [ ] Monitoring: device health telemetry (print success/failure, WiFi strength, uptime)
- [ ] User accounts: authentication, multi-device support, billing integration

---

## Hardware BOM for MVP Prototype

Order these parts to build the Day 1-4 prototype:

| Part | Source | Est. Cost | Notes |
|---|---|---|---|
| Raspberry Pi Zero W | Amazon / Adafruit | $10-15 | Get the WH (with headers) for easier wiring |
| 80mm USB thermal printer | Amazon / AliExpress | $15-25 | Search "80mm thermal receipt printer USB". GOOJPRT or similar. |
| USB OTG adapter (micro-USB to USB-A) | Amazon | $3 | Pi Zero uses micro-USB for USB host |
| USB-C power supply (5V 2.5A) | Amazon | $8 | Must power both Pi and printer — verify amperage |
| MicroSD card (16GB+) | Amazon | $5 | For Raspbian OS |
| 80mm thermal paper rolls (5-pack) | Amazon | $8-12 | Standard 80mm x 50' rolls |
| **Total** | | **$49-68** | |

**Optional upgrades:**
- 300 DPI thermal printer (~$10-15 more) for sharper text
- Raspberry Pi Zero 2 W (~$5 more) for faster bitmap processing
- 3D-printed enclosure (if you have a printer, ~$2 in filament)

---

## Agent Task Breakdown

For development using Claude Code or similar AI coding agents:

### Agent 1: Content Pipeline
```
Build a Python module that:
1. Fetches RSS feeds from a configurable list of URLs
2. Parses and ranks stories by recency, deduplicates by similarity
3. Calls Claude Haiku API to summarize each story in 80-100 words
4. Fetches weather data from OpenWeatherMap for a configured location
5. Selects a "delight" item (quote/poem/fact) from a curated list or API
6. Outputs a structured JSON edition file
```

### Agent 2: Layout Engine
```
Build a Python module that:
1. Takes a JSON edition file as input
2. Renders a 576px-wide, 1-bit PNG image using Pillow
3. Includes: masthead (title, date, weather), story sections (headline + summary),
   delight section, footer with attributions
4. Uses a clean sans-serif font, proper hierarchy (headline sizes, body size, spacing)
5. Handles variable content lengths (more stories = taller image)
```

### Agent 3: Print Delivery
```
Build a Python script for Raspberry Pi that:
1. Connects to an 80mm USB thermal printer via python-escpos
2. Polls a server URL for new edition PNGs
3. Downloads and prints the PNG in raster mode
4. Caches the last successful edition for offline fallback
5. Runs as a systemd service on boot
```

### Agent 4: Web Configuration
```
Build a minimal web app that:
1. Lets users configure: topics (RSS feeds), print time, print length, weather location
2. Stores config in SQLite
3. Serves a device pairing endpoint
4. Exposes API: GET /settings, POST /settings, GET /editions/latest
5. Single-page, minimal dependencies
```

### Agent 5: FastAPI Backend
```
Build the orchestration backend that:
1. Runs the content pipeline on a cron schedule
2. Stores editions and serves them to devices
3. Handles device registration and settings
4. Deploys to Railway/Fly.io with a single command
```

---

## After MVP Validates

If the core loop works (people use it daily, look forward to it, prefer it to their phone):

- **Crowdfunding campaign:** Kickstarter with a beautiful prototype video, target $50-100K
- **Custom hardware:** Commission industrial design, move to ESP32, injection-molded enclosure
- **Paper subscription:** Branded BPA-free paper rolls, auto-shipped monthly
- **Content partnerships:** Curated "publications" from media brands (The Skimm, Morning Brew, local papers)
- **B2B:** Hotel lobbies, Airbnb properties, co-working spaces — daily brief for guests
- **Open source community:** Open-source the layout engine and content pipeline, sell the hardware
- **International:** Localized content, multi-language support, international shipping
- **Evening edition:** Second daily print with different content (end-of-day summary, tomorrow's weather, evening reading)
