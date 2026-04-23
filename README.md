# Invig Locate IT – Tag Landing Page

A mobile-first landing page served when a user scans an NFC or QR tag attached to a tracked asset. Built as part of the **Invig Locate IT** ecosystem for real-time asset tracking in workshops, dealerships, and rental services.

| Version | URL | Description |
|---|---|---|
| V1 (current) | [westersnik.github.io/Keyfinder/](https://westersnik.github.io/Keyfinder/) | Static branded landing page |
| V2 (prototype) | [westersnik.github.io/Keyfinder/V2/](https://westersnik.github.io/Keyfinder/V2/) | Dynamic GIAI-driven page with mock data |

---

## Repository structure

```
gh-pages branch
├── index.html              # V1 – compiled React/Vite app (static)
├── assets/
│   ├── index-*.js          # Bundled React application
│   └── index-*.css         # Compiled Tailwind styles
├── V2/
│   ├── index.html          # V2 – standalone HTML/JS, no build step
│   └── img/
│       ├── invig-logo.png
│       ├── asset-key.jpg
│       ├── asset-tool.jpg
│       ├── asset-vehicle.jpg
│       ├── asset-equipment.jpg
│       └── asset-medical.jpg
└── README.md
```

Source code for V1 lives in the `main` branch (React + Vite project). V2 is a self-contained HTML file with no dependencies or build step.

---

## V1 – Static landing page

The V1 page presents a clean, Invig-branded experience when any tag is scanned. It is not yet GIAI-aware — it shows the same content regardless of which tag was scanned.

**What it includes:**

- "Tag skannet" confirmation badge with pulse animation
- Hero image of a key with a BLE tracking tag
- Three feature cards: real-time tracking, time savings, DMS/ERP integration (Automaster, Keyloop, SAP)
- Action buttons: **Få mer informasjon** → invig.no, **Kontakt salg** → sales@invig.no, **Support** → support@invig.no
- Invig-branded footer

---

## V2 – Dynamic GIAI-driven page

V2 reads the GIAI from the URL and renders fully asset-specific content. It is a single self-contained HTML file with no build step or external dependencies.

### URL format

```
https://westersnik.github.io/Keyfinder/V2/?giai=<GIAI>
```

Both `?giai=` and `?id=` are accepted as query parameters.

### What changes per asset

| Element | Driven by |
|---|---|
| Hero image | `asset_type` |
| Page title | `asset_type` |
| Type icon and label | `asset_type` |
| Asset name | `asset_name` |
| Customer name | `customer_name` |
| Last known location | `location_hint` |
| Responsible person | `assigned_to` |
| Status badge | `status` (active / checkout / missing / maintenance) |
| System reference | `ref` (DMS/ERP record ID) |
| Support button link | `contact_url` |

### Supported asset types

| Type key | Icon | Hero image | Example use case |
|---|---|---|---|
| `key` | 🔑 | asset-key.jpg | Car keys at dealerships and workshops |
| `tool` | 🔧 | asset-tool.jpg | Hand tools, power tools |
| `vehicle` | 🚗 | asset-vehicle.jpg | Loan cars, fleet vehicles |
| `equipment` | 🏗️ | asset-equipment.jpg | Forklifts, heavy machinery |
| `medical` | 🏥 | asset-medical.jpg | Medical devices, hospital equipment |
| `pallet` | 📦 | asset-equipment.jpg | Pallets, shipments |
| `other` | 📌 | asset-key.jpg | Any other tracked asset |

### Demo GIAIs (mock data, no API required)

| GIAI | Type | Description |
|---|---|---|
| `70735391257` | key | BMW 5-series, Sandnes Bilsenter, Automaster |
| `70735391258` | tool | Snap-on torque wrench, SAP |
| `70735391259` | vehicle | Audi A4 loan car, Keyloop |
| `70735391260` | equipment | Toyota forklift, SAP PM |
| `70735391261` | medical | GE ultrasound, Stavanger University Hospital |

**Example URL:**
```
https://westersnik.github.io/Keyfinder/V2/?giai=70735391257
```

---

## Tag identity: GS1 GIAI

Each physical tag is identified by a **GIAI (Global Individual Asset Identifier)** — a GS1 standard for uniquely identifying individual assets across organisations and systems. The GIAI is encoded directly into the QR code or NFC record on the tag.

**Invig GCP prefix:** `7073539`

**Example:**
```
GIAI         70735391257
GS1 AI       (8004) 70735391257
```

### Redirect flow

Invig operates a **GS1 Digital Link** resolver at `id.invig.no`. When a tag is scanned, the encoded URL follows the GS1 Digital Link URI syntax:

```
https://id.invig.no/8004/<GIAI>
```

The resolver redirects to the landing page with the GIAI passed as a query parameter:

```
https://westersnik.github.io/Keyfinder/V2/?giai=<GIAI>
```

This architecture follows the [GS1 Digital Link standard](https://ref.gs1.org/standards/digital-link/uri-syntax/), which allows the same physical tag to resolve to different endpoints depending on context (browser vs. machine-to-machine) via content negotiation at the resolver level.

---

## Multi-customer, multi-object support

Because all content is driven by the GIAI rather than hardcoded into the page, the same deployment serves entirely different experiences depending on who scanned what:

- A **car dealership** scanning a vehicle key sees the vehicle registration, current handler, and a link to their DMS record in Automaster or Keyloop.
- A **construction company** scanning a power tool sees the tool name, last service date, and a link to their ERP entry in SAP.
- A **hospital** scanning a medical device sees the device ID, maintenance status, and a contact link to their biomedical engineering team.
- A **logistics operator** scanning a pallet tag sees the shipment ID, destination, and estimated handover time.

---

## Tech stack

| Layer | V1 | V2 |
|---|---|---|
| Frontend | React 19 + TypeScript | Vanilla HTML/CSS/JS |
| Styling | Tailwind CSS v4 | Inline CSS |
| Bundler | Vite | None (no build step) |
| Hosting | GitHub Pages (`gh-pages`) | GitHub Pages (`gh-pages/V2/`) |
| Tag identity | GS1 GIAI (AI `8004`) | GS1 GIAI (AI `8004`) |
| Resolver | `id.invig.no` GS1 Digital Link | `id.invig.no` GS1 Digital Link |
| Data | Static (no API) | Mock data in-page (Bifrost API planned) |
| Tag encoding | QR code or NFC NDEF URI | QR code or NFC NDEF URI |

---

## Development

### V1 (React/Vite)

```bash
# Install dependencies
pnpm install

# Start local dev server
pnpm dev

# Build for production
pnpm build
# Output: dist/public/ → copy to gh-pages branch root
```

### V2 (standalone)

No build step. Edit `V2/index.html` directly and push to `gh-pages`.

To replace mock data with live data, update the `lookupAsset()` function in `V2/index.html` to call the Invig Bifrost API:

```js
async function lookupAsset(giai) {
  const res = await fetch(`https://api.invig.no/bifrost/assets/${giai}`);
  if (!res.ok) return null;
  return res.json();
}
```

---

## Roadmap

- [x] Static branded landing page (V1) with CTA buttons
- [x] GitHub Pages deployment
- [x] GS1 Digital Link resolver at `id.invig.no` (`8004/<GIAI>` → landing page)
- [x] V2 prototype with dynamic GIAI rendering and 6 asset types
- [x] AI-generated hero images per asset type
- [x] Mock data for 5 demo GIAIs across key, tool, vehicle, equipment and medical
- [ ] Connect V2 to live Invig Bifrost API (replace mock `lookupAsset()`)
- [ ] Customer-specific branding (logo, accent colour) from API response
- [ ] "Report found" flow for lost asset recovery
- [ ] Scan event logging (timestamp, approximate location) to Bifrost
- [ ] GitHub Actions workflow for automated V1 build and deploy

---

## Contact

**Invig AS** · [invig.no](https://invig.no) · [sales@invig.no](mailto:sales@invig.no)  
Koppholen 19, 4313 Sandnes · Org. 913 630 726 mva
