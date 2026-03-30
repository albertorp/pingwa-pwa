# PingWA

**PingWA** is a lightweight Progressive Web App (PWA) that lets you start a WhatsApp conversation with any phone number — without having to save it to your contacts first.

## What it does

WhatsApp normally requires a number to be in your phone's Contacts before you can message it. PingWA works around this by generating a `wa.me` deep link from a number you type or paste, and opening WhatsApp directly to a new conversation with that number.

## Features

- Accepts any phone number in international format (with or without the leading `+`, with or without spaces)
- Validates input and rejects anything that isn't a valid phone number (digits, spaces, and an optional `+` only)
- Opens WhatsApp in a new tab so you don't lose your place
- Installable as a PWA on iOS and Android — works like a native app from your home screen
- No data is stored, transmitted, or logged anywhere by the app itself
- Analytics via [PostHog](https://posthog.com) (pageviews and sessions only)

## How to use

1. Open the app at [albertorp.github.io/pingwa-pwa](https://albertorp.github.io/pingwa-pwa/)
2. Type or paste a phone number in international format, e.g. `+1 555 123 4567`
3. Tap **Go**
4. WhatsApp opens in a new tab ready to send a message to that number

## Installing on your home screen

### iOS (Safari)
1. Open the app in Safari
2. Tap the **Share** button (box with arrow)
3. Tap **Add to Home Screen**
4. Confirm the name and tap **Add**

### Android (Chrome)
1. Open the app in Chrome
2. Chrome will show an **Install app** banner automatically, or tap the menu (⋮) and select **Add to Home screen**
3. Tap **Install**

Once installed, PingWA launches in standalone mode (no browser chrome) and behaves like a native app.

## Phone number format

PingWA expects a full international number. Examples of accepted formats:

| Input | Interpreted as |
|---|---|
| `+34 612 345 678` | `+34612345678` |
| `34612345678` | `34612345678` |
| `+1 555 123 4567` | `+15551234567` |
| `15551234567` | `15551234567` |

If the input contains any characters other than digits, spaces, or a leading `+`, the app will show an error rather than opening WhatsApp with an invalid number.

## Tech stack

- Plain HTML + CSS + JavaScript — no framework, no build step
- [Tailwind CSS](https://tailwindcss.com) via CDN for styling
- [PostHog](https://posthog.com) for privacy-friendly analytics
- Hosted on [GitHub Pages](https://pages.github.com)
- PWA manifest + service worker for home screen installability

## Project structure

```
pingwa-pwa/
├── index.html            # The entire app
├── manifest.json         # PWA manifest (name, icon, theme color)
├── sw.js                 # Minimal service worker (enables PWA install)
├── pingwa_logo.png       # Logo shown in the app
└── Icon-App-1024x1024@1x.png  # Icon used for home screen and manifest
```
