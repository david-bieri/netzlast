# Netzlast — Data Center Infrastructure Map

**Interactive policy map: Virginia & Maryland data center investment, utility service territories, and NextEra–Dominion merger exposure**

🗺️ **Live map:** [david-bieri.github.io/netzlast](https://david-bieri.github.io/netzlast)

---

## What this shows

The map visualizes three overlapping stories about AI infrastructure and energy governance in the Mid-Atlantic:

**1. Active deals** — 13 major announced or contested data center investments (May 2026), sized by dollar commitment and colored by status. Circle rings encode the utility provider; a small corner dot indicates state. Click any marker for full project details.

**2. Established campuses** — ~35 named operational facilities across Virginia and Maryland (gray diamonds). Northern Virginia alone has 600+ total data centers; the Ashburn/Sterling density zone represents this concentration.

**3. Utility service territories** — County-level coloring by primary utility. The key analytical dimension: Dominion Energy (light blue) serves nearly the entire eastern half of Virginia, covering almost every active deal. The REC (green) counties where CleanArc and EdgeCore operate are structurally insulated from the NextEra merger. Maryland splits between BGE (Exelon, central MD) and Pepco (Exelon, DC metro) — a separate regulatory universe from Virginia's SCC proceedings.

---

## Analytical context

This map accompanies work on Virginia as the "first AI resource state" — the jurisdiction where AI-scale infrastructure demand is stress-testing a governance architecture built for a different era.

The **NextEra–Dominion merger** (announced May 18, 2026) is the structuring event. A $67 billion all-stock deal that would create the world's largest regulated electric utility, it requires approval from Virginia's SCC, FERC, the NRC, and state commissions in North Carolina and South Carolina. The utility service territory overlay makes the merger's geographic footprint — and its limits — immediately visible.

The **Berry Hill / STACK Infrastructure deal** (approved the same day, Pittsylvania County/Danville City) represents one of the largest industrial announcements in U.S. history at $100B over 30 years, in a region with essentially no existing data center infrastructure. Its location in the southwestern corner of the Dominion service territory, far from any established density zone, captures the core infrastructure governance challenge.

Related work: *"Who Pays for AI's Power Appetite? Virginia as the First AI Resource State"* — David Bieri, School of Public and International Affairs, Virginia Tech.

---

## Deploy to GitHub Pages

1. Push this repository to GitHub (the repo is already configured)
2. Go to **Settings → Pages → Source** → select `main` branch, `/ (root)` folder
3. Click **Save**
4. Your map will be live at `https://david-bieri.github.io/netzlast/` within a few minutes

The `.nojekyll` file prevents GitHub's Jekyll processor from interfering with the HTML.

No build step, no dependencies to install. The page is a single self-contained HTML file that loads D3 and TopoJSON from CDN at runtime.

---

## Data sources

| Layer | Sources |
|---|---|
| Announced deals | WSLS, VEDP, MD Commerce, SEC filings, court filings (May 2026) |
| Established campuses | Baxtel, DataCenterMap, operator disclosures |
| Utility territories | EIA, VEDP, individual utility disclosures; county-level approximations |
| County/state boundaries | US Atlas TopoJSON (Mike Bostock / Observable) |
| Deal details | Data Center Dynamics, Bisnow, Baltimore Banner, Maryland Matters, Virginia Mercury |

Utility service territories are approximate county-level assignments. Many utilities serve portions of counties; cooperative boundaries are especially irregular. Established campus coordinates are approximate based on public addresses and industry databases.

---

## Tech stack

- [D3.js v7](https://d3js.org/) — map projection, zoom, SVG rendering
- [TopoJSON](https://github.com/topojson/topojson) — topology parsing
- [US Atlas](https://github.com/topojson/us-atlas) — county and state boundaries

No framework. No build toolchain. Single HTML file.

---

## Author

**David Bieri**  
Associate Professor, School of Public and International Affairs  
Virginia Tech · Blacksburg, Virginia  
[dbieri@vt.edu](mailto:dbieri@vt.edu)

*This map is a research and journalism resource. It does not represent the official views of Virginia Tech.*
