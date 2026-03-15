# Product Requirements Document — v1
## Personal Newspaper Printer

**Date:** March 15, 2026
**Status:** Ready to prototype

---

## What We're Building

A device that prints a personalized, beautifully formatted mini newspaper every morning on thermal paper. AI-curated, AI-summarized, finite, and physical. You wake up, the paper is waiting next to your coffee.

**One-liner:** "Paper before pixels — your morning news, printed and waiting."

---

## The Problem

Morning information consumption is broken. You reach for your phone, open a news app or social feed, and 45 minutes evaporate into doomscrolling. The content is infinite, the algorithms are adversarial, and you start your day with anxiety. Meanwhile, the ritual of the morning newspaper — finite, tangible, shared — has been lost.

Screens are the delivery mechanism for most of tech's ills: infinite scroll, notification interrupts, algorithm-driven engagement traps. **The content isn't the problem. The medium is the problem.**

---

## Why Now

- 86% of Gen Z actively trying to reduce screen time
- 148% spike in dumb phone sales among 18-24 year olds
- reMarkable proves consumers pay $450+ for devices that do *less*
- AI summarization is now good enough (and cheap enough) to curate personalized news
- The "morning routine" trend is peaking on social media
- Little Printer proved the emotional connection in 2012 but the tech and market weren't ready

---

## Value Proposition

| For | Current Alternative | Our Advantage |
|---|---|---|
| Phone-checking morning readers | News apps, social feeds | Finite, no doomscroll, physical ritual |
| Existing newspaper subscribers | NYT/WSJ delivery | Personalized, multi-source, summarized, smaller format |
| Digital minimalism practitioners | Blocking apps, dumb phones | Additive (new ritual) not subtractive (removing things) |
| Design-conscious consumers | Nothing — this category doesn't exist | Beautiful object, Teenage Engineering energy |

---

## MVP Scope (Build in 3-5 Days)

### 1. Content Pipeline

- Fetch news from 3-5 RSS feeds (configurable: HN, Guardian, BBC, NYT, Reuters)
- Weather data from a free API (OpenWeatherMap or wttr.in)
- Claude Haiku summarizes each of the top 5-7 stories into 80-100 word summaries
- One "delight" item: quote of the day, poem, or fun fact
- Content pipeline runs on a server (Railway/Fly.io) on a cron schedule

### 2. Layout Engine

- Render the newspaper as a **1-bit bitmap** at 576px wide (80mm at 203 DPI)
- Custom masthead with date, weather icon, and edition name
- Section headers with horizontal rules
- Body text in a clean sans-serif font at 9-10pt
- Output: PNG file ready to send to any ESC/POS printer
- Built with Python + Pillow

### 3. Print Delivery

- Raspberry Pi Zero W connected to an 80mm USB thermal printer
- Pi runs a lightweight agent that polls the server for new editions
- When a new edition is available, Pi downloads the bitmap and prints it
- Fallback: if server is unreachable, print a cached "offline edition" (yesterday's content + "we're having trouble" note)

### 4. Configuration (Simple Web Page)

- **Topics:** Select from preset categories (Tech, World, Business, Science, Culture) or add custom RSS feeds
- **Print time:** Set daily print schedule (default: 7:00 AM)
- **Length:** Short (6"), Medium (12"), Long (18") — controls how many stories
- **Weather location:** City name or zip code
- **Device pairing:** Display a pairing code on the web page, enter it on the Pi (via a simple script)

### What's NOT in v1

- No native mobile app — web page only
- No custom enclosure — bare Pi and printer on the counter
- No multi-user per device — one configuration per printer
- No multiple daily editions — morning only
- No images in articles — text and icons only
- No on-device controls — all configuration via web
- No paper subscription — user buys their own 80mm thermal paper
- No user accounts or authentication — single-user, single-device
- No analytics or reading tracking

---

## How It Works

```
Server (cron, 6 AM) →
  Fetch RSS feeds + weather API →
  Select top stories by relevance/recency →
  Claude Haiku summarizes each story (80-100 words) →
  Layout engine renders bitmap (576px wide PNG) →
  Store edition on server

Device (Pi Zero W, polls every 5 min starting at configured time) →
  Check server for new edition →
  Download bitmap →
  Print via ESC/POS raster command →
  Paper is waiting when you wake up
```

---

## Tech Stack

| Component | Choice | Why |
|---|---|---|
| LLM | Claude Haiku | Cheapest, fastest, more than sufficient for summarization |
| Content Sources | RSS feeds + OpenWeatherMap | Free, reliable, wide coverage |
| Layout Engine | Python + Pillow | Full bitmap control, custom fonts, clean output |
| Backend | Python + FastAPI | Fast to build, async-friendly |
| Database | SQLite | One user, one device — keep it simple |
| Device | Raspberry Pi Zero W | $10, WiFi built-in, USB host for printer |
| Printer | 80mm USB thermal printer (GOOJPRT or similar) | $15-25, 203 DPI, ESC/POS compatible |
| Print Protocol | python-escpos + raster image mode | Bypasses ugly built-in fonts, full layout control |
| Hosting | Railway or Fly.io | Simple Python deployment, cron support |
| Scheduler | Cron (server-side) | Simple, reliable |

---

## Hardware Recommendation for MVP

**Total cost: ~$45-65**

| Part | Estimated Cost |
|---|---|
| Raspberry Pi Zero W | $10-15 |
| 80mm USB thermal printer module | $15-25 |
| USB OTG adapter | $3 |
| USB-C power supply (for Pi) | $8 |
| 80mm thermal paper (5 rolls) | $8-12 |
| MicroSD card (16GB) | $5 |

All available on Amazon or AliExpress. No soldering required. Setup is: flash SD card → plug in printer → connect to WiFi → run setup script.

---

## Key Decisions

**Why thermal, not inkjet?**
- $0.01-0.02 per print vs. $0.10+ for inkjet
- Near-silent operation
- No ink cartridges to replace
- Compact mechanism
- The fading (3-6 months) is a feature — yesterday's news isn't meant to last

**Why 80mm, not 58mm?**
- 48 characters per line vs. 32 — enough for readable prose
- More layout flexibility (section headers, weather blocks, whitespace)
- Marginal cost difference (~$2-3 more per printer module)
- Still compact — smaller than a coffee mug

**Why server-rendered, not local?**
- Keeps the device cheap and simple (ESP32-capable)
- Layout changes don't require device updates
- Content pipeline is complex (RSS parsing, LLM calls, layout) — better on a real server
- Opens the door to future multi-device support
- Risk: server dependency. Mitigation: offline fallback with cached content.

**Why Raspberry Pi, not ESP32?**
- For MVP, Pi is dramatically faster to develop for (full Python, SSH, familiar tooling)
- ESP32 is the right choice for a production device (cheaper, lower power, simpler)
- We'll switch to ESP32 when we design custom hardware

**Why bitmap rendering, not printer-native text?**
- Built-in printer fonts are ugly and limited
- Bitmap mode gives us custom fonts, variable sizes, icons, dividers
- The newspaper needs to look like a newspaper, not a receipt
- Tradeoff: slightly slower print time (~10-15 seconds for a full edition)

---

## User Personas

### Maya, 34 — Design Lead at a Tech Company

Owns a reMarkable tablet, uses a Chemex for morning coffee, has deleted Twitter twice. She wants a morning information ritual that doesn't involve a screen. She'd put this device on her kitchen counter next to her coffee setup and read the print while her coffee brews. She cares deeply about how the device looks and would be embarrassed by an ugly gadget on her counter.

### James, 41 — Freelance Writer, Father of Two

Wants to model screen-free behavior for his kids. The morning newspaper on the kitchen table is something the whole family can see — the kids ask about headlines, and it becomes a conversation starter. He doesn't need the device to be beautiful; he needs it to print reliably at 6:30 AM every day and have content his family can engage with.

### Priya, 28 — Product Manager, Digital Minimalism Enthusiast

Has a Light Phone for weekends, uses Forest app during work hours, and is deeply intentional about her tech stack. She wants the daily print to include HN top stories, a poem, and the weather. She'd share a photo of her morning print on Instagram. She's the customer who writes product reviews and tells 10 friends.

---

## Success Criteria (Week 1 Post-Prototype)

- Device prints reliably every morning at the configured time
- Content is fresh (no stale/repeated stories day to day)
- Summaries are accurate and readable — no hallucinated facts
- The entire print fits on 12-18 inches of paper
- Layout looks like a tiny newspaper, not a receipt
- At least 3 people (including founder) use it daily for a week
- At least 1 person says "I looked forward to this instead of checking my phone"

---

## Open Questions for Founder

1. **Name:** What's this thing called? The name shapes everything — brand, domain, marketing, industrial design personality.
2. **Design language:** Minimal and clean (reMarkable energy) or playful and warm (Little Printer energy)? This determines the enclosure, the masthead, the editorial voice, everything.
3. **Target market priority:** Design enthusiasts first, or anti-screen parents first? This changes the content defaults, the marketing channels, and the price point.
4. **Subscription intent:** Do you want recurring revenue from day one, or prove the hardware value first and add subscription later?
5. **Open source strategy:** Open-source the software and sell the hardware (Red Hat model)? Or keep the content pipeline proprietary?
6. **Crowdfunding:** Is a Kickstarter/Indiegogo campaign part of the plan? If so, we need a beautiful prototype and video before launch.
