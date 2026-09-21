# QRDine · Product Showcase

A one-page showcase of **QRDine**, a multi-tenant QR table-ordering platform for restaurants: what it does, the four apps it ships, demo recordings, the engineering decisions behind it and the tech it runs on.

**Live link:** _add after deploying_

> Built as a full-stack project. This page shows my work on it as a Full-Stack Developer.

---

## What's on the page

| Section | What it shows |
|---|---|
| Hero | Title, one-line pitch, my name and role, buttons, and a phone showing the customer menu |
| Four apps | Customer, cashier/kitchen, restaurant admin and platform console, each with its API route group |
| Demo | A phone recording (guest side) and a desktop recording (counter side) in device frames |
| Numbers | Ten figures that describe the codebase — apps, endpoints, roles, tables, events, order states |
| Features | Six feature cards, from the scan to the settled bill |
| Engineering | Six highlights, an architecture diagram and the order-lifecycle rail |
| Tech | The stack, grouped into Front ends, Backend, Data & realtime, and Security & ops |
| Code | GitHub repositories |
| QR flow | Four steps from printing a code to settling the table, plus an optional scannable demo QR |

The page is plain HTML, CSS and JavaScript in one file, with no build step. The only outside resource it always loads is Google Fonts. A QR-code library is fetched from a CDN **only** if you set `CONFIG.demo.url`.

---

## Folder structure

```
showcase/
├── index.html            # the whole page: HTML, CSS and JS
├── README.md
├── .gitignore
└── assets/
    ├── logo.svg          # QR + plate mark (header, footer, favicon)
    ├── images/           # optional: hero screenshot, og.png
    └── videos/
        ├── customer.mp4  # you add this
        └── staff.mp4     # you add this
```

---

## Preview locally

```bash
python3 -m http.server 4322
```

Run it inside `showcase/`, then open <http://localhost:4322>.

**On localhost, empty sections show placeholders** that say what's missing (for example "Add your recording at assets/videos/staff.mp4"). **Once deployed, an empty Code section hides itself**, so visitors never see a half-finished page.

Opening `index.html` directly from Finder works too — nothing on the page needs a server.

---

## Editing: everything is in `CONFIG`

Open `index.html` and find `const CONFIG = {` near the bottom. You shouldn't need to touch the HTML or CSS.

| Field | What it does |
|---|---|
| `name`, `role` | Shown in the hero, footer and browser tab |
| `site` | Your portfolio link, used in the hero byline and footer |
| `contact.email` / `linkedin` / `github` | Icon links in the hero, text links in the footer. Leave a field empty to hide it |
| `repoUrl` | The **Source** button in the nav. Empty = the button just scrolls to the Code section |
| `demo.url` | A live `/m/<qrToken>` link. See [Demo QR](#demo-qr) |
| `hero` | Hero image. Empty = the built-in menu mock. See [Hero image](#hero-image) |
| `videos.customer` / `videos.staff` | `src` (path or URL to an .mp4) and an optional `poster` image |
| `github` | List of repos. See [GitHub repos](#github-repos) |

---

## Hero image

```js
hero: { src: "", framed: true, alt: "QRDine customer menu on a phone" },
```

- **`src: ""` (the default)**: the page draws a **built-in mock** of the customer menu inside the phone frame — restaurant header, table + session line, search, category pills, four dishes with veg/non-veg marks and one sold-out item, and a cart bar. It's pure HTML and CSS, so it's always crisp and never 404s. The restaurant in it ("Spice District") is a placeholder, not a real business.
- **`framed: true`**: a plain screenshot of the customer app with no device around it. The page wraps it in the phone frame. About 9:19.5 (like 1179×2556) fits best.
- **`framed: false`**: the image already includes the phone (for example an AI mockup). Use a **transparent PNG** — there's an ember glow behind it and a solid background shows up as a visible rectangle.
- **Don't draw the floating chips into the image.** The page adds its own "Scan the table QR" and "Kitchen sees it instantly" chips on top.
- **Use demo data in any real screenshot.** Don't show a live restaurant's real orders, staff names or customer notes on a public page.

---

## Demo videos

Put the recordings at `assets/videos/customer.mp4` and `assets/videos/staff.mp4`, or point `CONFIG.videos.*.src` at any hosted .mp4.

- **Frames:** the customer slot uses a phone frame, the staff slot a browser-window frame. Orientation is re-checked once the file loads, so a portrait file always gets the phone and a landscape file always gets the window — whichever slot it's in.
- **Playback:** videos play muted and on loop while they're on screen, and pause when scrolled away. The expand button opens full screen **with sound**.
- **If a file is missing**, the frame shows a placeholder, so the layout never breaks.

Good things to capture:

| Slot | Suggested take |
|---|---|
| `customer.mp4` | Scanning a table QR → menu loads → add two dishes with a note → place order → status moves to ACCEPTED |
| `staff.mp4` | Live order board → accept the order → kitchen display → mark ready/served → generate bill → record a UPI payment |

Shrink a screen recording to a web-friendly size (H.264, starts playing before it finishes downloading):

```bash
ffmpeg -i recording.mov -vf "scale=-2:1280" -c:v libx264 -crf 26 -preset slow -movflags +faststart -an customer.mp4
```

`-an` removes the audio track. Leave it out to keep sound. Aim for **under ~10 MB per video**. GitHub rejects files over 100 MB.

---

## Demo QR

```js
demo: { url: "", label: "Open the demo menu" },
```

- **Empty (the default):** the card in the QR flow section explains how to turn it on. **No fake QR is ever drawn**, so nobody scans a code that goes nowhere.
- **With a URL:** the page lazily loads `qrcode` from jsDelivr and renders a real, scannable QR for that link, plus an "Open the demo menu" button. If the CDN is blocked, the explainer stays and the button still works.

Use a **demo restaurant and a demo table**, never a live one — anyone who scans it can place orders on that table.

---

## GitHub repos

```js
github: [
  { label: "QRDine — full platform", description: "Backend, customer, cashier and admin apps", url: "https://github.com/…", private: false },
  { label: "This showcase",          description: "Single-file HTML · no build step",          url: "",                   private: false },
],
```

- **With `url` set:** a clickable card that says "View repository".
- **With `private: true`:** a card that says "Private repo · walkthrough on request". Use this for code that can't be public.
- **With neither:** the card shows a reminder on localhost and is hidden once deployed. Fill in the showcase repo's own URL after your first push.

---

## The numbers

The Numbers section is **static text in the HTML**, not a live API call. Each tile carries a `data-count` attribute and animates up when it scrolls into view.

Where the figures come from, so you can keep them honest as the code changes:

| Figure | Source in the repo |
|---|---|
| 4 applications | `client/`, `cashier/`, `admin/` (admin + platform console), `backend/` |
| 59 REST endpoints | Counted across the five route groups: auth 5, public 5, staff 18, admin 27, platform 4 |
| 6 staff roles | The `user_role` enum: platform_admin, owner, manager, cashier, kitchen, waiter |
| 11 core tables | restaurants, users, refresh_tokens, dining_tables, table_sessions, categories, menu_items, orders, order_items, service_requests, audit_log |
| 9 realtime events | `order:new`, `order:updated`, `order:accepted`, `service_request:new`, `service_request:updated`, `session:updated`, `menu:availability`, `session:billed`, `session:closed` |
| 3 ordering modes | `order_verification`: `staff_confirm`, `none`, `session_required` |
| 6 order states | NEW → ACCEPTED → PREPARING → READY → SERVED → COMPLETED (plus CANCELLED) |
| 4 payment methods | CASH, CARD, UPI, OTHER |

A closing line under the grid says these describe the codebase, not production traffic — **keep it**. It's the difference between a confident page and an overclaiming one.

### Switching them to live numbers later

If you deploy a public, read-only, CORS-open endpoint:

1. Set `CONFIG.api` to its base URL.
2. Add `data-stat="<key>"` to the tiles you want to be live.
3. Add a loader per key in the commented **LIVE NUMBERS** block near the bottom of the script and uncomment it.

Each tile keeps its `data-count` value as the fallback, so a failed request degrades to the static number instead of showing a blank.

---

## Deploy

Any static host works. Pick one.

**GitHub Pages:**

```bash
git add .
git commit -m "QRDine showcase"
git branch -M main
git remote add origin https://github.com/<your-username>/qrdine-showcase.git
git push -u origin main
```

Then go to the repo's **Settings → Pages → Deploy from a branch → `main` / root**. The site appears at `https://<your-username>.github.io/qrdine-showcase/`.

**Netlify Drop:** drag the `showcase` folder onto <https://app.netlify.com/drop>.

**Vercel:** run `npx vercel --prod` inside this folder.

After deploying, open the link on your phone and check the hero mock, the demo frames and the QR card. Then put the link in your resume.

---

## Notes

- **No secrets on this page.** Unlike the MandirLive showcase there is no storefront token here — nothing on the page talks to a backend. Keep it that way: never paste a JWT, a database URL or a seeded admin password into `index.html`.
- **`og:image` is a relative path.** Social previews need an absolute URL. After deploying, add `assets/images/og.png` (1200×630) and change the `og:image` meta to the full `https://…` URL.
- **Fonts:** Fraunces for display, Inter for body, both from Google Fonts. If you want zero third-party requests, self-host them into `assets/` and swap the `<link>` for a local `@font-face`.
- **Accessibility:** the page respects "reduce motion" (no autoplay, count-ups or reveal animations), everything works with the keyboard, and the mock menu is `aria-hidden` so screen readers don't read a fake restaurant's prices.

---

## Before sharing: checklist

- [ ] `name`, `role`, `site` and contact links are correct
- [ ] `repoUrl` points at the real QRDine repo
- [ ] `customer.mp4` and `staff.mp4` recorded, compressed and added
- [ ] Hero left as the built-in mock, or swapped for a real screenshot with demo data
- [ ] `demo.url` set to a **demo** table, or left empty
- [ ] Showcase repo's own URL filled into `CONFIG.github`
- [ ] Numbers re-checked against the current codebase
- [ ] Deployed, `og:image` made absolute, and tested on a phone
- [ ] Link added to the resume
