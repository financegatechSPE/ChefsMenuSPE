# SigEp Meal Plan app — setup guide

A web app your brothers install from a link. No App Store needed.
There are four tabs: **Week**, **Today** (rate meals 1–10 and leave feedback), **Late Plates** and **Suggestions** (lunch, dinner, dessert and drink requests).

**First-time setup:** the first time someone opens the app, it asks for their name, meal plan (Full or Half) and dietary needs. They can reopen it from the person icon in the header. This info goes to the Profiles tab, and only you and the chef see it.

**Ratings:** a meal can be rated once it starts being served. Ratings for each day's meals stay open until 11:00 AM the next day, when the next lunch starts. Both the app and the backend enforce this.

**Late plates:** brothers check the meals they'll miss and enter the name for the plate. Requests close 2 hours before each meal: lunch (11:00–12:30) closes at 9:00 AM and dinner (5:00–6:30) closes at 3:00 PM, Eastern time. The app and the Google Sheet backend both enforce the cutoff. Brothers can cancel up until the cutoff.
Ratings and requests collect in a Google Sheet. Each week Jake asks Claude, in the "App for Chef's Menu" project, for the feedback report to send to Chef's Menu.

## Files
| File | What it is |
|---|---|
| `index.html` | The whole app |
| `menu.json` | **This week's menu.** It's the only file that changes every Sunday. |
| `manifest.webmanifest`, `sw.js`, `icons/` | These make the app installable and let it open offline |
| `apps-script/Code.gs` | The Google Sheet backend that collects everyone's ratings |

## 1. Put it online (free, about 5 minutes) with GitHub Pages
1. Create a free GitHub account and make a new **public** repo, e.g. `sigep-meals`.
2. Click **Add file → Upload files** and drag in everything from this folder, keeping the `icons` folder.
3. Go to **Settings → Pages**, set Source to *Deploy from a branch* and pick `main` / root. Then save.
4. After about a minute your link is live at `https://<your-username>.github.io/sigep-meals/`. Send that link to the chapter.

(Netlify Drop also works: drag the folder onto app.netlify.com/drop.)

## 2. How brothers install it
- **iPhone:** open the link in **Safari**, tap **Share ⬆︎**, then **Add to Home Screen**.
- **Android:** open the link in Chrome, tap **⋮**, then **Install app** (or *Add to Home screen*).

The app shows these instructions itself the first time someone opens it.

## 3. Connect the Google Sheet so everyone's responses are shared
Until you do this, the app runs in **demo mode**: ratings are saved only on the phone that sent them.
1. Create a new Google Sheet, e.g. "SigEp Meal Feedback".
2. Open **Extensions → Apps Script**, delete the sample code, paste in `apps-script/Code.gs` and save.
3. Click **Deploy → New deployment → Web app**. Set *Execute as*: **Me** and *Who has access*: **Anyone**. Click Deploy and authorize it.
4. Copy the web-app URL (it ends in `/exec`).
5. In `index.html`, paste that URL into `BACKEND_URL: ""`, then re-upload `index.html`.
6. In `Code.gs`, change `ADMIN_PIN` to a private value and redeploy. Anyone with the URL plus that PIN can read the responses.

The sheet gets **Ratings**, **Late Plates**, **Suggestions** and **Profiles** tabs. A brother's dietary needs are attached to each of his late-plate rows automatically, with one row per response. You can also share the sheet itself with Chef's Menu.

## 4. Every Sunday: new menu
Send Claude the screenshot or PDF of the new Chef's Menu menu. You'll get back a new `menu.json`.
Upload it to the repo so it replaces the old one. Everyone's app shows the new week the next time they open it, with no reinstall.
Past ratings stay in the sheet, tagged by week number.

## Weekly feedback report for Chef's Menu
Ask Claude in the project for "this week's Chef's Menu report". Claude needs the responses, which it can get in one of three ways:
- **Easiest:** in the Google Sheet, go to **File → Download → CSV** for each tab (Ratings, Suggestions) and attach both files.
- Or connect Google Drive to Claude so it can read the sheet directly.
- Or give Claude the web-app URL plus the PIN (`…/exec?pin=YOURPIN`).

Claude then makes the report: average rating per meal, every comment, the top and bottom meals, and all requests grouped by lunch, dinner, dessert and drink. It comes as a doc you can export to PDF or Word.

## Late-plate list for the chef
Each late plate is a row in the **Late Plates** tab, with Status *Requested* or *Cancelled*. To get the list for a meal, filter by Date and Meal and skip any name that has a later *Cancelled* row. You can also ask Claude for "today's late plates" with the sheet data.

**Updating the backend:** after any change to `Code.gs`, go to Deploy → Manage deployments → ✏️ → Version: *New version* → Deploy. That keeps the same URL.
