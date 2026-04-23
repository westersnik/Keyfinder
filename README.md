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

## Planned: Dynamic content based on tag ID

The next evolution of this page is to make it **fully dynamic** — each tag carries a unique ID (embedded in the QR/NFC URL as a query parameter, e.g. `?id=A-042`), and the page will use that ID to fetch and display asset-specific information at scan time.

This enables a single hosted page to serve as the scan destination for **any asset type** across **any customer**, without requiring a separate website per use case.

### How it will work

The tag URL will follow the pattern:

```
https://westersnik.github.io/Keyfinder/?id=<TAG_ID>
```

On page load, the frontend reads the `id` parameter and queries the Invig Bifrost API (or a lightweight edge function) to retrieve the asset record. The page then renders the appropriate content for that specific asset and customer context.

### Dynamic fields per asset

| Field | Description |
|---|---|
| `asset_type` | Determines which icon, label and copy to display (key, tool, vehicle, equipment, etc.) |
| `asset_name` | Human-readable name, e.g. "BMW 5-series – reg. AB12345" |
| `customer_name` | The organisation that owns the asset |
| `customer_logo` | Optional logo URL for white-label presentation |
| `location_hint` | Last known location or zone from the BLE gateway network |
| `assigned_to` | Current responsible person or department |
| `status` | Active, checked out, missing, maintenance, etc. |
| `contact_url` | Customer-specific support or contact link |

---

## Multi-customer, multi-object support

Because all content is driven by the tag ID rather than hardcoded into the page, the same deployment can serve entirely different experiences depending on who scanned what:

- A **car dealership** scanning a vehicle key sees the vehicle registration, current handler, and a link to their DMS record in Automaster or Keyloop.
- A **construction company** scanning a power tool sees the tool name, last service date, and a link to their ERP entry in SAP.
- A **hospital** scanning a medical device sees the device ID, maintenance status, and a contact link to their biomedical engineering team.
- A **logistics operator** scanning a pallet tag sees the shipment ID, destination, and estimated handover time.

The page adapts its headline, icon, colour accent, and call-to-action links to match the asset type and customer branding — all from a single codebase.

---

## Tech stack

| Layer | Technology |
|---|---|
| Frontend | React 19 + TypeScript |
| Styling | Tailwind CSS v4 |
| Bundler | Vite |
| Hosting | GitHub Pages (`gh-pages` branch) |
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
- [ ] Dynamic asset lookup by tag ID (`?id=` query parameter)
- [ ] Customer-specific branding (logo, accent colour) from API response
- [ ] Multi-object type rendering (keys, tools, vehicles, equipment, devices)
- [ ] "Report found" flow for lost asset recovery
- [ ] Scan event logging (timestamp, approximate location) to Bifrost

---

## Contact

**Invig AS** · [invig.no](https://invig.no) · [sales@invig.no](mailto:sales@invig.no)  
Koppholen 19, 4313 Sandnes · Org. 913 630 726 mva
