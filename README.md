# Arabic Scrabble (PWA)

Installable Arabic Scrabble game. Static site: plain HTML/CSS/JS, no build step.
Installs as a standalone app from Chrome and works offline after the first visit
(Firebase-backed features need a connection).

## Files

| File | Purpose |
| --- | --- |
| `index.html` | The whole game |
| `manifest.webmanifest` | PWA manifest (name, icons, standalone display) |
| `sw.js` | Service worker (offline cache) |
| `icons/` | App icons: 192/512 PNG, maskable 192/512, Apple touch icon, SVG |
| `favicon.ico` | Browser tab icon |
| `vercel.json` | Headers so the service worker and manifest update and load correctly |
| `pwa-check.html` | Diagnostic page: shows why a device can or cannot install the app |
| `icon-maker.html` | Optional tool that redraws the ض icons in a font you choose |

All paths are relative, so the app also works from a sub-folder.

## Game display notes

- **Grey dark mode:** the sun/moon button switches themes (grey board, grey panels); the choice is remembered on the device.
- **Any screen:** layout adapts to phones (portrait and landscape, notch/home-indicator safe areas),
  tablets, desktops and TVs. On big screens the board and cards scale up automatically.
- **Full screen when installed:** the manifest requests `fullscreen`, and the first tap in an installed app
  requests fullscreen too (hides status/navigation bars). **iPhone/iPad:** iOS does not let web apps hide the
  status bar; the game draws underneath it (`black-translucent`) so it blends in. iPhone Safari has no
  Fullscreen API, so the fullscreen button is hidden there.
- **Tile letters** are centered by measuring each glyph's real ink box, so they stay dead center
  at any board size and with any font. Scores stay in the tile corner.

- **No dialog text:** the in-game status messages are switched off. To bring them back, set
  `window.SHOW_STATUS_TEXT = true` and remove the `#status-message { display: none }` rule in `index.html`.

- **Vibration feedback (no sound):** a light vibration tick on every touch press of a button or tile and when a
  tile is placed, moved or returned. Android Chrome vibrates; iPhone/iPad Safari has no vibration API, so a
  best-effort iOS 18+ haptic is used there. Mouse users get nothing.
- **Remembers your game:** the running game is saved on the device. When the app is reopened it asks whether to
  continue the last game or begin a new one. Finished games are not offered again.

## Deploy: GitHub, then Vercel

```bash
git init
git add .
git commit -m "Arabic Scrabble PWA"
git branch -M main
git remote add origin https://github.com/<your-user>/<your-repo>.git
git push -u origin main
```

1. Go to vercel.com, **Add New, Project**, and import the GitHub repo.
2. **Framework Preset: Other.** Leave Build Command and Output Directory empty.
3. Deploy. Every push to `main` redeploys automatically.
4. Use the **production URL** (the plain `*.vercel.app` or your own domain), not a
   per-deployment preview link. Preview links can be behind Vercel login, which stops
   Chrome from reading the manifest and installing the app.

If the game uses Firebase Authentication, add your Vercel domain under
Firebase console, Authentication, Settings, **Authorized domains**.

## Check that it is installable

1. Open `https://<your-site>/pwa-check.html` in Chrome on the target device. It should show
   "Chrome says this app IS installable" and an **Install app now** button.
2. Chrome menu, **Add to Home screen**, then choose **Install** (not "Create shortcut").
   An installed app opens without the address bar.
3. Desktop: DevTools, Application, Manifest and Service Workers; or Lighthouse, PWA.

## Shipping updates

Change `CACHE_VERSION` at the top of `sw.js` (for example `v5` to `v6`) every time you
change `index.html` or icons, so installed copies refresh.

## Local testing

```bash
python3 -m http.server 8080     # or: npx serve .
```
Open http://localhost:8080 (localhost counts as secure for service workers).

## Troubleshooting

- **Install only creates a shortcut:** Chrome could not finish building the app on that
  device. Update Google Play services, Play Store and Chrome, make sure "Desktop site" is
  off, and open the production URL in normal Chrome (not Incognito or an in-app browser).
  Firefox for Android (menu, Install) is an alternative.
- **Old version still showing:** bump `CACHE_VERSION`, redeploy, then reload twice.
- **Different icon letter or font:** open `icon-maker.html`, pick a font, download the five
  PNGs, put them in `icons/` with the same names, bump `CACHE_VERSION`.
