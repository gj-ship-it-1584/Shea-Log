# Shea's Log — setup

A one-page app that replaces the printed feeding sheet. Tap **Left** or **Right** when a feed starts, **Pee** / **Poop** / **Pee + Poop** after a change, **Spit-up** when it happens. Every entry lands in a table that reads like the printout (Time · Left · Right · Spit · Pee · Poop), and the tiles at the top always show how long it has been since the last feed, wet diaper, and dirty diaper. Tap any row to fix the time, side, or add a note. Tap ⚙︎ for sharing, export, and printing.

The whole thing is four files in this folder: `index.html`, `sw.js`, `manifest.webmanifest`, and the icons. It is free to run. There are two pieces to set up, once, and each takes a few minutes.

---

## Part 1 — Put the app online (GitHub Pages, free)

The app has to live at a web address so both phones can open it and add it to their home screens.

1. Sign in at <https://github.com> (create a free account if you don't have one).
2. Click **+** (top right) → **New repository**. Name it `shea-log`, leave it **Public**, tick **Add a README**, click **Create repository**.
3. On the repository page click **Add file → Upload files**. Drag in all the files from this folder: `index.html`, `sw.js`, `manifest.webmanifest`, `icon-180.png`, `icon-192.png`, `icon-512.png`. Click **Commit changes**.
4. Click **Settings** (repository tab) → **Pages** (left sidebar). Under **Branch** choose `main` and `/ (root)`, click **Save**.
5. Wait a minute, refresh that page, and it shows your address, which looks like:

   `https://YOURNAME.github.io/shea-log/`

Open that on your iPhone in Safari. The app works right away, but only on that one phone until you do Part 2.

To update the app later, upload a new `index.html` the same way; both phones pick it up the next time they open it.

---

## Part 2 — Turn on sharing between your phones (Firebase, free)

Sharing uses Google's Firebase database. The free tier allows far more than a baby log will ever use, and nothing here requires a credit card.

1. Go to <https://console.firebase.google.com> and sign in with any Google account.
2. **Create a project** (or "Add project"). Name it `shea-log`. When asked about Google Analytics, turn it **off**. Click **Create project**, then **Continue**.
3. In the left sidebar open **Build → Firestore Database** → **Create database**.
   - Location: pick the one nearest you (for example `nam5 (United States)`). Click **Next**.
   - Choose **Start in production mode**. Click **Create**.
4. Still in Firestore, open the **Rules** tab, delete everything there, paste this, and click **Publish**:

   ```
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /families/{family}/{document=**} {
         allow read, write: if family.size() >= 20;
       }
     }
   }
   ```

   This lets the app read and write only under a long, random family code that the app generates. Anyone without the code sees nothing.

5. Get the app's config: click the **gear ⚙︎ → Project settings** (top of the left sidebar). Scroll to **Your apps**, click the **`</>`** (Web) icon, give it any nickname such as `phone`, leave Hosting unticked, click **Register app**. You'll see a block of text that starts with `const firebaseConfig = {` and ends with `};`. Select and copy that whole block.
6. On your iPhone, open the app (your GitHub Pages address), tap **⚙︎** in the top right, paste the block into **Paste your Firebase config**, and tap **Connect**. The page reloads and the status pill changes to **Shared · Firebase**.
7. Tap **Send invite link** and text or AirDrop it to your wife. She opens the link once in Safari on her iPhone, and her phone joins the same log. **While that page is open**, she taps Share → Add to Home Screen.

8. Do the same on your own phone: open your own invite link (Copy link, paste into Safari), then Share → Add to Home Screen.

That's it. Whatever either of you taps shows up on the other phone within a second or two. If a phone is offline, entries save locally and sync when it reconnects.

**Why "from the invite link" matters.** On iPhone, an app on the home screen keeps its own settings, completely separate from Safari. If you set up sharing in Safari and then add a plain copy to the home screen, the home-screen copy starts blank. The invite link carries the family code inside the address, so a home-screen icon made from that page remembers it.

**Checking you're on the same log.** Open Settings (top-right button) on both phones. The **family code** must be identical. If it isn't, paste one phone's code into the other phone's **Use your partner's code instead** box and tap **Switch**. The status pill should say **Connected · live** on both; **Connecting…** that never clears means Firestore isn't created yet or the rules weren't published.

---

## Everyday use

- **Feeds**: tap Left or Right when she latches. A timer starts and shows on both phones. Tap **Switch to Right** if she changes sides, **End feed** when done. If you forget to end it, just tap the row and type the minutes, or leave the duration blank; the sheet never tracked minutes anyway.
- **Made a mistake?** Every quick tap shows an **Undo** button for six seconds. After that, tap the row to edit or delete it.
- **Forgot to log it live?** Three ways, all ending in the same form:
  - Tap **Earlier** in the bottom bar.
  - **Press and hold** any button in the bottom bar (Left, Pee, Spit-up…) to open the form with that type already chosen.
  - Tap **+ Add an entry** at the bottom of the log (this also works on past days).
  In the form, tap a quick chip such as **30 min ago** or **2 hr ago**, or set the exact time with the picker. The line under the picker confirms the time in plain words before you save.
- **Photos of a diaper** (or spit-up): right after you tap Pee or Poop, the confirmation offers **Add photo**; iPhone then asks Take Photo or Photo Library. You can also open any diaper entry and tap **Add photo**. Photos are shrunk to about 150 KB and kept in the same shared database, so both phones can see them and nothing extra needs setting up. Tap a thumbnail to view it full-screen or delete it.
- **Past days**: the ‹ › arrows move by day. Tap the date label to jump back to today.
- **Pediatrician**: **Share summary** sends the day's counts as a text; **⚙︎ → Download CSV** gives the full history as a spreadsheet.
- **Night feeds**: the app follows your phone's dark mode, so it stays dim at 3 a.m.

## Notes

- The data belongs to you. It lives in your Firebase project and on each phone. Nothing is sent anywhere else.
- Firebase's free tier is more than enough (it allows tens of thousands of reads and writes per day; a newborn generates a few dozen).
- If you ever want to start over on a phone, ⚙︎ → Advanced → Disconnect.
