# Understanding Scheme Fees · 2-Day Simulation

A single-file, browser-based decision simulation that teaches payment professionals how a card
scheme's fee invoice is built and how to make it smaller without breaking the things it protects.
Two days, fifteen calls a day, real budget movement on every call, and a live cohort leaderboard.

Built on The Payment School Simulation Engine by **intreensic**.

- **Build:** v2.0 · 2-day (the build stamp is printed on the sign-in screen)
- **Course id:** `scheme-fees`
- **Audience:** this build is written in plain business English for cohorts working in a second
  language. Trade terms are kept and glossed in brackets on first use.
- **Repo:** `OtoEbe/Scheme-Fees-2-Day`

---

## Where it runs

| URL | Role | Notes |
| --- | --- | --- |
| https://tpssimulation.intreensic.com/scheme-fees/ | **Primary. This is the address the cohort uses.** | Served from the `scheme-fees/` folder of the `Risk-and-AML-in-Acquiring` repo, which is the repo the custom domain is attached to. |
| https://otoebe.github.io/Scheme-Fees-2-Day/ | Backup and source of truth for the code | GitHub Pages on this repo, `main` branch, `/(root)`. |

### One thing to know about running two copies

A GitHub Pages custom domain attached to a project site serves **that repo only**. The domain cannot
path-route to this repo, which is why the primary address still lives in the Risk & AML repo. Both
copies must be updated when the build changes, or the two URLs drift apart.

The two addresses are different browser origins. Anything the browser stores locally (the run
snapshot, the unlocked-day flags, the cached session) does not travel between them. Accounts and
progress do travel, because they live in Supabase: a learner who signs in on the backup URL pulls
their cloud progress and carries on. Tell learners to pick one address and stay on it for the two
days, and keep the backup for the day the primary misbehaves.

---

## What is in this repo

```
index.html          The whole simulation. One file, no build step, no dependencies.
.nojekyll           Stops GitHub Pages running the file through Jekyll.
README.md           This file.
supabase/
  config.toml       Project reference for the Supabase CLI.
  migrations/       Schema history, applied in filename order.
```

`index.html` is roughly 364 KB and contains the engine, the course content pack, the styling, the
facilitator console and the report writer. There is nothing to install and nothing to compile. Open
it from disk and it runs offline against local storage.

---

## Deploying an update

Do both places, in this order, and verify each one.

**1. The primary address.** Open the `Risk-and-AML-in-Acquiring` repo, go into the `scheme-fees`
folder so the URL ends `/tree/main/scheme-fees`, choose Add file, then Upload files, drag the new
`index.html` in, and commit. Never drag a folder at the repo root. Doing that has twice created a
nested `Something/scheme-fees/index.html` path that looks committed and serves nothing.

**2. This repo.** Same idea at the root: Add file, Upload files, drag `index.html`, commit.

**3. Verify.** Hard-refresh each URL with Ctrl+F5 and read the build stamp on the sign-in screen. It
should say **v2.0 · 2-day**. The Pages CDN can take about ten minutes, so if the old stamp is still
there, wait and refresh again rather than re-uploading.

Always upload the file you were given directly rather than one pulled out of a synced cloud folder.
A stale sync copy is the single most common cause of "I deployed and nothing changed".

---

## Running the course

### Shape

| | Day 1 | Day 2 |
| --- | --- | --- |
| Title | What the Bill Is Made Of | Finding and Stopping the Leaks |
| Calls | 15 | 15 |
| Difficulty | ×1.7 | ×1.8 |
| Unlock | Open | Code **TRIGGER** |

Day 1 covers who pays whom, how each fee charges, what sets a fee off, which fees are compulsory
against which are chosen, then the fee schedule itself and the lines that bill on nothing. Day 2
covers the penalties a programme causes itself, clearing and settlement discipline, billing-line
visibility, the cost map, owners for every leak, unit economics, the monthly routine and renewal
week.

### Codes

- **Day 2 unlock:** `TRIGGER`. Give it to the room at the start of day two and not before.
- **Facilitator console:** `CATALYST`, from the link at the bottom of the sign-in screen.

### Scoring

Each day scores a composite out of 100 across six weighted criteria, multiplied by the day's
difficulty, plus 15 percent for a first attempt and 10 percent for taking no hints. A perfect run is
**438 points**. Ranking uses each player's best attempt for points and their most recent run for
money, and a promotion needs both.

| Rank | Points | Capital |
| --- | --- | --- |
| Billing Analyst | start | $100,000 opening budget |
| Scheme Analyst | 100 | $128,000 |
| Fee Manager | 210 | $158,000 |
| Head of Scheme Management | 270 | $176,000 |
| Chief Payments Officer | 400 | $224,000 |

A clean run finishes day one on 213 points and $162,000, which is Fee Manager, and closes day two on
438 points and $237,000, which is Chief Payments Officer. A worst-case run finishes on 109 points
and negative $43,500 and stays Billing Analyst.

Replays serve a second question set, Series B, chosen by attempt number so the whole room sees the
same set. The two sets are calibrated to identical money at every slot, so a replay is fair rather
than easier.

### Facilitator console

Reachable from the sign-in screen with `CATALYST`. It holds the day codes, live cohort statistics,
the programme record (title, cohort, venue, dates, facilitator, pass threshold, default 70 percent),
and the roster pulled straight from the profiles table with each participant's best points per day,
score percentage, closing capital, rank, attendance, participation and remarks.

Exports:

- **Final Training Report** per participant, a standalone HTML completion record with a status, a
  score against the threshold and a verification id.
- **Cohort zip** of every report.
- **Gradebook CSV** with a byte-order mark and CRLF line endings, so Excel opens it cleanly.
- **Mailto drafts** per participant and for the cohort summary. Attachments are added by hand,
  because there is no mail server in the loop by design.

The console can also remove a participant, which cascades their events, sessions, enrolment,
progress and auth user so the email can register again.

### On the day

Learners create an account with name, team, email and password. Everyone at a table uses the same
team name spelled the same way, because team standings are the average of member results.
Verification email arrives in the background and never blocks play. The first sign-in on a device
needs a connection. After that the simulation runs offline, keeps a run snapshot on the device, and
uploads results when the network returns. The debrief button says "Upload results" for that reason.
If the room's connection is unreliable, tell learners to press it and wait for the tick before they
close the tab.

---

## Backend

Supabase project **Understanding Scheme Fees**, reference `wskjrcgdhgolahrrmpvs`, region eu-west-1,
free tier. The URL and the publishable key are embedded in `index.html` inside the BACKEND block.
The key is publishable and every table is governed by row level security, so it is safe in a public
file and can be rotated from the dashboard.

Tables: `profiles`, `enrollments`, `sessions`, `events`, `progress`, plus the `leaderboard` and
`leaderboard_days` views. Writes are restricted to signed-in users and scoped to the rows they own.
Anonymous read is left open for the standings and the console. The `events` table is write-only to
the client, so a count run as an anonymous role will read zero even when telemetry is flowing.

Migrations, in order:

```
20260806153358_scheme_fees_schema_v1
20260806153411_facilitator_delete_profile
20260811171659_scheme_fees_accounts_v2
20260811171757_accounts_v2_grants_fix
20260811171904_accounts_v2_close_legacy_update
```

### Before a cohort

- Free-tier projects pause after about a week of no traffic. Open the dashboard the day before and
  confirm the project is active, then load the live URL and check the cloud chip reads "synced".
- Decide what to do with leftover test accounts. The database is not empty between cohorts unless
  someone empties it.
- The built-in email sender is rate limited to a handful of messages an hour, which is fine for
  background verification and thin for a full room. Custom SMTP is the fix and is still outstanding.

---

## Editing the course content

Everything course-specific sits between the `PACK:BEGIN` and `PACK:END` markers near the top of the
script, plus the `BACKEND` block just below. The engine underneath reads only from those objects and
hardcodes no course facts, so a new programme is a clone of the file with a new pack.

Each question node carries a title, an objective, the story text, a hint, and exactly four choices:
one best, one acceptable, two poor. Every choice carries its own money delta, its consequence text
and its rationale. Flags written by a choice reach later calls through `ss.carry`, within the same
day as well as across days.

Three rules that exist because breaking them has cost real rework:

1. **Write all four options to one length band.** The option text is the decision alone. The teaching
   belongs in the rationale. When the best answer is visibly the longest, learners pick it by shape
   instead of by judgement. The verification harness enforces a ratio of 1.25 and has caught this
   three times.
2. **No em-dashes, and no "not this, it is that" phrasing.** House style, enforced by the harness.
3. **Rerun the harness after any content edit.** It walks the structure, the goto chain, the criteria
   and objective integrity, the Series A against Series B money and flag parity at every slot, the
   flag ordering, the rendering of every conditional story line under both states, and the rank
   arithmetic at both ends of the range.

---

## Verification status for this build

- Structural and economic harness: 122,555 checks plus 60,000 randomised two-day runs, all green.
- Browser playthrough on Chromium against a mocked backend: account creation, avatar, boardroom
  briefing, fifteen calls with the two-step lock, debrief, end of day one standings, `TRIGGER`
  unlock, fifteen more calls, and the final ranking across both days. Green.
- Checked and clean: no day-three residue, no retired unlock code, no em-dash anywhere in the file,
  and exactly one literal closing script tag, which matters because the report writer emits HTML
  from inside the engine's own script block.

---

## Version history

| Version | Date | Change |
| --- | --- | --- |
| v1.0 | 6 Aug 2026 | First build of the course on the v0.6 engine. Three days, ten calls a day, Series A only. |
| v1.1 | 11 Aug 2026 | Accounts on Supabase Auth, background email verification, cloud progress with cross-device resume, and the Series B question set. |
| v1.1.1 | 11 Aug 2026 | Two-step answering, progress flush on sign-out and tab close, one-time email sign-in link, build stamp on the sign-in screen. |
| v2.0 | 18 Aug 2026 | Two days of fifteen calls in place of three days of ten. All sixty questions rewritten in plain business English with glossed trade terms. Difficulty multipliers retuned to hold the 438-point ladder. Flags now readable within the same day. |

---

## Known gotchas

- A project-site custom domain serves one repo. It does not path-route to others. This cost a day
  once already.
- Dragging a folder onto a GitHub repo root creates a nested path rather than replacing files.
  Upload the single file, from inside the target folder.
- The Pages CDN caches for roughly ten minutes. Read the build stamp before concluding a deploy
  failed.
- Free-tier Supabase projects pause when idle. Restore from the dashboard, no data is lost.
- Two live copies means two updates. Keep them in step or retire one.

---

Built and maintained by intreensic. The companies, people and merchants in the simulation are
invented. The fee structures, rates and thresholds follow the published scheme material used in the
workshop.
