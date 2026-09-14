# Dayframe — installable build

A Progressive Web App: installs to the home screen, runs full-screen, works offline,
and keeps its vault encrypted on the device.

## Files

- `index.html` — the whole app in one file (React + design system inlined)
- `manifest.webmanifest` — name, icons, standalone display, theme colour
- `sw.js` — service worker: caches the shell, so it opens with no network
- `icon-192.png`, `icon-512.png`, `apple-touch-icon.png` — home-screen icons

## Install it

**iPhone / iPad (Safari)**
1. Put this folder somewhere reachable over HTTPS (see hosting below).
2. Open the URL in Safari.
3. Share → **Add to Home Screen** → Add.

**Android (Chrome)**
1. Open the URL.
2. Chrome shows *Install app* — or menu → **Install app**.

**Offline-only, no hosting:** AirDrop/email `index.html` to the phone and open it in
Safari, then Add to Home Screen. It still runs and still encrypts; the service worker
just won't register from `file://`, so there's no install banner.

## Hosting (any static host, one drag-and-drop)

Netlify Drop, Cloudflare Pages, GitHub Pages or Vercel — upload this folder as-is.
Requirements: HTTPS, and `sw.js` served from the same folder as `index.html`.
No build step, no server code, no database.

## Data & security

- A random AES-256-GCM data key is generated on the device at sign-up.
- The key is wrapped twice — under your passphrase and under a one-time recovery
  code — with PBKDF2-SHA256, 310,000 iterations. The raw key is never stored.
- Only ciphertext is written to storage (`dayframe.vault.v3`); the account record
  (`dayframe.account.v2`) holds the wrapped key and salts, never the passphrase.
- Auto-lock discards the key from memory after 2 minutes idle.
- No network calls of any kind. The service worker caches only the app shell.

Each install is its own vault: an account made on the phone is separate from one made
on a laptop. Use **Account & security → Export JSON** for a copy you control.

## Going native

For an App Store build, reimplement the same screens in SwiftUI/React Native and swap
two pieces: the wrapped key moves to the iOS Keychain behind Face ID
(`kSecAttrAccessibleWhenUnlockedThisDeviceOnly`), and the vault moves to a
sandbox SQLite/Core Data file. The crypto design above carries over unchanged.
