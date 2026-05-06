---
title: "Maledictorian"
description: "Story-driven prototype game built in Unity, recieved $25,000 grant from IGDA Foundation"
date: "Apr 10 2021"
demoURL: "https://maledictorian.netlify.app"
---
## Features

- **YouTube Search** — Search videos via the Innertube client with automatic deduplication
- **DASH Video Playback** — Adaptive streaming via dash.js with full seeking support
- **Custom Video Controls** — Play/pause, ±10s seek, volume slider, 8 playback speeds (0.25×–2×), Picture-in-Picture, fullscreen, with auto-hide
- **Media Session API** — OS-level media controls from the system tray and lock screen
- **SponsorBlock** — Privacy-preserving hash-prefix lookup; auto-skips sponsors, self-promo, intros, outros, and more with colored progress bar markers
- **Channel Subscriptions** — Subscribe/unsubscribe with a reactive local database
- **Subscription Feed** — Home page aggregates the latest videos from all subscribed channels, sorted by date, with persistent caching
- **Encrypted Local Database** — RxDB with CryptoJS encryption on sensitive fields (channel IDs, names, thumbnails)
- **Cloud Sync** — Appwrite-powered authentication with bidirectional subscription sync and avatar upload
- **Invidious Companion Sidecar** — Bundled companion binary auto-launches on startup with health checking
- **Stream Proxy** — Custom `stream://` URI scheme in Rust for proxying video streams with full Range header support
- **Everforest Theme** — A beautiful custom color scheme built on daisyUI
- **3D Hover Effects** — Video cards with CSS 3D transforms

## Tech Stack

| Layer | Technology |
| --- | --- |
| Desktop Framework | [Tauri 2](https://tauri.app/) (Rust) |
| Frontend | [React 19](https://react.dev/) + [TypeScript 5.8](https://www.typescriptlang.org/) |
| Build Tool | [Vite 7](https://vite.dev/) |
| Routing | [React Router 7](https://reactrouter.com/) |
| UI | [daisyUI 5](https://daisyui.com/) + [Tailwind CSS 4](https://tailwindcss.com/) |
| Video Player | [dash.js 5](https://github.com/Dash-Industry-Forum/dash.js) |
| YouTube API | [youtubei.js 17](https://github.com/LuanRT/YouTube.js) |
| Local Database | [RxDB 17](https://rxdb.info/) + IndexedDB + [CryptoJS](https://github.com/brix/crypto-js) |
| Cloud Backend | [Appwrite](https://appwrite.io/) |
| Companion Server | [Deno](https://deno.com/) + [Hono](https://hono.dev/) |