# SuiteSpot — Frontend (Hotel Reservation & Booking System)

Final project, **Group 1 · C3A** — Cortez, Cedric · Elpedes, Dustin · Maliñana, Johnbel

The React app for SuiteSpot, an 80-room hotel reservation system (8 floors × 10 rooms; Single,
Double, King and Suite): guests search by date, price and capacity and book rooms; the front desk
checks guests in and out; the manager runs rooms, bookings and accounts.

| | |
| --- | --- |
| **Frontend (live)** | `https://finals-project-webdevt.vercel.app/rooms` |
| **Backend repository** | https://github.com/Jbel02/final-project-1-backend-webdevt |
| **Backend (live)** | https://suitespot-api.onrender.com — [`/api/health`](https://suitespot-api.onrender.com/api/health) |

The interface carries over the iOS design language of `midterm-project-webdevt` — the same system
colours, type scale, fonts, translucent nav bar and tab bar, action sheets, spring transitions and
the Desktop / iPad / iPhone preview frame.

**Stack:** React 19, Vite, Tailwind CSS v4, React Router, Axios, Context API. Hosted on Vercel.

> **Proposal tracking:** every item of `final-project-proposal-G1.pdf`, every gap we found, every
> default we chose and every addition is listed in [`docs/CHECKLIST.md`](docs/CHECKLIST.md). The
> server-side items are built in the backend repository.

## Team

| Member | Owns |
| --- | --- |
| Maliñana, Johnbel | Manager screens (rooms, users), server and database setup, Atlas, Render and Vercel deployment |
| Cortez, Cedric | Guest flow (browse, book, my bookings), shared UI components, screenshots |
| Elpedes, Dustin | Front desk (bookings, check-in/out), server tests, receipt printing |

Work is done on branches and merged through reviewed pull requests.

## Screenshots

![Browse rooms](docs/screenshots/rooms.png)
![Book a room](docs/screenshots/book-room.png)
![My bookings](docs/screenshots/my-bookings.png)
![Dark mode](docs/screenshots/rooms-dark.png)

---

## Quick start

Requires **Node.js 20+**. The app needs the API, so clone both repositories side by side:

```
final-project-1-frontend-webdevt/     (this repo)
final-project-1-backend-webdevt/
```

**1. Start the API** (see the backend README for options):

```bash
cd final-project-1-backend-webdevt
npm run install:all
npm run dev
```

It runs on http://localhost:5000. Without a `MONGODB_URI` it starts an in-memory database and
seeds it, so no database setup is needed.

**2. Start the frontend** in a second terminal:

```bash
cd final-project-1-frontend-webdevt
npm run install:all
npm run dev
```

Open http://localhost:5173. The dev server forwards `/api` to the API on port 5000. If you
restart the API, the in-memory data resets and you'll be signed out.

### Demo accounts (seeded test data)

| Role | Email | Password |
| --- | --- | --- |
| Manager | manager@suitespot.test | Manager123 |
| Receptionist | frontdesk@suitespot.test | Desk12345 |
| Guest | guest@suitespot.test | Guest1234 |
| Guest | maria@suitespot.test, ana@…, carlo@… | Guest1234 |

These accounts work on the live site as well as locally. Seeded bookings are placed relative to
today, so there is always a guest checked in, one arriving today, one leaving today, plus past,
future and cancelled stays.

### Scripts (run from the repo root)

| Script | What it does |
| --- | --- |
| `npm run dev` | Vite dev server on port 5173 |
| `npm test` | Client tests |
| `npm run lint` | ESLint over the client |
| `npm run build` | Production build into `client/dist` |

---

## Project map

```
shared/                 rules used by BOTH repositories (no dependencies)
  constants.js            room types, prices, meal rates, statuses
  dates.js                date-only helpers, hotel time zone, overlap rule
  pricing.js              quoteBooking() — the one place a bill is computed
  validation.js           every form's validator (frontend + backend validation)

client/                 React (Vite) + Tailwind
  src/api/                one function per endpoint (Axios)
  src/context/            AuthContext, ThemeContext, DeviceContext
  src/components/         iOS primitives ported from the midterm + Receipt, RoomPhoto…
  src/pages/auth/         Sign In, Create Account
  src/pages/rooms/        Browse (guest), Manage (manager), Detail, Book, Editor
  src/pages/bookings/     My Bookings (guest), Front Desk (staff), Booking detail/receipt
  src/pages/users/        Users, New Receptionist, Account (manager)
  src/pages/Profile.jsx   everyone
  src/styles/             tokens.css (midterm), components.css (iOS primitives), device.css
```

> `shared/` is mirrored in the backend repository, because both sides run the same rules. If you
> change a file there, change it in both repositories.

---

## How it works

**Authentication.** Login sets a JWT in an **httpOnly cookie** — page scripts never see the
token. The client hides screens a role can't use (`RequireRole`), but that's a courtesy: the API is
the gate, and the backend's permission tests check every endpoint against every kind of caller.

**Validation, twice.** Each form runs a validator from `shared/validation.js` as you type (a
field's message appears after you leave it or submit). The server runs **the same function**
again and returns per-field messages, which appear in the same place.

**Money.** The booking form and the server both price a stay with `quoteBooking()`; the server
never trusts amounts sent by the browser. Partial payment is 30% now, with the balance settled at
check-out.

**Receipts.** "Print Receipt" calls `window.print()`. A print-only receipt is portalled into
`<body>`, outside the phone frame; print CSS hides everything else.

**Dates.** Stays are calendar dates stored as UTC midnight, and "today" is the hotel's day in
Asia/Manila.

---

## Design (carried over from the midterm)

- `client/src/styles/tokens.css` is the midterm's token file, unchanged; Tailwind reads it
  through `@theme inline`, so `bg-card`, `text-label-secondary` or `text-green` re-theme with dark
  mode automatically.
- Same font stack as the midterm: SF Pro on Apple devices, Segoe UI Variable on Windows, Roboto
  on Android. Apple's licence doesn't allow serving SF Pro as a web font, so it comes from the OS.
- iOS wording: centred nav titles, Title Case buttons ("Book This Room", "Check In"), Cancel /
  Save / Edit in the bar, action sheets for destructive choices ("Cancel Booking" / "Keep
  Booking"), short sentence-case footnotes under grouped lists.
- Push transitions for detail screens, sheet-style presentation for tasks (booking, sign in,
  editors), cross-fades between tabs — all critically damped springs.
- Device preview (top-right control): Desktop, iPad Pro 11″, iPhone 17 Pro Max. Layout inside
  responds to container queries on the simulated screen.
- Reduced motion, reduced transparency and increased contrast are honoured.

---

## Deployment (Vercel)

Hosting isn't specified in the proposal; this setup is an addition (see the checklist). The API
and database are described in the backend README (Render + MongoDB Atlas).

**Vercel** — import this repo with the repo root as the root directory and the Application Preset
set to **Other**. `vercel.json` builds `client/` and forwards `/api/*` to the API on Render. If
Render assigned a URL other than `https://suitespot-api.onrender.com`, update it in `vercel.json`.

The browser only ever talks to the Vercel domain, so the auth cookie is first-party everywhere
(Safari blocks cross-site cookies). Render's free tier sleeps when idle — open the site a minute
before a demo.
