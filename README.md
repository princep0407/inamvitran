# Inam Vitran Hajari 2026 — Naroda

Attendance + gift-distribution tracker for the event. Works from any phone's browser,
syncs live across every phone via Firebase, and has login-based access control so
Balak (boys) and Balika (girls) teams — and the gift team — only see their own side.

This is a single static file (`index.html`), so it needs no build step. You need to:
1. create a free Firebase project (for live sync),
2. push this folder to GitHub,
3. deploy it on Netlify.

---

## 1. How login & access already works

The app has a login screen built in with three kinds of Login IDs:

| Role | Sees | Set up by |
|---|---|---|
| **Admin** | Everything: entry, gifts, reports, settings, and can create/remove Login IDs | You, first |
| **Entry (હાજરી)** | Only marking arrivals — locked to Balak *or* Balika *or* both, your choice per ID | Admin, in સેટિંગ → Login IDs |
| **Gift (ઇનામ)** | Only their assigned gift group's students | Admin, in સેટિંગ → Login IDs |

**The very first time the page is opened (before any Login ID exists), it grants
temporary Admin access automatically** so you can log in to સેટિંગ and create real
Login IDs (e.g. `balak_entry` / `balika_entry` / `gift_team1`, each with its own
password). Once the first ID is created, everyone — including you — must log in.

To keep Balak and Balika data separate, create two Entry IDs and set "વિભાગ" to
બાળક for one and બાલિકા for the other; each ID will then only see and edit its own
side's students.

> **Security note:** access control here is enforced by the app's login screen, not
> by the database. The Firestore rules below (step 3) are intentionally open so the
> app can work without a full backend. This is fine for a temporary community event
> app used by trusted volunteers, but anyone who obtained your Firebase config and
> knew the collection names could bypass the login and write data directly. Don't
> reuse this Firebase project for anything sensitive.

---

## 2. Create a free Firebase project (for live sync across phones)

1. Go to https://console.firebase.google.com and create a new project (free "Spark" plan is enough).
2. In the left menu: **Build → Firestore Database → Create database**. Choose a region close to you, and start in **production mode**.
3. Click the gear icon → **Project settings** → scroll to "Your apps" → click the **Web** icon (`</>`) → register an app (any nickname).
4. Firebase shows a `firebaseConfig` object like:
   ```js
   const firebaseConfig = {
     apiKey: "AIza...",
     authDomain: "your-project.firebaseapp.com",
     projectId: "your-project",
     storageBucket: "your-project.appspot.com",
     messagingSenderId: "...",
     appId: "..."
   };
   ```
5. Open `index.html` in this repo, find the `firebaseConfig` block near the top of the `<script>` section, and replace the placeholder values with your own.
6. Back in Firebase Console: **Firestore Database → Rules**, delete the default rules, paste in the contents of `firestore.rules` from this repo, and click **Publish**.

That's it — no other Firebase setup needed. The app talks to Firestore directly from the browser.

---

## 3. Push this folder to GitHub

```bash
cd inam-vitran-hajari      # this folder
git init
git add .
git commit -m "Inam Vitran Hajari 2026 attendance app"
git branch -M main
git remote add origin https://github.com/<your-username>/<your-repo>.git
git push -u origin main
```

(Or use GitHub Desktop / the "Add file → Upload files" button on github.com if you prefer not to use the command line.)

---

## 4. Deploy on Netlify

1. Go to https://app.netlify.com → **Add new site → Import an existing project**.
2. Connect your GitHub account and pick this repo.
3. Build settings: leave **Build command** empty and **Publish directory** as `.` (this repo already has a `netlify.toml` that sets this for you).
4. Click **Deploy site**. Netlify gives you a URL like `your-site.netlify.app` — that's the link everyone uses on event day.

Any time you push a change to GitHub, Netlify redeploys automatically.

---

## 5. Day-of checklist

- Open the site once yourself first, log in with the Admin access you get automatically, go to **સેટિંગ → Login IDs**, and create IDs for your Balak entry team, Balika entry team, and each gift-group volunteer.
- Share the site URL + each team's Login ID/password with them.
- The header shows **"Live, synced across phones"** once Firebase is configured correctly — if it instead says "Offline: this phone only", double-check the `firebaseConfig` values and that Firestore rules were published.
