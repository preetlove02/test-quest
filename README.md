# Thryve Quest — Complete Guide

A 135-question student assessment with a one-time access link per
student. Google Sheets is the database, Google Apps Script is the
backend, and every submission is emailed to you as a PDF.

---

## 🔗 Important Links (bookmark this section)

| What | Link |
|---|---|
| **Student form (live)** | https://preetlove02.github.io/test-quest/index.html |
| **Apps Script Web App URL** (backend) | `https://script.google.com/macros/s/AKfycbxCnIQwezzE6mBMUXC4FdL3y22bm1gROHfX8DVeJ1XusePoYL7sP39bZ8PUCbOohtJe/exec` |
| **Apps Script editor** (to edit Code.gs / view Sheet / check Executions) | https://script.google.com |
| **Admin tool** (generates student links) | `admin.html` — open it locally, or host it next to `index.html` |
| **PDF responses are emailed to** | thryvemeeraki@gmail.com |

> If you ever create a **new** Apps Script deployment (not just a new
> version of the same one), the Web App URL changes. You'd need to paste
> the new URL into `CONFIG.SCRIPT_URL` in both `index.html` and
> `admin.html`, then re-upload them. Editing an *existing* deployment
> ("Manage deployments → New version") keeps the same URL — no file
> changes needed.

---

## 🎯 How to Generate a Student Link (the main thing you'll do day-to-day)

1. **Open `admin.html`** in your browser (double-click the file, or visit
   wherever you've hosted it).
2. In the **"Base form URL"** field, paste:
   ```
   https://preetlove02.github.io/test-quest/index.html
   ```
3. In **"How many codes?"**, enter how many you need (e.g. `1` for a
   single student, or `30` to generate a whole class at once).
4. Click **Generate**.
5. A list appears with ready-to-share links, e.g.:
   ```
   https://preetlove02.github.io/test-quest/index.html?otp=AB3X7Q9K
   ```
6. Click **Copy** next to each link and send it to that student (WhatsApp,
   email, SMS — anything). Each link is unique and works only once.

**Want a memorable/custom code instead of a random one?** Use the
**"Manually re-issue a single code"** box at the bottom of `admin.html`
— type something like `RHYTHM42`, click **Add this code**, and it'll
generate a link using that exact code.

**Don't have `admin.html` open / prefer doing it by hand?** Paste this
directly into your browser's address bar (replace `YOURCODE` with
anything):
```
https://script.google.com/macros/s/AKfycbxCnIQwezzE6mBMUXC4FdL3y22bm1gROHfX8DVeJ1XusePoYL7sP39bZ8PUCbOohtJe/exec?action=add&otp=YOURCODE&callback=x
```
If it works, you'll see `x({"status":"ok"});` on the page. The student
link is then:
```
https://preetlove02.github.io/test-quest/index.html?otp=YOURCODE
```

---

## 👀 Tracking Students — the OTPs Sheet

Open your Apps Script's bound Google Sheet → **OTPs** tab. Each code has
a status that updates automatically:

| Status | Meaning |
|---|---|
| `UNUSED` | Code generated, student hasn't opened the link yet |
| `USED` | Student opened the link and entered their details, but hasn't submitted (may still be mid-quiz) |
| `COMPLETE` | Student fully submitted their responses |

Once a code is `USED` or `COMPLETE`, that link can never be opened again
— even by the same student. If a student gets stuck or needs a redo,
generate them a fresh code.

All full responses (every question + every answer, in full text — not
just A/B/C/D) live in the **Responses** tab, one row per submission.

---

## ⏱ The 3-Hour Timer

The moment a student clicks **"Begin My Quest"**, a countdown starts
(visible at the top of their screen). They have **3 hours** to finish.

- Toast warnings appear at 15 minutes and 5 minutes remaining.
- If time runs out, whatever they've answered so far is **automatically
  submitted** — nothing is lost.
- The timer is tracked in the browser tab only (not saved anywhere), so
  if a student closes the tab and reopens the *same* link... they can't —
  the code is already `USED`, so they'd be locked out and would need a
  new code from you.

To change the time limit, edit this line near the top of `index.html`'s
`<script>` block:
```js
const TIME_LIMIT_MS = 3 * 60 * 60 * 1000; // 3 hours — change the "3" to whatever you want
```

---

## 📧 What Gets Emailed, and to Whom

| Email | Sent to | Contains |
|---|---|---|
| Full response PDF | **thryvemeeraki@gmail.com** (set via `RECIPIENT_EMAIL` in `Code.gs`) | Every section, question, and answer, grouped by section, with the Thryve logo at the top |
| Confirmation note | The student's own email address | Short "we've received your response" message — no PDF attached |

If the student confirmation isn't arriving, check the student's
Spam/Promotions folder first, and see the Troubleshooting section below.

---

## 🧰 Setting Up From Scratch (only needed once, already done for this delivery)

1. Go to [script.google.com](https://script.google.com) → **New project**.
2. Delete the default code and paste in the full contents of `Code.gs`.
3. Run `setupSheets` once (select it in the function dropdown → **Run**).
   This creates the **OTPs** and **Responses** tabs in a bound Google
   Sheet, and will prompt you to authorize Sheets/Drive/Gmail/Docs access
   — accept the prompts.
4. **Deploy → New deployment → type: Web app**
   - Execute as: **Me**
   - Who has access: **Anyone**
   - Click **Deploy**, copy the **Web app URL**.
5. Paste that URL into `CONFIG.SCRIPT_URL` in both `index.html` and
   `admin.html` (already done in this delivery — see the Links table at
   the top).
6. Host `index.html` anywhere static (GitHub Pages, Google Sites, etc.)
   — already live at the URL in the Links table above.

---

## 🛟 Troubleshooting

**"Could not reach the server" / link won't load:**
- Apps Script → **Deploy → Manage deployments** → edit (pencil icon) →
  confirm **Execute as: Me** and **Who has access: Anyone**. If you
  change either, click **Deploy** again.
- If you edited `Code.gs` after deploying, you must create a **New
  version** (same screen) for the live URL to run your latest code.
- Try in an **incognito window** — ad blockers sometimes block requests
  containing `callback=`.

**"No access code was found in this link":**
- The URL is missing `?otp=...` at the end. Always use the exact link
  `admin.html` generates — don't just open the bare form URL.

**A code shows "already been used" but the student says they never
opened it:**
- Someone may have opened the link once already (even briefly) — check
  the OTPs sheet status. If it's stuck on `USED` and the student is
  legitimately blocked, just issue them a new code.

**Confirmation email to the student isn't arriving:**
- Check the **Email** column for that row in the **Responses** sheet —
  if it's blank or wrong, that's why.
- Check **Executions** in the Apps Script editor → click the relevant
  `doPost` row → look for a logged error.
- Check the student's Spam/Promotions folder.
- If the counsellor PDF *also* stopped arriving at the same time, it's
  likely a **Gmail daily send quota** issue (consumer Gmail accounts cap
  around 100 sends/day) — wait 24 hours or use a Google Workspace
  account for a higher limit.

**Made code changes and they don't seem to take effect:**
- For `Code.gs`: you must **Deploy → Manage deployments → edit → New
  version → Deploy** after every change — saving alone isn't enough.
- For `index.html` / `admin.html`: make sure you actually re-uploaded the
  file to wherever it's hosted (GitHub Pages can take a minute to update
  after a push).

---

## 📁 Files in This Delivery

| File | Purpose |
|---|---|
| `Code.gs` | Apps Script backend — paste into your Apps Script project |
| `index.html` | The student-facing quiz (single self-contained file, 135 questions) |
| `admin.html` | Generates student access links |
| `README.md` | This guide |

No scoring or interpretation is calculated automatically — every raw
answer is captured as-is, in full text, for your counsellors to review
and interpret manually.
