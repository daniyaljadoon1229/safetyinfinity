# Safety Infinity 4.0 — publishing guide

From this package to the Google Play Store and the Apple App Store, with Safety Infinity accounts (username linked to a verified email and a verified mobile number from any country), shared company team workspaces with incident and NCR alerts, a natural female read-aloud voice, and the Safety Infinity Pro subscription (15 SAR a month or 85 SAR a year). Written for someone who has never published an app. You do **not** need a Mac or any programming tools: the apps are built in the cloud by Codemagic, and the account back end is deployed from Google Cloud Shell in your browser.

© 2026 Daniyal Khalid. All rights reserved.

> **New name, same IDs.** The app is now called **Safety Infinity** (store name `Safety Infinity: HSE Pro`). Only the names people see have changed. The technical IDs stay exactly as in 3.0, so nothing you already created has to be redone: app ID / bundle ID `com.daniyalkhalid.mastersafety`, subscription products `mastersafety_pro_monthly`, `mastersafety_pro_yearly` and `mastersafety_pro`, and the Codemagic names `Master Safety ASC key`, `mastersafety_upload`, `mastersafety_dist` and `mastersafety_appstore`. Customers never see these IDs.
>
> **Already followed the 3.0 guide?** Do only these steps: rename the display names (Steps 3, 4, 8, 9), the website repository (Step 1.6), switch on **Push Notifications** for the App ID and regenerate the profile (Steps 3.1 and 11.4–11.5), upload the **APNs key** (Step 6.5), enable the two new Google Cloud APIs (Step 6.4), **deploy the functions and rules again** (Step 7), then build, test (Step 14) and submit with the new texts, screenshots and privacy answers (Steps 15 and 16).

---

## What you need

| Item | Cost | Notes |
|---|---|---|
| Google Play developer account | US$25, one time | ID verification can take a few days |
| Google payments profile (merchant) | Free | Needed to sell subscriptions; add your bank account |
| Apple Developer Program | US$99 per year | Approval usually within a few days |
| Apple Paid Apps agreement, tax and bank details | Free | Needed to sell subscriptions |
| Firebase project on the **Blaze** (pay-as-you-go) plan | Pay per use | Accounts, team workspaces, attachments and alerts. SMS codes are charged per text by country (roughly US$0.01–0.10 each). Email, sign-in, push alerts, functions and the database stay within the free allowance for a small app; a large team workspace with many photo attachments reads and stores more. Set a budget alert. |
| Google Cloud Text-to-Speech and Translation (same project) | Pay per use | The natural female voice: first 1 million characters a month free, then US$30 per million. Translation of library pages: first 500,000 characters a month free, then US$20 per million. Each account gets 100,000 voice and 200,000 translation characters a month (about 25 standards read aloud); after that the app uses the phone's own voice, which is free. Translated pages are cached and shared, so each page is paid for once. |
| RevenueCat account | Free up to US$2,500 a month in subscription revenue, then 1% | Checks and manages subscriptions on both stores |
| GitHub account | Free | Holds the app project and hosts the website |
| Codemagic account | Free | 500 Mac build minutes a month on a personal account; one build takes about 10–20 minutes |
| A computer with Chrome or Edge | — | For uploading files and filling in the forms |
| At least 12 testers with Google accounts | — | Google requires a 14-day closed test for new personal accounts |

**Realistic timeline:** Apple about 1–2 weeks once your account and agreements are approved. Google about 3 weeks because of the 14-day test.

**Two files are kept separate from the app project on purpose:** `safeclause-upload.jks` and `KEY-DETAILS-KEEP-PRIVATE.txt`. This is your Android upload key (its file name still says SafeClause; that doesn't matter and must not be changed). Never upload it to GitHub or share it. The key details file also lists the key's SHA-1 and SHA-256 fingerprints, which Firebase needs (fingerprints are not secret).

**How it works for your users:** they download the app free, create an account (username, email, mobile number from any country, password), enter the SMS code and confirm the email link, then choose monthly or yearly. A short narrated app tour plays once after the first sign-in. Afterwards they sign in with their **username, email or mobile number** and password on any phone, and their subscription and My Work come with them. They can switch the app between English and Arabic, pick a colour theme and Auto, Day or Night mode, and choose the tap and alert sounds in **Settings**. They can delete the account in the app at any time.

**Team workspaces:** in the **Company** tab, one person creates the company workspace and becomes its **admin**. The admin adds colleagues by their Safety Infinity username as **editors** (add and change records) or **viewers** (read and export). Everyone in the workspace sees the same projects, weekly and monthly reports, incidents, NCRs, trainings, inductions, drills, audits, equipment certificates, employee of the month, notice-board posters, KPIs and reports, with their attachments. When someone reports an incident or an NCR, the other members' phones get an alert with a different sound for each level (critical, recordable, first aid, near miss, damage, NCR). Each member needs their own account and subscription.

---

## Part A — Accounts, website and project

### Step 1. Put the website and the app project on GitHub

1. Create a free account at **github.com**. Your username becomes part of your website address.
2. Create a repository named **`safetyinfinity`**, set to **Public**. Choose **Add file → Upload files** and drag in everything inside the `website` folder (`index.html`, `terms.html`, `privacy.html`, `delete-account.html`, `updates.json`, `_style.css`, `.nojekyll`). Commit.
3. In that repository go to **Settings → Pages**. Under *Build and deployment* choose **Deploy from a branch**, branch **main**, folder **/ (root)**, then **Save**. After a minute or two your site is live at `https://YOUR-GITHUB-USERNAME.github.io/safetyinfinity/`. Open `…/terms.html` and `…/updates.json` to check they work.
4. In the `safetyinfinity` repository, edit `index.html`, `terms.html`, `privacy.html` and `delete-account.html` (pencil icon) and replace `YOUR-EMAIL@example.com` with your support email.
5. Create a second repository named **`safetyinfinity-app`**, set to **Private**. Upload everything in this package **except** the upload key files. The repository root must contain `package.json` and `codemagic.yaml`. You add the Firebase files in Part B and finish `www/config.js` in Part C.
6. **Coming from 3.0:** open your old `mastersafety` repository → **Settings → General → Repository name**, rename it to `safetyinfinity`, then upload the new website files over the old ones. Rename `mastersafety-app` to `safetyinfinity-app` the same way (Codemagic follows the rename), delete its old files and upload this package. Keep your `firebase/google-services.json` and `firebase/GoogleService-Info.plist`, and your filled-in RevenueCat keys in `www/config.js`.

### Step 2. Open the developer accounts

- **Google Play:** go to **play.google.com/console**, choose a **personal** account, pay US$25, and complete identity verification. Then **Setup → Payments profile**: create or link a payments profile and add your bank account, so you can sell subscriptions.
- **Apple:** go to **developer.apple.com/programs/enroll**, sign in with an Apple Account that has two-factor authentication on, enrol as an **Individual**, and pay US$99. Then in **App Store Connect → Business** (Agreements, Tax, and Banking): accept the **Paid Apps** agreement and fill in bank and tax details. Subscriptions do not load in the app until this shows **Active**.
- **Recommended:** apply for the **App Store Small Business Program** (developer.apple.com/app-store/small-business-program). It lowers Apple's commission to 15% from the first day of each subscription.

On an individual Apple account, the App Store shows your **legal name** (as on your ID) as the seller. The app itself credits "Daniyal Khalid" as author and owner.

### Step 3. Register the app with Apple

1. **developer.apple.com → Certificates, Identifiers & Profiles → Identifiers → +** → *App IDs* → *App*. Description `Safety Infinity`, Bundle ID **Explicit** `com.daniyalkhalid.mastersafety`. Under **Capabilities** tick **Push Notifications** (needed for incident and NCR alerts). In-App Purchase is switched on automatically. Register. *Coming from 3.0:* open the existing identifier, tick **Push Notifications**, change the description to `Safety Infinity`, and **Save**.
2. **appstoreconnect.apple.com → Apps → + → New App**: platform iOS, name `Safety Infinity: HSE Pro`, primary language English, bundle ID `com.daniyalkhalid.mastersafety`, SKU `mastersafety-ios`. *Coming from 3.0:* the name is changed on the version page in Step 16 instead.
3. Create an API key for Codemagic: **Users and Access → Integrations → App Store Connect API → +**. Name `Codemagic`, access **App Manager**, **Generate**, then **Download API Key** (a `.p8` file; you can download it only once). Write down the **Issuer ID** and **Key ID**.
4. Create an In-App Purchase key for RevenueCat: **Users and Access → Integrations → In-App Purchase → +** (Generate In-App Purchase Key). Name it `RevenueCat`, download the `.p8` file and note its **Key ID** and the **Issuer ID**.
5. Create a push key for Firebase: **developer.apple.com → Certificates, Identifiers & Profiles → Keys → +**. Name `Safety Infinity push`, tick **Apple Push Notifications service (APNs)**, choose environment **Sandbox & Production**, **Continue → Register → Download** (a `.p8` file; once only). Note its **Key ID** and your **Team ID** (top right of the developer site). You upload it in Step 6.5.

### Step 4. Register the app with Google

**Play Console → Create app**: name `Safety Infinity: HSE Pro`, default language English, type **App**, **Free** (the subscription is sold inside the app). Accept the declarations. *Coming from 3.0:* the name is changed in the store listing in Step 15.

---

## Part B — Set up accounts, alerts and voice (Firebase)

### Step 5. Create the Firebase project

1. Go to **console.firebase.google.com → Create a project**. Name it `Safety Infinity`. You can switch **Google Analytics off** (the app doesn't use it). Note the **Project ID** shown under the name (for example `safety-infinity-12ab3`). *Coming from 3.0:* keep your existing project; its name doesn't matter.
2. Bottom left, **Upgrade** to the **Blaze** plan and link a billing account. SMS codes, Cloud Functions, the voice and translation need it. Then in **console.cloud.google.com → Billing → Budgets & alerts**, create a budget (for example US$20 a month) with email alerts.
3. **Build → Authentication → Get started → Sign-in method:**
   - Enable **Email/Password** (leave "Email link" off).
   - Enable **Phone**. Under **Phone numbers for testing** add two fictional numbers with fixed codes, for example `+1 650-555-1234` → `123456` and `+1 650-555-5678` → `654321`. These never receive an SMS; you use them for the store-review demo accounts.
4. **Authentication → Settings:**
   - **SMS region policy:** leave it on **Allow** for all regions, so numbers from every country work. (If you ever see SMS abuse from one region, deny just that region.)
   - **User actions:** keep **Email enumeration protection** on.
5. **Authentication → Templates:** for *Email address verification* and *Password reset*, set the sender name to `Safety Infinity` and the reply-to address to your support email.
6. **Build → Firestore Database → Create database:** choose **Production mode** and a location near your users (it can't be changed later). Click **Enable**. Write down the location: if it is not a US location (`nam5` or `us-central1`), see the note in Step 7.
7. **Project settings (gear) → General → Web API key:** copy it; you need it in Step 7.

### Step 6. Register the apps, add the config files, switch on alerts and voice

1. **Project settings → General → Your apps → Add app → Android.** Package name `com.daniyalkhalid.mastersafety`, nickname `Safety Infinity Android`. Under **SHA certificate fingerprints**, add the **SHA-1** and **SHA-256** from `KEY-DETAILS-KEEP-PRIVATE.txt` (your upload key). Download **`google-services.json`**.
2. **Add app → Apple (iOS+).** Bundle ID `com.daniyalkhalid.mastersafety`, nickname `Safety Infinity iPhone`. Download **`GoogleService-Info.plist`**.
3. In the `safetyinfinity-app` repository, open the **`firebase`** folder → **Add file → Upload files** → upload both files → Commit. The build copies them into the Android and iPhone projects, registers the plist with Xcode and adds the URL scheme phone sign-in needs.
4. **Google Cloud console** (console.cloud.google.com, same project) → **APIs & Services → Library**: search for and **Enable** each of these:
   - **Play Integrity API** (Android phone verification)
   - **IAM Service Account Credentials API** (username and phone sign-in)
   - **Cloud Text-to-Speech API** (the natural female voice in English, Arabic, Urdu and Hindi)
   - **Cloud Translation API** (translating library pages into Arabic, Urdu and Hindi before reading them aloud)
5. **Push alerts on iPhone (required):** Firebase **Project settings → Cloud Messaging → Apple app configuration → APNs Authentication Key → Upload**: choose the `.p8` push key from Step 3.5 and enter its **Key ID** and your **Team ID**. This also makes iPhone phone verification smoother. Android needs nothing extra: Firebase Cloud Messaging is on by default.

### Step 7. Deploy the back end from Cloud Shell (in your browser)

The `firebase` folder holds the back-end functions and the database security rules:

- **Accounts:** unique usernames, sign-in with username or phone, account deletion.
- **Team workspaces:** create a workspace; add and remove members; change roles; rename; delete.
- **Alerts:** registering each phone for alerts, incident and NCR alerts by level, test alert.
- **Voice and translation:** the natural voice and page translation, with a monthly allowance per account.

The rules let only workspace members read company records and their attachments, only admins and editors change them, and only admins change the company profile. My Work records and attachments stay private to their owner.

1. On your computer, zip the **`firebase`** folder from this package (Windows: right-click → *Send to → Compressed (zipped) folder*; Mac: right-click → *Compress*). Name it `firebase.zip`.
2. Open **console.cloud.google.com**, select your Firebase project at the top, and click the **Cloud Shell** icon `>_` (top right). Wait for the terminal to start.
3. In the Cloud Shell menu (⋮) choose **Upload** and pick `firebase.zip`. (If an older `firebase` folder is there from 3.0, run `rm -rf firebase` first.)
4. Paste these commands one at a time. Replace `your-project-id` with your Project ID and `PASTE-WEB-API-KEY` with the key from Step 5:

```bash
unzip -o firebase.zip && cd firebase
sed -i "s/YOUR-FIREBASE-PROJECT-ID/your-project-id/" .firebaserc
echo "WEB_API_KEY=PASTE-WEB-API-KEY" > functions/.env
(cd functions && npm install)
npx firebase-tools@latest login --no-localhost
npx firebase-tools@latest deploy --only functions,firestore:rules --project your-project-id
```

   The login command shows a link: open it, choose your Google account, and copy the code back into Cloud Shell. The deploy takes a few minutes and ends with **Deploy complete!**. If it asks to enable APIs (Cloud Functions, Cloud Build, Artifact Registry, Eventarc, Pub/Sub), answer **Y**. The first deploy of the alert functions sometimes fails with a permissions message because Eventarc is still being set up: wait two minutes and run the last command again.

   **Database outside the US?** The incident and NCR alert functions must run in the database's region. If your Firestore location in Step 5.6 is not `nam5` or `us-central1`, add its region before deploying, for example `echo "TRIGGER_REGION=europe-west1" >> functions/.env` for `eur3` or `europe-west1`. For any other single region, use that region's name as shown in Firestore → Settings.

5. Allow the functions to create sign-in tokens (needed for username and phone sign-in). Still in Cloud Shell:

```bash
PROJECT=your-project-id
NUMBER=$(gcloud projects describe $PROJECT --format="value(projectNumber)")
gcloud projects add-iam-policy-binding $PROJECT \
  --member="serviceAccount:${NUMBER}-compute@developer.gserviceaccount.com" \
  --role="roles/iam.serviceAccountTokenCreator"
```

You only repeat Step 7 when the functions or rules change. **Coming from 3.0, deploy again:** 4.0 adds the alert, voice and translation functions and new rules for attachments, employee of the month and notice-board posters. The callable functions run in `us-central1`; if you change `REGION` in `firebase/functions/index.js`, change `functionsRegion` in `www/config.js` to match.

---

## Part C — Set up the subscription

### Step 8. Create the two plans in App Store Connect

**Your app → Monetization → Subscriptions:**

1. **Create subscription group**: reference name `Safety Infinity Pro`. Add an App Store localization: display name `Safety Infinity Pro`.
2. In the group, **Create** two subscriptions:

| Reference name | Product ID | Duration | Price (Saudi Arabia) |
|---|---|---|---|
| Safety Infinity Pro Monthly | `mastersafety_pro_monthly` | 1 month | 15.00 SAR, or the nearest Apple lists |
| Safety Infinity Pro Yearly | `mastersafety_pro_yearly` | 1 year | 85.00 SAR, or the nearest Apple lists |

3. For each: **Availability** all countries; **Subscription Prices → Add** → choose **Saudi Arabia** as the base, pick the price, accept Apple's prices for other countries. Add a localization (display name `Safety Infinity Pro Monthly` / `Safety Infinity Pro Yearly`, description `All Safety Infinity features, billed monthly` / `… billed yearly`). Upload the review screenshot `store-assets/review-paywall.png`.
4. Keep both plans on the **same level** in the group (they give the same features). People can switch between them in their Apple account settings.

*Coming from 3.0:* keep the product IDs; edit only the group and subscription **display names and descriptions** (localizations) to say Safety Infinity, and upload the new review screenshot.

Both should show **Ready to Submit** (or *Waiting for Review* after a change). They are reviewed together with the app version in Step 16.

### Step 9. Create RevenueCat and get the app keys

1. Sign up at **app.revenuecat.com** and create a project named **Safety Infinity** (*coming from 3.0:* rename it under Project settings, or leave it; customers never see it).
2. **Add app → App Store**: bundle ID `com.daniyalkhalid.mastersafety`; upload the **In-App Purchase key** `.p8` from Step 3 with its Key ID and Issuer ID.
3. **Add app → Play Store**: package name `com.daniyalkhalid.mastersafety`. For the service account credentials, follow RevenueCat's guide *"Creating Play Service Credentials"* (Google Cloud service account, invite it in Play Console **Users and permissions** with the financial and order permissions RevenueCat lists, upload its JSON key to RevenueCat). New credentials can take up to 36 hours to work.
4. **Product catalog → Entitlements → New**: identifier **`pro`**, description `Safety Infinity Pro`.
5. **Product catalog → Products**: add `mastersafety_pro_monthly` and `mastersafety_pro_yearly` for the App Store app (you add the Google products in Step 12). Attach both to the `pro` entitlement.
6. **Product catalog → Offerings → New**: identifier **`default`**, make it **Current**. Add two packages: **Monthly** (`$rc_monthly`) with `mastersafety_pro_monthly`, and **Annual** (`$rc_annual`) with `mastersafety_pro_yearly`.
7. **Project settings → API keys**: copy the **public app-specific** keys. The Apple key starts `appl_`, the Google key starts `goog_`.

The app identifies each subscriber by their Safety Infinity account ID, so a subscription bought on one phone unlocks the app on any phone where the same account signs in.

### Step 10. Fill in `www/config.js`

In the `safetyinfinity-app` repository open `www/config.js` → pencil, and replace:

- `YOUR-GITHUB-USERNAME` (three places) with your GitHub username,
- `appl_YOUR-REVENUECAT-IOS-KEY` with your Apple key from Step 9,
- `goog_YOUR-REVENUECAT-ANDROID-KEY` with your Google key from Step 9.

Commit. The build **stops with an error** while anything still says `YOUR-`, or while a Firebase file is missing from the `firebase` folder, so an incomplete build can't reach the stores by accident.

---

## Part D — Build and test

### Step 11. Set up Codemagic (cloud builds)

1. Sign up at **codemagic.io** with your GitHub account. Use your **personal** account; the free minutes don't apply to Teams.
2. **Add application** → GitHub → choose `safetyinfinity-app`. Codemagic finds `codemagic.yaml` automatically.
3. In Codemagic settings (these internal names keep the 3.0 spelling on purpose; type them exactly):
   - **Integrations → Developer Portal → Manage keys → Add key.** Name it exactly **`Master Safety ASC key`**, then enter the Issuer ID and Key ID and choose the Codemagic `.p8` file from Step 3.
   - **codemagic.yaml settings → Code signing identities → Android keystores.** Upload `safeclause-upload.jks` and enter the password and alias from `KEY-DETAILS-KEEP-PRIVATE.txt` (the key password is the same as the keystore password). Reference name **`mastersafety_upload`**.
   - **Code signing identities → iOS certificates → Generate certificate.** Type **Apple Distribution**, choose your API key, create it, **download it immediately** and keep its password. Then **Upload certificate** with that file, reference name `mastersafety_dist`.
4. On **developer.apple.com → Profiles → +** → *Distribution: App Store Connect*. Choose the App ID `com.daniyalkhalid.mastersafety` and the certificate Codemagic created, name it `Safety Infinity App Store`, generate. **The profile must be created (or regenerated) after Push Notifications was ticked in Step 3.1**, otherwise alerts never reach iPhones. *Coming from 3.0:* open your old profile → **Edit → Save** to regenerate it with push included.
5. In Codemagic: **Code signing identities → iOS provisioning profiles → Fetch profiles**. Select the new profile, reference name `mastersafety_appstore`, **Download selected** (delete the old 3.0 profile there first if it is listed).

### Step 12. Android build, Firebase fingerprints, Google subscription

1. Codemagic → `safetyinfinity-app` → **Start new build** → **Safety Infinity — Android (Google Play)**. Download the **`.aab`** under *Artifacts*.
2. Play Console → **Test and release → Testing → Internal testing → Create release** → upload the `.aab` → accept **Play App Signing** → release name `4.0.0` → save and roll out to yourself.
3. **Play Console → Test and release → App integrity → App signing:** copy the **App signing key certificate** SHA-1 and SHA-256 and add them in Firebase **Project settings → your Android app → Add fingerprint**. Google re-signs the app, so phone verification needs these too.
4. **Monetize with Play → Products → Subscriptions → Create subscription**: product ID **`mastersafety_pro`**, name **Safety Infinity Pro**, add the benefits from `STORE-LISTING.md`. Google only lets you create subscriptions after a build with billing is uploaded, which you just did. *Coming from 3.0:* open the existing subscription and change only its name and benefits.
5. **Add base plan** twice:
   - ID **`monthly`**, auto-renewing, billing period **1 month**, Saudi Arabia price **15.00 SAR**, then *Update exchange rates* for other countries. **Activate.**
   - ID **`yearly`**, auto-renewing, billing period **1 year**, Saudi Arabia price **85.00 SAR**, other countries by exchange rate. **Activate.**
6. In RevenueCat → **Products**, add the Play products `mastersafety_pro:monthly` and `mastersafety_pro:yearly`, attach both to `pro`, and add them to the **Monthly** and **Annual** packages of the `default` offering.
7. Play Console → **Setup → License testing**: add your Gmail and testers so test purchases are free.

### Step 13. iPhone build

**Start new build** → **Safety Infinity — iPhone (App Store)**. The build uploads itself to App Store Connect and appears under **TestFlight** after processing (you get an email).

The first build of each workflow is the first real compile of the project. If a build fails, copy the error from the Codemagic log and send it to me.

### Step 14. Test everything, then create the review demo accounts

Install the TestFlight build (iPhone) and the internal-testing build (Android), then check:

- **Create account** with your real mobile number: the SMS code arrives, the email link confirms, the app tour plays once (with voice), then the subscription screen appears.
- **Sign out** (Settings → About Safety Infinity → Account), then **sign in** with your username, again with your email, and again with your mobile number.
- **Forgot password** sends a reset email.
- Subscribing unlocks the app; **Restore purchases** works after reinstalling; About shows your plan. TestFlight and license-tester purchases are free test purchases.
- A record you create in My Work appears on your other phone after you sign in there. Add a photo and a PDF to it: both open on the other phone.
- **Settings:** switch Auto, Day and Night, try two themes, play each sound, and switch the language to **العربية** and back. Under **Read aloud**, choose **Natural voice** and play the English, Arabic, Urdu and Hindi samples: you should hear the natural female voice when online.
- **Library:** open any standard → **Listen** → choose Arabic: the page is translated, then read aloud.
- **Company → Team:** create your workspace. On a second phone, create a second account, then add its username as an **editor**: the second phone shows the workspace after **Refresh**. Add an incident on one phone and check it appears on the other. Change the second account to **viewer** and check its forms are read-only.
- **Alerts:** on both phones open **Settings → Notifications → Turn on notifications** and allow them, then **Send me a test**. On phone A, record a **Lost time injury**: phone B gets a *Critical incident* alert with the siren sound. Record a **First aid case** (beacon) and a **Near miss** (chime), raise an **NCR** (pulse) and close it (ping). Tapping an alert opens the record. (Alerts aren't sent to the person who made the change.)
- **Company → Dashboard:** tap *Try sample data* in a test workspace to see every chart, the employee of the month card and the notice board, then **Remove sample data**.
- **Certificates and posters:** open a training record → **Make certificates** → choose Arabic, then Urdu, and tap **Print or PDF**. Open the notice board → a poster → **Print or PDF**. The phone's print screen opens (choose *Save as PDF* to keep a copy).
- Users appear in Firebase **Authentication → Users**, workspaces under **Firestore → orgs**, phones registered for alerts under **users → (user) → devices**, and customers (named by account ID) in RevenueCat → **Customers**.

Then create two **demo accounts** for the store reviewers, using the fictional test numbers from Step 5 (codes `123456` / `654321`) and email addresses you can open (for example `yourname+apple@gmail.com` and `yourname+google@gmail.com`):

- **Apple demo:** finish sign-up and email confirmation; don't subscribe.
- **Google demo:** finish sign-up and email confirmation; then in RevenueCat → **Customers**, search for its user ID (shown in Firebase Authentication → Users as *User UID*) and **Grant** the `pro` entitlement (lifetime), so Google's reviewers get straight in.

Write the usernames and passwords into `store-listing/STORE-LISTING.md` where it says `YOUR-DEMO-USERNAME` / `YOUR-DEMO-PASSWORD`. Demo accounts from 3.0 keep working.

---

## Part E — Google Play release

### Step 15. Store listing, app content, closed test, production

- **Grow → Store presence → Main store listing:** paste the text from `store-listing/STORE-LISTING.md` (new name `Safety Infinity: HSE Pro`). Upload `store-assets/play-icon-512.png`, `play-feature-graphic-1024x500.png` and the images in `store-assets/screenshots/android/` (replace all old screenshots).
- **Policy → App content:** answer each section exactly as listed in `STORE-LISTING.md`, including **App access** (Google demo account), **Data safety** (now also photos, files and docs, and device or other IDs for alerts) and the **delete account URL**.
- Keep the **"Sources and independence"** paragraph in the description. Google requires apps that share government information (such as labour law and Civil Defense rules) to name their sources and say they don't represent a government.
- **Closed test (12 testers, 14 days):** **Testing → Closed testing** → create a track, add an email list of **at least 12** testers (add them as license testers too), create a release with the same `.aab`, **Start rollout**, and send testers the **opt-in link**. They must stay opted in for **14 days in a row**.
- **Production:** after 14 days, **Dashboard → Apply for production access**, answer the questions about your test, then **Production → Create release** with the same build and send it for review.

---

## Part F — App Store release

### Step 16. Fill in the version and submit

- **App Information:** name `Safety Infinity: HSE Pro`, subtitle, categories (Business / Reference), content rights (No), age rating (all None → 4+). Keep Apple's standard License Agreement.
- **Pricing and Availability:** Free (the subscription is the paid part).
- **App Privacy:** as listed in `STORE-LISTING.md` (email, phone number, user ID, photos, other user content and purchase history; linked to the user; no tracking). Publish.
- **Version 4.0.0:** upload the images from `store-assets/screenshots/iphone/` (replace the old ones), then paste the promotional text, description (it ends with the Terms of Use and Privacy Policy links, which Apple requires for subscriptions), keywords and What's New. Add the support URL and copyright `2026 Daniyal Khalid`.
- **In-App Purchases and Subscriptions:** select `mastersafety_pro_monthly` and `mastersafety_pro_yearly`.
- **App Review Information:** tick **Sign-in required** and enter the **Apple demo** username and password; paste the **App Review notes** from `STORE-LISTING.md`.
- Select the **build** from TestFlight, then **Add for Review → Submit**. Apple usually replies within a few days.

---

## Part G — After launch

**Post a regulatory update (no new app version needed):** in the public `safetyinfinity` repository, open `updates.json` → pencil → add a new item at the **top** of the `updates` list → **Commit**. Phones pick it up the next time the app opens with internet.

```json
{ "d": "2026-11-01", "t": "Title of the change", "b": "One or two sentences on what changed and who it affects.", "ref": "nfpa-72", "src": "NFPA" }
```

`ref` is optional: the library ID of the related standard, so readers can tap through to it.

**Account requests:** people can delete their own account in the app. If someone emails you instead, find them in Firebase **Authentication → Users** by email or phone, delete the user there, delete their `users/<User UID>` document (with its records, files, devices and usage) and `usernames/<username>` document in **Firestore**, and delete their customer in RevenueCat. To stop an abusive account, use **Disable account** in Firebase Authentication.

**Team workspace requests:** if the only admin of a workspace leaves the company without handing over, open **Firestore → orgs → (workspace) → members**, find the new admin's document and change its `role` field to `admin`; then ask that person to open **Company → Team → Refresh**. When someone deletes their account, the app removes them from every workspace automatically and promotes another member if they were the only admin.

**Keep costs in check:** Firebase **Authentication → Usage** shows SMS sent by country; Google Cloud **Billing → Reports** shows Text-to-Speech and Translation separately. Your budget alert emails you if costs rise. To change the monthly voice or translation allowance per account, change `MONTHLY_SPEAK` or `MONTHLY_TRANSLATE` in `firebase/functions/index.js` and deploy again (Step 7).

**Change prices:** change them in App Store Connect and Play Console only; the app shows whatever the store charges. Existing subscribers are notified by the stores as their rules require.

**Change the library or the app:** edit the files in `safetyinfinity-app` (or ask me to make the change), raise `"version"` in `package.json` (for example `4.0.1`), and run both Codemagic builds again. Upload the new `.aab` as a new Play release, and pick the new build on a new version in App Store Connect.

**Money:** the stores pay you monthly. Apple and Google collect and pay Saudi VAT (15%) for you. After VAT and a 15% store fee you receive about 11 SAR per monthly and 63 SAR per yearly Saudi subscription. RevenueCat → Overview shows subscribers and revenue.

**Name protection:** "Safety Infinity" isn't used by another app or HSE company that we could find, but that search is not a legal clearance. Before launch, consider a trademark search and filing for the name and the shield-and-infinity logo with the Saudi Authority for Intellectual Property (SAIP, saip.gov.sa).

---

## Troubleshooting

| Problem | What to do |
|---|---|
| Build stops: *"www/config.js still has placeholders"* or *"Firebase files missing"* | Finish Step 6 and Step 10. For a private test build only, add the environment variable `ALLOW_FREE_BUILD` = `1` in Codemagic; never upload that build. |
| *"Sign-in unavailable … missing from this build"* | The native bridge was not bundled. Check the Codemagic log for `prepare-www` and that `npm install` succeeded. |
| Creating an account works, but sign-in with **username or phone** fails | Step 7 isn't finished: check the deploy said *Deploy complete!*, `functions/.env` has the right Web API key, the **IAM Service Account Credentials API** is enabled and the **Token Creator** role was added. Firebase → Functions → Logs shows `signBlob` or `API key` errors. |
| SMS code never arrives on Android | Add the **App signing key** SHA-1 and SHA-256 from Play Console to Firebase (Step 12.3) and enable the **Play Integrity API** (Step 6.4). Install from a Play testing track, not a side-loaded file. |
| SMS code never arrives on iPhone | Check the build log shows `added to the Xcode project: GoogleService-Info.plist` and the patched `Info.plist`, and that the APNs key is uploaded (Step 6.5). |
| *"Phone verification isn't set up for this app"* | Same as the two rows above; then wait a few minutes and try again. |
| *"Too many attempts"* | Firebase limits repeated SMS or sign-in attempts from one device or number. Wait an hour, or use a test phone number while testing. |
| **No alerts on iPhone** | Push Notifications must be ticked on the App ID (Step 3.1), the provisioning profile regenerated **after** that (Step 11.4–11.5), and the APNs key uploaded to Firebase (Step 6.5). The Codemagic log should show `push notifications: App.entitlements set`. Then on the phone: Settings → Notifications → Refresh this phone. |
| **No alerts on Android** | On the phone: Android Settings → Apps → Safety Infinity → Notifications must be allowed. In the app: Settings → Notifications → Send me a test. If the test arrives but incident alerts don't, check Firebase → Functions → Logs for `onIncident` errors and the `TRIGGER_REGION` note in Step 7. |
| Alerts arrive without the chosen sound | Phones in silent or Do Not Disturb mode don't play sounds. On Android, a sound change takes effect for new alerts; if you changed sounds in Android's own notification settings, those settings win. |
| Read-aloud uses the phone's voice instead of the natural voice | The phone is offline, the **Cloud Text-to-Speech API** isn't enabled (Step 6.4), or the account has used its monthly allowance. Firebase → Functions → Logs shows `speak` errors. Some Android phones need a voice pack for Urdu or Hindi to read offline (Android Settings → Text-to-speech). |
| *"Couldn't translate this page right now"* | Enable the **Cloud Translation API** (Step 6.4) and deploy again (Step 7). |
| Subscription screen says *"Plans unavailable"* | Apple: the Paid Apps agreement must be **Active** and both products **Ready to Submit**. Google: both base plans **Active**, and the app installed from a Play testing track. RevenueCat: the `default` offering is **Current** and its packages contain the right product IDs. New Play credentials can take up to 36 hours. |
| Apple rejects under 5.1.1 (asking for a phone number) | Reply in Resolution Center: the mobile number is part of the account's identity and security — it is verified by SMS, used to sign in and to recover the account, and never used for marketing; accounts are needed because the subscription and the user's records sync across devices. |
| Apple rejects under 3.1.2 (subscriptions) | Make sure the description ends with working Terms of Use and Privacy Policy links, the subscription screen shows price and period for each plan, and Restore purchases works. Reply with the paywall screenshot. |
| Apple rejects under 4.2 (minimum functionality) | Reply: Safety Infinity is a native toolkit with an offline library of 239 standards, a 75-hazard register, 98 fill-in HSE document templates with attachments and printing, risk assessment, permit, checklist, PPE and investigation tools, a company HSE workspace with registers, charts, KPIs, generated reports, certificates and team alerts, an exam engine, a read-aloud voice in four languages, animations and a synced personal workspace. None of it is a website. |
| Google flags misleading or government information | Make sure the description keeps the sources list and the independence statement. |
| Codemagic: signing error on iPhone | Check the names match exactly: integration `Master Safety ASC key`, bundle ID `com.daniyalkhalid.mastersafety`, profile type App Store, and that the profile includes Push Notifications (Step 11.4). |
| Codemagic: Android keystore not found | The keystore reference name must be exactly `mastersafety_upload`. |
| Play says the version code was already used | Start a new Codemagic build. Build numbers go up automatically with each build. |
| Team: *"No Safety Infinity account has that username"* | The colleague must finish sign-up first (username, SMS code and email link). Check the spelling: usernames are shown in the colleague's **About → Account**. |
| Team: a member doesn't see the workspace | Ask them to open **Company → Team → Refresh**, or close and reopen the app. Check they are listed under **Members**. |
| Team: *"A workspace needs at least one admin"* | Make another member an admin first, then change or remove the last admin. |
| Team: records or attachments don't load (*"Loading the workspace…"* stays) | Check the rules were deployed in Step 7 (`firestore:rules`) and that the member's email and phone are verified. |
| The "What's new" screen says "bundled" | Check `www/config.js` has your real GitHub username and that `updates.json` opens in a browser. |

## Optional: build on your own computer instead

- **Android (Windows or Mac):** install Node.js 22 and Android Studio (Otter or newer). In the project folder run `npm install`, then `npm run setup:android`, then `npm run open:android`. In Android Studio choose **Build → Generate Signed App Bundle** and use your upload key.
- **iPhone:** this needs a Mac with Xcode 26. Run `npm install`, then `npm run setup:ios`, then `npm run open:ios`. In Xcode check **Signing & Capabilities** shows **Push Notifications**, then choose **Product → Archive → Distribute App → App Store Connect**.
