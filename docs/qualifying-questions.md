# Qualifying Questions
## Questions to Answer Before Committing to Build

**Date:** March 15, 2026

These questions should be answered (or deliberately deferred) before investing significant engineering time. They're organized by category to facilitate focused conversations.

---

## Hardware Decisions

1. **Paper width — 58mm or 80mm?** 58mm is cheaper and more compact but limits you to ~32 characters per line. 80mm gives ~48 characters — enough for readable prose. Does the content vision require 80mm, or can we design around 58mm's constraints like Little Printer did?

2. **Thermal mechanism quality:** Consumer thermal printers range from $5 AliExpress modules to $50+ Adafruit units. What's the minimum print head quality that produces text we'd be proud of? Have we printed sample layouts on 3-4 different mechanisms to compare?

3. **BPA-free paper:** The EU banned BPA in thermal paper in 2020. Do we commit to BPA-free paper only? This limits supplier options but eliminates a recurring PR risk. Can we source BPA-free rolls at reasonable cost for a paper subscription model?

4. **Connectivity — WiFi only, or WiFi + Bluetooth?** WiFi is essential for fetching content. Bluetooth adds cost and complexity but enables phone-based setup (like Chromecast's onboarding). Is the setup UX worth the BOM increase?

5. **Compute — ESP32 vs. Raspberry Pi Zero W vs. cloud-rendered?** ESP32 is $2-3 and sufficient if we render layouts server-side and send bitmaps. Pi Zero W is $5-10 and can render locally. Cloud rendering reduces device cost but adds latency and server dependency. Which architecture do we want?

6. **Auto-cutter:** Should the device include a paper auto-cutter (~$3-5 BOM cost), or does the user tear the paper? Auto-cut feels more polished. Manual tear feels more like ripping an article from a newspaper. Which aligns with the product personality?

7. **Paper roll size and replacement frequency:** Standard 80mm rolls are ~50 feet. At ~12 inches per daily print, one roll lasts ~50 days. Is that acceptable? Should we design for a larger roll compartment to extend replacement intervals?

---

## Print Quality & Typography

8. **Raster vs. native text:** Do we render all content as bitmaps (full typographic control, custom fonts, icons) or use the printer's built-in character set (faster, simpler, uglier)? Raster is clearly better for quality — but is print speed acceptable for a ~15-inch daily print?

9. **Font selection:** At 203 DPI, serif fonts get muddy below 10pt. What's our font stack? Do we commission a custom pixel-optimized font, or find an existing one that works well at thermal DPI? Have we tested how common fonts (Inter, IBM Plex, JetBrains Mono) render at 203 DPI on actual thermal paper?

10. **Header design:** Every newspaper has a masthead. What does ours look like? A small graphic? Custom wordmark? Date and weather? This is the first thing the user sees every morning — it sets the tone.

11. **203 DPI vs. 300 DPI:** Premium thermal mechanisms offer 300 DPI. The cost increase is ~$5-10 per unit. Is the quality improvement visible enough on thermal paper to justify the cost? Has anyone done a side-by-side comparison?

---

## Content & Layout

12. **What goes on the paper?** The MVP needs a default content mix. Proposed: weather + 5 news summaries + one "delight" (poem, quote, puzzle, fun fact). Is that the right mix? What does the user look forward to most — the news or the delight?

13. **Content personalization depth:** How personalized should the content be? Options range from "pick 3 topics" (simple) to "learns from your reading patterns over time" (complex). What's the MVP level?

14. **Editorial voice:** Should the summaries be neutral/AP style, or can the AI have a personality? A wry, slightly editorial voice ("Well, Congress is at it again...") might create more engagement but risks alienating users. Is there a personality slider, or do we pick one voice?

15. **Content length — how long is the daily print?** 6 inches feels too short (just headlines). 24 inches feels like too much paper. What's the sweet spot? Do we let users configure this, or make an opinionated choice?

16. **Multiple editions:** The idea source mentions "multiple times a day, like when people received newspapers multiple times a day." Do we build for this from the start, or is it a future feature? How does the user signal they want a 5 PM evening edition?

17. **Images:** Can we print images? Thermal printers can print 1-bit bitmaps — dithered photos look interesting but consume paper fast. Do we include weather icons, small section graphics, or even dithered article images? Or is text-only more elegant?

18. **Local information:** Weather is obvious. What about local events, transit alerts, air quality? How do we get the user's location — IP geolocation, explicit setting, or phone GPS during setup?

---

## User Experience

19. **Setup flow:** The target user is design-conscious but not necessarily technical. What does the out-of-box experience look like? Plug in → connect to WiFi → scan QR code → configure in browser → first print in 5 minutes? Or do we need a native app?

20. **Configuration interface:** Web app, native app, or on-device controls? Web app is cheapest to build. Native app enables push notifications ("your newspaper is printing"). On-device buttons are charming but limited. What's the MVP choice?

21. **Print schedule:** Fixed time (7 AM), or flexible? What if the user wakes up at 5:30 AM on weekdays and 9 AM on weekends? Do we need schedule profiles?

22. **Missed prints:** The user is away for a week. Does the device keep printing? Does paper pile up and jam? Do we need "vacation mode"? Does the device detect a full paper tray?

23. **Error communication:** The device has no screen. How does it tell the user it's out of paper, offline, or experiencing an error? LED color codes? A small e-ink status display? Print an error message on the last bit of paper?

24. **Feedback mechanism:** How does the user tell us which stories they liked? A QR code next to each story linking to the full article? A daily "rate your newspaper" prompt in the web app? Physical tear-off "liked it / skip this topic" strips?

---

## Economics & Business Model

25. **Pricing: hardware vs. subscription split.** If the total value is ~$100/year, how do we split it? $99 device + free service? $49 device + $4.99/month? $0 device + $9.99/month? What does the target customer prefer — owning the device outright or a lower upfront cost?

26. **Paper subscription viability:** Can we sell branded paper rolls as a consumable? The razor/blade model works if margins on paper are high (they are — thermal paper is cheap). But users can buy generic rolls on Amazon for $0.50. How do we make our paper worth paying for? Premium stock? BPA-free guarantee? Pre-printed headers?

27. **Unit economics at scale:** At 10K units, what are our all-in costs (BOM + assembly + packaging + shipping + returns + support)? At 100K units? Where does profitability start?

28. **LLM API costs:** At Claude Haiku pricing, daily summarization costs <$0.01/user/day (~$3/user/year). This is negligible. But what about news API costs? NewsAPI.org is free for dev, $449/month for production. Mediastack starts at $11/month. How do we handle news sourcing at scale?

29. **International pricing:** Shipping hardware internationally is expensive and creates customs/tax complexity. Do we launch US-only? US + EU? Global from day one?

---

## Manufacturing & Supply Chain

30. **First run quantity:** Minimum order quantity for injection-molded enclosures is typically 1,000-5,000 units. Tooling costs $5,000-15,000. Can we justify this before validation? Or do we start with 3D-printed enclosures for the first 100-500 units?

31. **Assembly:** PCB assembly (SMT) is highly automated, but final assembly (putting PCB in enclosure, adding paper roll, packaging) is manual. Do we assemble in-house for the first run, or contract to a CM (contract manufacturer)?

32. **Paper supply chain:** Where do we source thermal paper rolls? Direct from Chinese manufacturers (cheap, slow, minimum orders) or from US distributors (faster, more expensive)? Do we warehouse paper or drop-ship?

33. **Reliability:** Thermal printers are mechanical devices. What's the expected lifespan of the print mechanism in daily-use cycles? 50 km of paper? 100 km? What's our warranty commitment?

34. **Certification:** Does this device need FCC certification (yes, if it has WiFi)? UL listing? CE marking for EU? What's the cost and timeline for regulatory compliance?

---

## Software Architecture

35. **Server dependency spectrum:** Where on the spectrum do we land?
    - Fully local (device fetches RSS, runs local LLM — impractical today)
    - Hybrid (device fetches content list from server, renders locally)
    - Fully cloud (server renders bitmap, pushes to device — simplest device, most dependent)

36. **Offline resilience:** If our server goes down, should the device still print *something*? A cached version of yesterday's news? A "we're having trouble, here's a puzzle" fallback? This is how we avoid the Little Printer death spiral.

37. **Content pipeline:** How do we build the content curation pipeline? RSS → filter → summarize → layout → bitmap → push. How much of this is automated vs. editorially curated? Do we ever have a human editor reviewing the AI's output?

38. **Multi-user per device:** Can a household share one device? Mom wants tech news, Dad wants sports, kids want fun facts. Does the newspaper include sections for each person? Or does each person get their own print at their own time?

39. **API/webhook for custom content:** Power users will want to push custom content to their printer (like Little Printer's publications). Do we build an API from day one? This could be the foundation of a developer community.

---

## Competition & Positioning

40. **Why not just use an e-ink tablet?** reMarkable can display news. Kindle can display articles. What's our answer to "why print when you can read on e-ink?" The answer is about ritual, tangibility, and the finite artifact — but can we articulate this compellingly in marketing?

41. **Why not just subscribe to a physical newspaper?** The NYT, WSJ, and local papers still offer home delivery. Our answer: personalization, summarization, multiple sources, beautiful small format, no pile of unread papers. But is that enough for someone who could just get the Times delivered?

42. **Competitive response:** If this takes off, what stops Amazon from building a Kindle Newspaper Printer? Or Google Nest from adding a print module? Our moat needs to be community, design, and content curation — not technology.

43. **Little Printer comparison:** Every review, every HN thread, every tweet will compare this to Little Printer. How do we honor that legacy while clearly differentiating? What's the answer to "isn't this just Little Printer again?"

---

## Edge Cases & Risks

44. **News during crisis:** During a major event (earthquake, pandemic, election), does the newspaper print a special edition? Does the summarization handle fast-moving stories well, or does it produce stale summaries?

45. **Content liability:** If the AI hallucinates a fact in a news summary and someone acts on it, what's our liability? Do we need a disclaimer on every print? ("AI-generated summaries. Verify important information.")

46. **Paper waste:** An environmentally conscious target demographic may object to daily paper consumption. How do we address this? Recyclable paper, carbon offsets, plantable paper, opt-in print schedule? What's the actual environmental footprint vs. the energy cost of screen devices?

47. **Abuse potential:** Could someone use the device to print harmful content? If we have user-generated publications or webhook APIs, what content moderation is needed?

48. **Accessibility:** Can someone with low vision read thermal print? The default font size may be too small. Do we offer a "large print" mode that reduces content density but increases readability?

49. **Power and connectivity:** What happens during a power outage or WiFi drop? Does the device have a small battery to complete a print in progress? Does it retry when connectivity returns?

50. **Sunlight and heat:** Thermal paper darkens when exposed to heat. If the device sits near a sunny window, could the paper in the tray pre-darken? Do we need to advise on placement, or design a light-shielding paper compartment?
