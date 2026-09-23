# Namonamaha Admin Portal — what it can do, end to end

This guide walks through everything the Admin Portal does, screen by screen, and how a booking travels through it from a devotee's first payment to the agent being paid. For setup, architecture and how to keep the customer care portal copy in sync, see the [README](README.md).

**Read this first:** the Admin Portal is a working *prototype*. It runs entirely in the browser with no backend. Anything that looks like money moving or a message being sent is simulated (see [What is real and what is simulated](#what-is-real-and-what-is-simulated)). The one exception is the CSV export, which is a real file download.

---

## 1. The big picture

Namonamaha books Hindu temple rituals (poojas) for devotees. Three kinds of booking exist: **offline** (in person at the temple), **online**, and **weekly/monthly subscriptions**. Four groups of people are involved:

| Who | What they do | Where |
|---|---|---|
| **Devotee** | Books a pooja and pays 20% advance, then the balance | Customer care portal (the booking flow) |
| **Customer care** | Confirms the booking, sends the balance-payment link, assigns an agent, handles escalations and cancellations | Customer care portal |
| **Onsite agent** | Meets the devotee at the temple, assigns the pandit, collects the completion code | Customer care portal |
| **Pandit** | Performs the pooja and closes it with the completion OTP | Customer care portal |
| **Admin** | Supervises all of the above: sees every booking and its money, pays agents, manages people, temples, closures, ads and customer care logins | **This Admin Portal** |

The Admin Portal sits *above* the customer care portal. The admin signs in once here, can step straight into the customer care portal without a second login, and sees every booking made there.

---

## 2. Signing in

- Open the Admin Portal and sign in with the admin login ID and password (prototype credentials are shown on the login screen and in the README).
- The eye icon shows or hides the password without clearing what you typed.
- The sign-in is remembered across browser tabs, so opening another Admin Portal tab, or coming back from the customer care portal, doesn't ask again. **Log out** (top right) ends it.
- **Dark mode switch:** the **Dark mode ON / OFF** switch in the top bar (and in the corner of the sign-in page) changes the whole screen between dark and light. Light mode gives a light top bar, dark headings and a pale wallpaper; dark mode is the original look. Your choice is remembered in this browser; the first time, it follows your computer's light/dark setting.
- The whole app is laid out for desktop: content fills the window, with larger text on big screens, over the Namonamaha wallpaper. The top bar shows the ॐ mark and NamoNamaha logo.

---

## 3. The screens

The tabs across the top are: **Overview · Daily pooja · Agents · Pandits · Temples · Blackout calendar · Advertisements · Customer care portal · Devotees**.

### 3.1 Overview

The business at a glance.

- **Total income** — the value of every pooja whose date has passed.
- **This month** — the same, for the current month only.
- **Upcoming bookings** — the value of poojas still ahead. Not income yet.
- **Pending agent payouts** — what agents have earned but haven't been paid, across all agents.
- **Income by temple** — a table of completed poojas and income for each temple.
- Shortcut cards to Daily pooja, Agents and the Customer care portal.

Overview figures come from the Admin Portal's own data only. Bookings made in the customer care portal deliberately don't feed these totals, because the two apps keep separate rosters of temples and agents (see the README).

### 3.2 Daily pooja

A month calendar of every pooja.

- Each day shows how many poojas ran and their total value. Today is outlined.
- **Previous / Today / Next**, plus **Month and Year dropdowns**, jump to any month.
- Click a day to see that day's poojas: temple, pooja, devotee, agent, pandit, amount and status (Completed or Upcoming).
- Under the devotee, agent and pandit are three contact icons (see [contact icons](#39-devotees)). Agents and pandits show their ID.
- Bookings made in the customer care portal appear here too, tagged **Real booking**.

### 3.3 Agents

Everything about each onsite agent, one card per agent.

- **Add agent** — enter a name and mobile number. The portal creates a login ID (`AG-` plus four digits) and a password made from the **first four letters of the name and the first four digits of the mobile** (Suranjeet / 7004518295 gives `sura7004`). It's shown once so it can be shared.
- **Contact and bank details** — phone, WhatsApp, bank name, account number and IFSC. **Edit bank details** changes any of them and they stay until changed again. Clearing them all returns the card to "Not added".
- **Earned / Paid / Pending** — an agent earns a flat ₹500 for each completed pooja they handled. Pending is earned minus paid.
- **Pay** — opens a dialog where you enter *any* amount: the full pending balance, a part payment, or an advance. Nothing is real; the payout is simulated so the whole flow can be seen.
  - A payment **can fail**: always when the agent has no bank account on file, and at random otherwise (a stand-in for a bank being down, a wrong IFSC or a payout limit).
  - A failed payment does **not** count as paid. An orange **Last payment failed** banner appears on the agent's card with the reason and time, and a red message confirms it.
- **Pay history** — every attempt, newest first, with date and time, amount, a Paid or Failed status, and the reason when it failed.
- **Notify agent (WhatsApp)** — simulates a WhatsApp message telling the agent they've been paid. It doesn't pay them.
- **Delete** — removes the agent.
- **Search and A–Z** — find an agent by name, phone or ID, or jump by first letter. Built for long lists.

### 3.4 Pandits

- **Add pandit** — same name and mobile form as agents, kept separate. Creates a `PD-` login ID and a password from the name and mobile the same way.
- The list shows each pandit's ID, phone and number of completed poojas.
- **View login** shows their login ID and password again. **Delete** removes them.
- Search and A–Z work here too.

### 3.5 Temples

- **Add temple** (name and city) and **Delete**.
- Each temple shows its ID and how many poojas have been booked there.

### 3.6 Blackout calendar

Temple closure dates such as Ambubachi Mela, festivals, ferry suspensions and bandhs. The list is read from the customer care portal, which is what enforces the closures.

- A **Locked** closure blocks new bookings on those dates. An **Unlocked** one is only a warning, so bookings are allowed again.
- **Unlock / Lock** toggles each closure.
- Each card shows how many bookings fall inside its dates.
- **Add closure** — pick a temple, a date or date range, and a reason. New closures start locked.
- **Remove** appears on closures you added yourself.
- A change takes effect the next time the customer care portal is opened or reloaded.
- The list only appears once the customer care portal has been opened at least once, because that's where the temples and closures come from.

### 3.7 Advertisements

Promo videos on YouTube, Instagram and Facebook.

- **Add video** — pick the platform, give it a title and paste the link.
- Each card shows the real platform icon, the title, a clickable link and the date added. **Delete** removes it.

### 3.8 Customer care portal

Three things:

1. **Enter as Admin — no login needed.** Opens the customer care portal in a new tab already signed in as the admin, straight onto the all-temples work queue, without typing a login ID or password. Going to the portal's normal address still shows its login page, so care staff are unaffected. Once inside, a **← Admin portal** button takes you back.
2. **Customer care login.** The main customer care login ID and password. **Show password** reveals it, **Change login** edits it. The change applies the next time the customer care portal loads.
3. **Customer care for a state.** **+ Add customer care** creates a new customer care login for a chosen state, with a login ID and password you set. That person can then sign in on the customer care portal's normal login page (they appear as "Customer care — Assam"). Login IDs are checked so you can't reuse one already taken by the main login, another state login or the portal's own accounts, including the admin. **Remove** withdraws a login and it stops working.

### 3.9 Devotees

The complete booking directory: one row per pooja, across all three booking types, seeded demo data and real bookings together. Built to stay usable with hundreds of devotees and repeat bookings.

**What each row shows**

- **Devotee** — name, plus a **"N days left"** box (it turns orange when three days or fewer remain, and disappears once the date has passed) and three contact icons.
- **Pooja** — name, pooja ID, a **booking-type tag** (Offline puja / Online puja / Subscription), a **Real booking** tag if it came from the customer care portal, and a **status tag**.
- **Agent** and **Pandit** — name, ID and the same three contact icons.
- **Pooja rate, Advance payment, Due, Full payment, Total.**
- **Date and time.**

**Contact icons** (devotee, agent and pandit alike): a real **Call** link, an **Internet call (masked)** button that simulates a masked call, and a **WhatsApp** link that opens a chat.

**How the money columns behave.** Pooja rate is the full price. Advance is the deposit paid up front (about 20%). Due is what's still owed and is shown in red. **Full payment and Total stay blank until the balance has actually been paid**, and Total only ever shows the full pooja rate once nothing is left due.

**Status tags**

| Tag | Meaning |
|---|---|
| **Awaiting balance** (orange) | Something is still due. The pooja can't start yet, however close its date is. |
| **Ready — fully paid** (blue) | The balance is cleared and the date is still ahead. |
| **Completed** (green) | Fully paid and the date has passed. |
| **● RUNNING** (green pulsing dot, red word, before the devotee's name) | The pandit or agent has started the pooja and it hasn't yet been closed with the completion OTP. Only real customer care bookings can be running. |

**Filters** — all combine, and **Clear filters** appears once any are on:
- **Booking type** — Offline, Online or Subscription.
- **Payment status** — Fully paid or Due pending.
- **From and To dates** — show only bookings in that range.
- **Search** — by devotee, agent or pandit name, phone or ID, or by pooja ID.
- **A–Z strip** — jump to devotees whose name starts with a letter.

**Export** — the **⬇ Export** button opens a small dialog with its own From and To dates (defaulting to what's on screen, but changeable independently). **Download CSV** saves a real file such as `namonamaha-bookings-2026-10-01_to_2026-10-05.csv`. It contains devotee, phone, booking type, pooja, pooja ID, temple, agent, agent ID, pandit, pandit ID, pooja rate, advance, due, full payment, total, payment status, date, time and source. It respects the current search and filters, plus the dates chosen in the dialog.

**One-year window** — poojas older than 365 days are removed automatically each time the app loads, so old records age out day by day.

---

## 4. A booking, end to end

This is how one booking moves through the two apps, and where the Admin Portal shows it.

1. **A devotee books** in the customer care portal and pays the **20% advance**. The booking is now *Advance paid*.
2. **Customer care confirms** by phone and **sends the balance-payment link**.
3. **The devotee pays the balance.** Only now is the booking *fully paid*.
4. **An agent is assigned.** The Admin Portal starts showing a booking as a **Real booking** once it has an advance payment *and* an assigned agent, because a pooja can't happen without an agent.
   - Until the balance is paid it reads **Awaiting balance**, with the amount due in red and the "N days left" box counting down.
   - Once paid in full it reads **Ready — fully paid**, with Full payment and Total filled in.
5. **A pandit is assigned** by the agent. The Pandit column fills in. A blank Pandit on a real booking is normal until then.
6. **The pooja runs.** When the pandit or agent starts it, the devotee's row shows the pulsing green dot and **RUNNING**.
7. **The pooja is closed** with the completion OTP. The row becomes **Completed** and counts as income on the Overview if it is one of the Admin Portal's own poojas.
8. **The agent is paid.** From Agents, the admin uses **Pay** with the amount to release. The attempt is recorded in **Pay history**, either as Paid or as Failed with the reason.
9. **The admin can step in at any point.** They can call, message or WhatsApp the devotee, agent or pandit from the icons, look up any booking in Devotees, export a date range, lock or unlock a temple closure that affects the date, or open the customer care portal directly.

---

## 5. The customer care portal (companion app)

The customer care portal is a separate app with its own repo. A copy lives in this repo as `customer-care-portal.html`. In short, it gives customer care a **work queue** (bookings grouped by what's blocking them), an **all-history** table, **SLA and escalation** tracking, **cancellations and refunds**, and a **message log**. It masks devotee names and phone numbers on screen for everyone who signs in, admin included. The Admin Portal is where the real values are visible (the Devotees tab).

Its own documentation, `PROJECT_CONTEXT.md` in its repo, is the reference for how it works.

---

## 6. What is real and what is simulated

| Real | Simulated |
|---|---|
| Sign-in state across tabs, everything you add, edit or delete (saved in the browser) | Payments to agents (the payout, its success or failure) |
| The Devotees **CSV export** — a genuine file download | WhatsApp notifications ("Notify agent") |
| **Call** and **WhatsApp** links (they open your phone app or WhatsApp chat) | **Internet call (masked)** — shows a message instead of calling |
| Reading real bookings from the customer care portal, and applying your lock/unlock, care logins and blackouts to it | Completion OTPs, SMS and email |
| The one-year clean-up of old records | The 20% random failure of a payout |

Two more things to know:

- **Data lives in this browser.** Nothing is sent to a server. Clearing site data resets the demo. The Admin Portal and the customer care portal only share information because they're opened from the same address.
- **Sign-ins are not secure.** Credentials are hardcoded or generated for the prototype. A real launch needs a backend with hashed passwords and a shared database, which would also make the customer care logins and closures reach the real customer care portal repo rather than only the copy kept here.

---

## 7. Where the data is stored

| Browser storage key | Holds | Written by |
|---|---|---|
| `nm_admin_v1` | Temples, agents (with bank details and pay history), pandits, ads, seeded poojas | Admin Portal |
| `nm_admin_session` | Whether the admin is signed in | Admin Portal |
| `nm_admin_theme` | Dark or light mode choice | Admin Portal |
| `nm_shared_care_login` | The main customer care login | Admin Portal, read by the customer care portal |
| `nm_shared_state_care_logins` | Customer care logins created for states | Admin Portal, read by the customer care portal |
| `nm_shared_blackouts` | Lock/unlock choices and added closures | Admin Portal, read by the customer care portal |
| `namonamaha-care-demo-v15` | All customer care portal data: bookings, temples, agents, pandits, closures | Customer care portal, read by the Admin Portal |

If the customer care portal's own storage key changes, the Admin Portal must be updated to match or real bookings quietly stop appearing. The README explains how to re-sync.

---

## 8. Tips

- After any update, do a **hard refresh** (`Cmd+Shift+R` on Mac) so the browser doesn't show old images or code.
- Open the customer care portal **once** before looking at the Blackout calendar or the "Real booking" rows, so its data exists to be read.
- The three sample agents, three pandits, four temples and eight devotees are demo data to show what the screens look like. Rows with no "Real booking" tag are demo data; they show as advance-only paid with the balance due.
