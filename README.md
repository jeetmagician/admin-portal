# Namonamaha Admin Portal

A single-file, front-end-only **prototype** of the admin console for Namonamaha, a service that books Hindu temple rituals (poojas) for devotees — offline (in-person), online, and weekly/monthly subscription bookings. It runs entirely in the browser with no backend: no real database, no real payments, no real messages. Everything is simulated so the full admin workflow can be seen, tried, and handed off as a spec.

It's designed to work alongside a companion prototype, the **customer care portal** (the CRM used by care staff, onsite agents, and pandits). That CRM has its own separate repo, [`jeetmagician/customer-care-portal`](https://github.com/jeetmagician/customer-care-portal) — this repo keeps its own copy as `customer-care-portal.html` (see "Keeping the CRM copy in sync" below for why that matters and how to redo it).

## Running it locally

No build step, no dependencies. Serve the folder with any static file server and open `index.html`:

```bash
python3 -m http.server 8080
# then open http://localhost:8080/index.html
```

**Admin Portal login:** `admin` / `Namonamaha@2026`
**Customer care portal:** click **Enter customer care portal** on the Admin Portal's *Customer care portal* tab to go straight in as Admin with no login. Signing in by hand also works: `admin` / `Admin@123`, or a care login such as `priya.care` / `Kamakhya@101` (the main care login can be changed, and extra state logins created, from the Admin Portal — see below).

## What's in the Admin Portal

- **Overview** — total and monthly income (counted once a pooja's date has passed), upcoming booking value, pending agent payouts, and an income-by-temple breakdown.
- **Daily pooja** — a full calendar (with Month/Year dropdowns and a Today shortcut) showing every pooja on a given date: temple, pooja, devotee, agent, pandit, amount, and status. Bookings synced from the customer care portal (see below) are marked **Real booking**.
- **Agents** — contact details, bank account info (editable, persists until changed), and payouts. Clicking **Pay** opens a dialog to enter any amount (full due, partial, or an advance) — payments are simulated and can realistically **fail** (no bank details on file, or a random simulated gateway decline), which shows up immediately as a warning on the agent's card and in their **Pay history**, without being counted as paid.
- **Pandits** — roster with generated login credentials.
- **Temples** — add/remove temples from the catalogue.
- **Blackout calendar** — temple closure dates (Ambubachi Mela, festivals, bandhs) read from the customer care portal, each with **Lock / Unlock**: locked blocks new bookings on those dates, unlocked is warning-only so bookings are allowed again. Also add a new closure (starts locked) and remove ones you added. Takes effect the next time the customer care portal loads.
- **Advertisements** — YouTube/Instagram/Facebook promo links, each with the real platform icon (cut to a transparent PNG from the provided brand marks).
- **Customer care portal** — a one-click **Enter as Admin** into the CRM with no login ID/password (you're already signed in here), an editor for the main customer care login (changing it here changes what actually works over there), and **Customer care for a state** — create a new customer care login (state, login ID, password) that can then sign in on the CRM's normal login page, with a duplicate-ID check and a Remove button.
- **Devotees** — a searchable directory of every pooja booking (seeded demo data plus real bookings synced from the CRM) with:
  - One-click **Call**, simulated **Internet call (masked)**, and **WhatsApp** icons for the devotee, agent, and pandit on every booking.
  - A full payment breakdown per booking: Pooja rate, Advance payment, Due, Full payment, Total. Full payment and Total only ever show once the balance has actually cleared — a booking with anything still due can't have started, no matter how close its date is.
  - A clear status tag per booking — **Awaiting balance** (due > 0, can't start), **Ready — fully paid**, or **Completed** — plus, for a pooja the CRM says is actually in progress (started but not yet closed with the completion OTP), a green pulsing dot and red **RUNNING** next to the devotee's name instead.
  - A **"N days left"** countdown under each devotee's name (turns orange inside 3 days), hidden once the date has passed.
  - Filters for booking type (Offline / Online / Subscription), payment status, a date range, free-text search (name/phone/ID), and an A–Z jump strip.
  - **CSV export**, with its own independent date range, that downloads a real file — not simulated.
  - A rolling one-year data window: records older than 365 days are pruned automatically as the app loads.

Both apps share a visual identity (fonts, color tokens, the same wallpaper photo and logo) and, since they're served from the same origin, some state on purpose: the Admin Portal reads the CRM's booking data directly from `localStorage` to power the Daily pooja / Devotees "Real booking" rows (only once a booking has an advance payment *and* an assigned agent — a pooja can't happen without one), and writes the shared customer-care login the CRM reads back.

## Architecture notes

- **No backend.** All state lives in the browser's `localStorage`. `index.html` owns `nm_admin_v1` (temples, agents, pandits, ads, seeded poojas) and a couple of small shared keys; `customer-care-portal.html` owns its own larger snapshot (`namonamaha-care-demo-v15`) with its full booking/staff/roster data.
- **The CRM bridge is one-directional and intentionally scoped.** The Admin Portal reads real bookings from the CRM to enrich Daily pooja and Devotees only — it deliberately does **not** feed the CRM's agents/temples into Overview income or Agent payouts. Those two systems have independent rosters, and merging them into one financial ledger is a bigger, separate problem than this prototype solves.
- **Everything that looks like money moving or a message sending is simulated** — payouts, WhatsApp notifications, masked calls, OTPs. The one genuinely real action is the Devotees CSV export, which is a real client-side file download.
- **No real authentication.** Login credentials are hardcoded/generated for the prototype and are not meant to be secure.
- **The CRM masks devotee details for everyone, admin included — this app is where they're unmasked.** The CRM masks a devotee's name/phone *at render time* inside its own screens; the underlying stored data is always the real value. The Admin Portal reads that raw stored data directly, so the Devotees tab shows real names and numbers by design (the CRM's own docs assign unmasked devotee details to the admin portal, not to the CRM).

### Keeping the CRM copy in sync

`customer-care-portal.html` here is a **manually-synced copy** of the separate CRM repo, not a live pull — the two repos have independent git histories. This matters because the CRM bridge (`CRM_STORAGE_KEY` in `index.html`, currently `'namonamaha-care-demo-v15'`) has to match whatever `STORAGE_KEY` the CRM's own `index.html` is actually using. If the CRM repo bumps its storage key (it does this on every change to its data shape) and this copy isn't updated to match, the bridge silently reads a key that no longer exists — Daily pooja/Devotees just quietly stop showing any real bookings, with no error.

To re-sync after a CRM update:
1. `git fetch <the CRM remote>` and diff its `index.html` against this repo's `customer-care-portal.html` to see what changed.
2. Pull in the new `index.html` wholesale, then re-apply this repo's local-only additions (search for each by name — none exist upstream): the "← Admin portal" link in the top bar, the wallpaper background on `.care-login-wrap` (with its starfield markup removed), and `syncSharedCareLogin()` / `applyAdminBlackouts()` / `openAsAdminIfRequested()` in the bootstrap line. `syncSharedCareLogin()` also adds the state customer care logins the Admin Portal saves under `nm_shared_state_care_logins` as care users (ids prefixed `SC-`, replaced on every load so removed ones disappear). `applyAdminBlackouts()` does the same for blackout locks: it re-applies `nm_shared_blackouts` (`{locked, removed, added}`) to the CRM's own `BLACKOUTS` on every load, with added closures prefixed `AB-`. `openAsAdminIfRequested()` is what makes `?admin=1` enter as the admin with no login: it sets the same session state the CRM's own `cc-login` handler sets after a successful admin sign-in, so if the CRM changes how its login gate works, this needs updating too.
3. Update `CRM_STORAGE_KEY` in this repo's `index.html` to match the CRM's new `STORAGE_KEY`.
4. Re-test the bridge: open the CRM once (to seed/persist its data), then check Daily pooja / Devotees in the Admin Portal actually show a "Real booking" row.

As of this sync the CRM has been simplified to **one login page into a single all-temples customer care portal** (`admin` or any per-temple care login opens the same portal). Its earlier Desk A/B model, its own Admin panel and per-temple/zone isolation were built and then removed upstream (the CRM's `PROJECT_CONTEXT.md` lists them under "built, then removed"), so none of that is reflected here.

## Files

| File | What it is |
|---|---|
| `index.html` | The Admin Portal — everything described above. |
| `customer-care-portal.html` | The CRM used by customer care, onsite agents, and pandits to run bookings day to day. |
| `logo-transparent.png` | Shared NamoNamaha wordmark, used in both apps' top bars and login screens. |
| `wallpaper.jpg` | Shared login/background art. |
| `icon-instagram.png`, `icon-facebook.png`, `icon-youtube.png` | Platform icons shown on Advertisement cards. |
| `logo.jpg` | Legacy logo asset. |

## Status

This is a working prototype, not a production system. If/when this becomes real, the natural next step is a real backend (the CRM's own in-app documentation sketches a Supabase + Razorpay + WhatsApp Cloud API design) behind both of these front ends.
