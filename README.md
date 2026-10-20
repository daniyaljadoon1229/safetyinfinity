# Safety Infinity 4.0 — store app project

© 2026 Daniyal Khalid. All rights reserved.

This folder is the complete Android + iPhone project for Safety Infinity (formerly Master Safety and SafeClause), with
Safety Infinity accounts (username linked to a verified email and a verified mobile number from any
country), shared company team workspaces (admin, editor and viewer roles) with incident and NCR push alerts,
attachments, printing, certificates and notice-board posters, a read-aloud voice in four languages, an Arabic
interface, and the Safety Infinity Pro subscription (15 SAR a month or 85 SAR a year). **Start with `PUBLISHING-GUIDE.md`** — it walks
through every step to the Google Play Store and the Apple App Store, without needing a Mac.

| Folder / file | What it is |
|---|---|
| `www/` | The app itself (works offline). `config.js` holds the updates-file address and the subscription settings. `data.js` holds the library, 98 document templates, toolkit, PPE guide, KPI library, exams, animations and the Arabic interface dictionary. |
| `src/native.js` | Loads Capacitor and the store plugins (files, sharing, storage, Firebase accounts and push messaging, RevenueCat, text-to-speech, printing) and the phone-number library; bundled into `www/native.js` at build time. |
| `firebase/` | The back end: Cloud Functions (usernames, sign-in with username or phone, account deletion, team workspaces and roles, incident and NCR alerts, natural voice and translation), Firestore security rules (private My Work and attachments; workspace records and attachments readable by members and writable by admins and editors), and the place for your `google-services.json` and `GoogleService-Info.plist`. |
| `assets/` | Icon and splash artwork (turned into every required size during the build) and the eight alert sounds. |
| `capacitor.config.json` | App ID `com.daniyalkhalid.mastersafety` (unchanged since 3.0), name, colours, push presentation. |
| `codemagic.yaml` | Cloud build recipes for Android (.aab) and iPhone (to TestFlight). |
| `scripts/` | Build helpers: prepare `www` (fonts, native bridge, config check), create and configure the native projects (Firebase files, alert sounds, notification icon, push entitlement, text-to-speech access). |
| `website/` | Support page, terms of use, privacy policy, account deletion page and `updates.json` for GitHub Pages. |
| `store-assets/` | Store icon, Play feature graphic, and screenshots for both stores (including the subscription screen). |
| `store-listing/STORE-LISTING.md` | All store text, subscription product IDs and form answers, ready to paste (`fields.json` has the same texts). |

Quick commands (on your own computer, Node.js 22+):

```bash
npm install
npm run build             # fonts + native bridge + config check
npm run setup:android     # or: npm run setup:ios  (iOS needs a Mac with Xcode 26)
npm run open:android      # opens Android Studio to build a signed bundle
```

The Android upload key is **not** in this folder on purpose. Keep it separate and private.
