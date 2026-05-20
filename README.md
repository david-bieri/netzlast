# Netzlast — Data Center Investment: Virginia & Maryland

**Interactive policy map of 13 major announced data center deals and ~35 established operational campuses across Virginia and Maryland, with utility service territory overlays, PJM transmission infrastructure, and six analytical panels.**

🗺️ **[Live dashboard →](https://david-bieri.github.io/netzlast/)**
📖 **[User guide →](https://david-bieri.github.io/netzlast/guide.html)**

---

## Overview

*Netzlast* (German: grid load) maps the collision between data center capital — the physical infrastructure of AI — and the grid, regulatory, and governance systems of Virginia and Maryland. Virginia hosts roughly 35% of global data center capacity and is navigating the contested NextEra–Dominion Energy merger, a $1.9B/yr sales tax exemption under legislative pressure, and a PJM transmission grid not designed for gigawatt-scale point loads.

The dashboard supports the research project **"Who Pays for AI's Power Appetite? Virginia as the First AI Resource State"** — School of Public and International Affairs, Virginia Tech.

---

## Files

| File | Description |
|---|---|
| `index.html` | Full dashboard — single self-contained HTML file, no build step |
| `guide.html` | 14-section user guide covering all features, data fields, and methodology |
| `MAINTENANCE.md` | Data update routines, field checklist, CHORO/REGSCORE sync guide, deployment |
| `README.md` | This file |

---

## Map features

**Deal markers** encode four variables simultaneously: outer ring colour = serving utility (Dominion blue / BGE purple / Pepco dashed brown / REC dotted green / AEP dashed red), circle fill = deal status (Active / Dead / Contested / Announced), lower-left dot = project type, upper-right dot = state (VA / MD). Circle size scales with investment ($B, √-scaled). Click any marker to open the full detail card.

**Toggleable layers**

| Button | Layer |
|---|---|
| ▦ Utility areas | County-level utility service territories (Dominion, BGE, Pepco, REC, AEP, NOVEC, MD co-ops) |
| ⬡ Density | NoVA 300+ data center cluster zones with facility count labels |
| ◇ Established | ~35 operational campuses (hover for operator and MW) |
| ⚡ Grid lines | 7 transmission segments: 500kV existing/new, 230kV new, MPRP proposed (hover for SCC case) |
| ▨ Reg. zones | Active data center moratoria (PG Co. MD, Montgomery Co. MD) and contested zoning (Prince William Co. VA) |
| ◉ Risk map | County choropleth shaded by aggregate regulatory flashpoint score (0–18) |

**Filters** — Deal status / State / Utility / Project type — combine with AND logic and update Panels A and B in real time.

---

## Analytical panels

| Panel | Title | Interactive controls |
|---|---|---|
| A | Capacity by utility & project type | MW ↔ $B toggle · click utility bar to set filter |
| B | Project pipeline (Gantt) | Sort by Announced / Online / MW / Investment · year-range zoom presets · click bar → pan map |
| C | Investment vs. capacity scatter | Inv vs. MW ↔ $/MW rank axis swap |
| D | Fiscal exposure & tax exemption table | Click any column header to sort |
| E | PUE benchmarks by type | VA DCSA 1.2 threshold line · Show estimated toggle |
| F | Regulatory flashpoint matrix | Click any column header to sort by that dimension |

---

## Project typology

| Type | Description | Colour |
|---|---|---|
| Hyperscale (own-operate) | Cloud provider builds and operates for own workloads | Deep blue |
| AI / GPU cluster | Explicitly designed for high-density AI training loads | Deep red |
| Wholesale colo | Developer builds to spec under long-term wholesale lease | Teal-green |
| Multi-developer campus | Multi-building campus, consortium or partnership | Purple |
| Speculative / no tenant | Announced but no committed anchor tenant | Warm grey |

---

## Regulatory flashpoint matrix

Scores each deal 0–3 on six dimensions (Zoning / Grid / Rate regulation / Environmental / Tax / Community), producing an aggregate 0–18 risk score. Sorted by total score descending by default; click any column header to sort by that dimension.

Top-scoring deals as of May 2026: PW Digital Gateway (13), Brightseat Tech Park (13), Dickerson / Montgomery Co. (13), Berry Hill (10), STACK / Stafford (9).

---

## Data model

Each deal object contains 27 fields. Key additions beyond standard deal metadata:

| Field | Description |
|---|---|
| `typ` | Project typology (hyperscale / ai-dense / wholesale / campus / speculative) |
| `mwNum` | Numeric capacity in MW (estimated values flagged with `mwEst:true`) |
| `cooling` | Cooling type (air / hybrid / liquid / closed-loop / reclaimed) |
| `pue` | Power Usage Effectiveness — disclosed only for AWS (1.15) and Google (1.12) |
| `substation` | Grid interconnection status (signed / conditional / pending / partial / na) |
| `opposition` | Community opposition level (none / low / petition / legal / moratorium) |
| `tax_exempt` | VA DCSA status (va / va-risk / md / na) |
| `yr_ann` | Year announced |
| `yr_online` | Projected year of initial operation |

Full glossary in the [user guide](https://david-bieri.github.io/netzlast/guide.html#glossary).

---

## Key data corrections (documented)

- **Berry Hill** utility corrected from Dominion → **AEP** (Appalachian Power): land sale contingent on AEP guaranteeing 299 MW by end 2028.
- **EdgeCore / Louisa** investment corrected from $42M (land only) → **$17B+** total announced investment.
- **Quantum Frederick** capacity corrected to **2,400 MW** (full 2.4 GW campus per Frederick County EDA).

---

## Sources

Local news (WSLS, Virginia Business, Virginia Mercury, Baltimore Banner, Maryland Matters), trade press (Data Center Dynamics, Bisnow, Commercial Observer, Data Center Frontier), regulatory filings (Virginia SCC transmission register, MD PSC Case 9773 / MPRP, PG Co. planning records), company disclosures (EdgeCore Jun 2025, STACK Jan 2025, TPG/Quantum filings), Dominion Energy 2024 IRP, PJM load forecast 2025, VEDP Biennial DCRSUT Report (Jan 2026), JLARC 2024 data center report.

Data reflects public information available as of **May 2026**.

---

## Technical stack

- **D3.js v7.8.5** — all map rendering, SVG panels, and data joins
- **TopoJSON v3.0.2** — county and state boundary topology
- **us-atlas v3** (CDN) — US counties TopoJSON at 1:10M scale
- No build step, no bundler, no framework. Single HTML file deployable to any static host.

---

## Citation

```
Bieri, David (2026). Data Center Investment: Virginia & Maryland — Interactive Policy Map.
School of Public and International Affairs, Virginia Tech. Blacksburg, VA.
https://david-bieri.github.io/netzlast/
```

---

## Contact

David Bieri · Associate Professor of Regional Economics and Economic Geography  
School of Public and International Affairs, Virginia Tech  
[bieri@vt.edu](mailto:bieri@vt.edu)  
Research: *"Who Pays for AI's Power Appetite? Virginia as the First AI Resource State"*

---

*Last updated: May 2026*
