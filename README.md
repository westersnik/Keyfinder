# Invig Locate IT – Tag Landing Page

A mobile-first landing page served when a user scans an NFC or QR tag attached to a tracked asset. Built as part of the **Invig Locate IT** ecosystem for real-time asset tracking in workshops, dealerships, and rental services.

Live: [westersnik.github.io/Keyfinder](https://westersnik.github.io/Keyfinder/)

---

## What it does today

When a tag is scanned, the user is presented with a clean, branded landing page that:

- Confirms the tag has been scanned and the asset is registered in the Locate IT system
- Communicates the core value propositions of Locate IT (real-time tracking, time savings, DMS/ERP integration)
- Provides direct action buttons: **Get more information**, **Contact sales**, and **Support**
- Links to [invig.no/nokkelsporingssystem](https://invig.no/nokkelsporingssystem) for full product details

The page is optimised for mobile, loads instantly, and requires no authentication.

---

## Tag identity: GS1 GIAI

Each physical tag is identified by a **GIAI (Global Individual Asset Identifier)** — a GS1 standard for uniquely identifying individual assets across organisations and systems. The GIAI is encoded directly into the QR code or NFC record on the tag.

Example GIAI for an Invig-managed asset:

```
GIAI  70735391257
```

The GS1 Application Identifier (AI) for GIAI is `8004`, which means the full GS1 element string is:

```
(8004) 70735391257
```

### Redirect flow

Invig operates a **GS1 Digital Link** resolver at `id.invig.no`. When a tag is scanned, the encoded URL follows the GS1 Digital Link URI syntax:

```
https://id.invig.no/8004/<GIAI>
```

**Example:**

```
https://id.invig.no/8004/70735391257
```

The resolver at `id.invig.no` redirects to this landing page, passing the GIAI as a query parameter so the page can look up and display asset-specific information:

```
https://westersnik.github.io/Keyfinder/?giai=70735391257
```

This architecture follows the [GS1 Digital Link standard](https://ref.gs1.org/standards/digital-link/uri-syntax/), which allows the same physical tag to resolve to different endpoints depending on context (consumer-facing, B2B, internal systems) via content negotiation at the resolver level.

---

## Planned: Dynamic content based on GIAI

The next evolution of this page is to make it **fully dynamic** — on page load, the frontend reads the `giai` query parameter and queries the Invig Bifrost API to retrieve the asset record. The page then renders asset-specific content without requiring a separate website per customer or object type.

### Dynamic fields per asset

| Field | Description |
|---|---|
| `asset_type` | Determines icon, label and copy (key, tool, vehicle, equipment, device, etc.) |
| `asset_name` | Human-readable name, e.g. "BMW 5-series – reg. AB12345" |
| `customer_name` | The organisation that owns the asset |
| `customer_logo` | Optional logo URL for white-label presentation |
| `location_hint` | Last known location or zone from the BLE gateway network |
| `assigned_to` | Current responsible person or department |
| `status` | Active, checked out, missing, maintenance, etc. |
| `contact_url` | Customer-specific support or contact link |

---

## Multi-customer, multi-object support

Because all content is driven by the GIAI rather than hardcoded into the page, the same deployment serves entirely different experiences depending on who scanned what:

- A **car dealership** scanning a vehicle key sees the vehicle registration, current handler, and a link to their DMS record in Automaster or Keyloop.
- A **construction company** scanning a power tool sees the tool name, last service date, and a link to their ERP entry in SAP.
- A **hospital** scanning a medical device sees the device ID, maintenance status, and a contact link to their biomedical engineering team.
- A **logistics operator** scanning a pallet tag sees the shipment ID, destination, and estimated handover time.

The page adapts its headline, icon, colour accent, and call-to-action links to match the asset type and customer branding — all from a single codebase and a single hosted URL.

---

## Tech stack

| Layer | Technology |
|---|---|
| Frontend | React 19 + TypeScript |
| Styling | Tailwind CSS v4 |
| Bundler | Vite |
| Hosting | GitHub Pages (`gh-pages` branch) |
| Tag identity | GS1 GIAI (AI `8004`) |
| Resolver | `id.invig.no` – GS1 Digital Link redirect |
| Data (planned) | Invig Bifrost API via fetch on page load |
| Tag encoding | QR code or NFC NDEF URI record |

---

## Repository structure

```
/
├── index.html          # Entry point served by GitHub Pages
├── assets/
│   ├── index-*.js      # Bundled React application
│   └── index-*.css     # Compiled Tailwind styles
└── README.md
```

Source code lives in the `main` branch (React + Vite project). The `gh-pages` branch contains only the compiled static output.

---

## Development

```bash
# Install dependencies
pnpm install

# Start local dev server
pnpm dev

# Build for production
pnpm build
```

After building, copy the contents of `dist/public/` to the `gh-pages` branch and push to deploy.

---

## Roadmap

- [x] Static branded landing page with CTA buttons
- [x] GitHub Pages deployment
- [x] GS1 Digital Link resolver at `id.invig.no` (redirect `8004/<GIAI>` → landing page)
- [ ] Dynamic asset lookup by GIAI (`?giai=` query parameter → Bifrost API)
- [ ] Customer-specific branding (logo, accent colour) from API response
- [ ] Multi-object type rendering (keys, tools, vehicles, equipment, devices)
- [ ] "Report found" flow for lost asset recovery
- [ ] Scan event logging (timestamp, approximate location) to Bifrost

---

## Contact

**Invig AS** · [invig.no](https://invig.no) · [sales@invig.no](mailto:sales@invig.no)  
Koppholen 19, 4313 Sandnes · Org. 913 630 726 mva
