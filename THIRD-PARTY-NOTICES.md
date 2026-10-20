# Third-party notices

Safety Infinity uses the following open-source components. They keep their own licences; the
Safety Infinity copyright notice in LICENSE.txt does not apply to them.

| Component | Use | Licence |
|---|---|---|
| Capacitor (`@capacitor/core`, `cli`, `android`, `ios`, `app`, `status-bar`, `preferences`, `filesystem`, `share`) | Native app shell, on-device storage, file export and sharing | MIT |
| `@capacitor/assets` | Generates app icons and splash screens at build time | MIT |
| RevenueCat Capacitor SDK (`@revenuecat/purchases-capacitor`) | Subscription purchases and status through the App Store and Google Play | MIT |
| Capacitor Firebase plugins by Capawesome (`@capacitor-firebase/authentication`, `firestore`, `functions`, `messaging`) and the Firebase iOS and Android SDKs (Google) | Accounts, phone and email verification, My Work and team workspace sync, push alerts | Apache 2.0 |
| `@capacitor-community/text-to-speech` | Reading aloud with the phone's own voices | MIT |
| `@capgo/capacitor-printer` (Capgo) | Printing documents, certificates and posters | Mozilla Public License 2.0 — used unmodified; its source is available at https://github.com/Cap-go/capacitor-printer |
| libphonenumber-js (with Google libphonenumber metadata) | Phone numbers from every country: validation and formatting | MIT (metadata Apache 2.0) |
| firebase-admin, firebase-functions, `@google-cloud/text-to-speech`, `@google-cloud/translate` (server side, in `firebase/functions`) | Account, workspace, alert, voice and translation functions | Apache 2.0 |
| esbuild | Bundles the native bridge at build time (not shipped as source) | MIT |
| Barlow and Barlow Condensed (Jeremy Tribby), via `@fontsource` | App typefaces | SIL Open Font License 1.1 |
| IBM Plex Mono and IBM Plex Sans Arabic (IBM), via `@fontsource` | Code references and Arabic text | SIL Open Font License 1.1 |
| Noto Nastaliq Urdu and Noto Sans Devanagari (Google), via `@fontsource` | Urdu and Hindi certificates and posters | SIL Open Font License 1.1 |

The font files are downloaded by `npm install` and copied into the app by `npm run build`.
The SIL Open Font License allows the fonts to be bundled with the app; the licence text is
included in each `@fontsource` package. The bundled `www/native.js` keeps the licence
comments of the packages it contains.

The app icon, the shield-and-infinity logo, the 3D interface icons, the PPE illustrations, the
alert and tap sounds, certificate and poster designs and the narrated tour are original works by
Daniyal Khalid and are covered by LICENSE.txt. The natural read-aloud voice is generated on request
by Google Cloud Text-to-Speech.
