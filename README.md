# Fit Finder — Maggie Glaize

A consultation tool for finding storage bins that fit a measured space. Maggie measures a space at a client's house, enters the dimensions, and the app shows which bins from IKEA / Container Store / Target will fit — with a proportional diagram showing how many tile across and how they look from the side.

## Project context

- **Who uses it:** Maggie Glaize (professional organizer), on her iPad during and after client consultations
- **Built by:** Natalya, via Claude Code
- **Design reference:** maggieglaizeorganize.com — warm cream palette, serif headings, minimal UI
- **Status:** MVP complete, awaiting Maggie's feedback before next phase

---

## How to run locally

Both devices must be on the same WiFi:

```bash
cd ~/Documents/fitfinder
python3 -m http.server 8080
```

Find the Mac's local IP: System Settings → Wi-Fi → Details next to the network name.  
Open `http://[local-ip]:8080` in Safari on the iPad.

---

## Files

- `index.html` — the entire app: all HTML, CSS, JS, and product catalog in one file
- `products.csv` — reference copy of the catalog (not used by the app, kept for editing convenience)

---

## Product catalog

Products live as a JavaScript array inside `index.html`. There is no CSV loading in the app — products are maintained in the code.

**To add or edit a product**, find the `var products=[...]` array in the script and add an entry:

```javascript
{
  name: "Product name",
  store: "IKEA",                // must be: IKEA | The Container Store | Target
  width: 13, depth: 15, height: 12,
  unit: "in",                   // in | cm
  price: "$6.99",
  material: "Fabric",
  soldAs: 1,                    // omit or set to 1 if sold individually; set to e.g. 6 if sold as a pack
  url: "https://..."
}
```

**Pack products (`soldAs`):** If a bin is sold in a set (e.g. SKUBB comes in a set of 6), add `soldAs: 6`. The app will show a "Set of 6" tag on the card and calculate how many sets to buy for the width.

---

## Offline requirement

The app must work fully offline at client sites. Product links (IKEA / Target / Container Store URLs) are the only accepted internet dependency.

**Current status:** Fully offline-capable. Google Fonts removed — app uses system fonts (Georgia for headings, San Francisco for UI). No external resources loaded.

**Deployment plan:** Host on domain (maggieglaizeorganize.com) + service worker for offline caching. Flow: load once at home → add to iPad Home Screen → works offline everywhere from the icon. Hosting type still TBD (need to know: Squarespace, WordPress, cPanel?).

---

## Upcoming: Cart + Share feature

**Goal:** Replace Maggie's current Google Sheet output workflow.

- During consultation: add bins to a running list, labeled by space ("Master closet drawer", "Kitchen pantry shelf"), with quantity and running total
- After consultation: tap Share → generates a branded HTML summary → sent via iOS share sheet (email to client)
- The client summary includes: space labels, product names, dimensions, store, price, clickable product links
- Install day: same list used as a checklist, items tapped to check off as placed

**Design step before building:** Natalya will draft the client output format in Claude (claude.ai) using Maggie's existing Google Sheet as a reference, then paste the brief into Claude Code to build.

---

## Design notes

- **Colors:** cream `#F5F2EC`, sage `#7A8B7F`, taupe `#C4B9AA`, warm white `#FDFBF7`
- **Fonts:** Georgia (headings, product names) + system sans `-apple-system` (UI labels) — Google Fonts removed for offline support. Embed Cormorant Garamond + Jost as base64 once hosted on HTTPS (service worker will cache them).
- **Keep it minimal** — consultation tool, not a consumer app
- **Target screen:** iPad landscape (1024×768), works portrait too
- **Single file rule:** keep everything in `index.html`. No build step, no dependencies.
