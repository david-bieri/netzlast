# Netzlast — Maintenance & Data Update Guide

Internal reference for keeping the dashboard current. The dashboard is a single self-contained HTML file (`index.html`); all data lives as JavaScript constants inside it. There is no database, no API, and no build step.

---

## 1. Data architecture at a glance

All data is in the `<script>` block of `index.html`. The constants that require periodic review:

| Constant | What it holds | Update frequency |
|---|---|---|
| `DEALS` | 13 announced deal objects (27 fields each) | When deal status changes or new deal announced |
| `REGSCORE` | 6-dimension regulatory risk scores per deal | When opposition, zoning, or grid status changes |
| `CHORO` | County FIPS → max risk score (drives ◉ Risk map) | Whenever REGSCORE changes |
| `MORT_COUNTIES` | Active moratoria and contested zoning counties | When new moratorium enacted or lifted |
| `GRID_LINES` | 7 transmission line segments | When SCC case status changes |
| `SUBSTATIONS` | Named substation locations | When new substations approved/built |
| `DENSITY` | NoVA cluster zone labels and counts | Annually, when new facility counts published |
| `ESTABLISHED` | ~35 operational campus markers | When major new campuses open |
| `PUE_REF` | PUE benchmark ranges by project type | When new operator disclosures made |

`guide.html` and `README.md` may also need updates when features or data change significantly.

---

## 2. Trigger events

**Update immediately:**
- A deal changes status (Active → Contested, Contested → Dead, Announced → Active)
- A county enacts or lifts a data center moratorium
- A major deal is announced that should enter the dataset
- A court ruling changes zoning status

**Review quarterly:**
- Deal capacity or investment figures revised in press/SEC filings
- PJM interconnection queue status changes (check PJM CEII public queue)
- SCC transmission case status updates (check scc.virginia.gov)
- MD PSC MPRP case status (check psc.state.md.us)
- Operator PUE disclosures (annual sustainability reports, typically Q1–Q2)

**Review annually:**
- NoVA facility counts for DENSITY zones (sources: Baxtel.com, DCD market reports)
- ESTABLISHED campus inventory
- `yr_online` projections as construction progresses

---

## 3. Updating a deal — field checklist

When a deal changes, run through all 27 fields. The ones most likely to need updating:

```
s           → deal status (Active / Dead / Contested / Announced)
cp          → capacity string (e.g. "1,800 MW" → "1,800 MW phase 1 online")
tl          → timeline string
tx          → annual tax revenue (when disclosed in county budget documents)
jo          → jobs (when actual hiring announced vs. original promise)
iss         → key issue text (update to reflect current situation)
substation  → 'pending' → 'signed' when ESA executed
opposition  → escalate if petition grows or moratorium enacted
yr_online   → revise if construction delayed or accelerated
```

After updating a deal's fields, check whether **REGSCORE** needs updating (see Section 4) and whether **CHORO** needs updating (see Section 5).

---

## 4. Updating REGSCORE

`REGSCORE` is a hardcoded object: `{ deal_id: [land, grid, rate, env, fiscal, comm] }`.

**Scoring rubric (0 = minimal, 3 = high risk):**

| Dim | 0 | 1 | 2 | 3 |
|---|---|---|---|---|
| land | By-right, no opposition | Standard permit, low opp. | Contested rezoning / petition >1K | Active moratorium or court-voided |
| grid | Signed ESA, operational | Signed ESA, merger uncertainty | Conditional or pending commitment | No commitment; PJM queue backlog |
| rate | Non-Dominion cooperative | BGE/Pepco (stable) | IOU grid; cost-allocation uncertain | Dominion + NextEra merger + contested |
| env | Closed-loop, near-zero WUE | Standard cooling, established site | High water use / community concerns | EJ community + documented harm / coal-site |
| fiscal | VA DCSA confirmed, PUE compliant | VA DCSA eligible or MD standard | VA DCSA at-risk (PUE ≤ 1.2) | MD moratorium or no exemption path |
| comm | No opposition | Low, no formal action | Formal petition (>1,000 sigs) | Legal challenge or government moratorium |

**Example update:** Woodlawn (wl) moves from `Announced` → `Contested` with a community petition. Change `wl.opposition` from `'low'` → `'petition'`, update `wl.s` to `'Contested'`, and update REGSCORE `wl` comm from `1` → `2` (total: 7 → 8).

---

## 5. Updating CHORO after REGSCORE changes

`CHORO` maps county FIPS → the **maximum** REGSCORE total among all deals in that county. It must be kept in sync with REGSCORE manually.

**After any REGSCORE change:**

1. Recompute the total for the changed deal: `sum([land, grid, rate, env, fiscal, comm])`
2. Check if that deal is the current maximum for its county
3. If yes (or if a new deal was added to a county), update the CHORO value

**County → deal mapping** (for quick reference):

| FIPS | County | Deals in county | Current max |
|---|---|---|---|
| 51143 | Pittsylvania Co. VA | bh | 10 |
| 51153 | Prince William Co. VA | pwdg | 13 |
| 51107 | Loudoun Co. VA | aws | 4 |
| 51041 | Chesterfield Co. VA | goog | 4 |
| 51033 | Caroline Co. VA | ca | 2 |
| 51179 | Stafford Co. VA | stst, va4 | 9 (stst) |
| 51177 | Spotsylvania Co. VA | ph95 | 3 |
| 51113 | Louisa Co. VA | ec | 1 |
| 24021 | Frederick Co. MD | qf | 8 |
| 24033 | Prince George's Co. MD | bs | 13 |
| 24005 | Baltimore Co. MD | wl | 7 |
| 24031 | Montgomery Co. MD | dk | 13 |

If a new deal is added in a county already in CHORO, update the CHORO entry if the new total exceeds the current max. If the new deal is in a county *not* in CHORO, add a new FIPS entry.

---

## 6. Updating MORT_COUNTIES

```javascript
const MORT_COUNTIES = {
  '24033': { status:'moratorium', col:'#DC2626', note:'...' },
  '24031': { status:'moratorium', col:'#DC2626', note:'...' },
  '51153': { status:'contested',  col:'#D97706', note:'...' },
};
```

**To add a moratorium:** add a new entry with the county's 5-digit FIPS and `status:'moratorium'`.  
**To lift a moratorium:** remove the entry or change `status` to `'contested'` if zoning remains uncertain.  
**Status values:** `'moratorium'` (red overlay, dashed) or `'contested'` (amber overlay, dashed).

FIPS codes for likely future entries: Loudoun Co. VA = `51107`, Fairfax Co. VA = `51059`, Frederick Co. MD = `24021`, Carroll Co. MD = `24013`.

---

## 7. Adding a new deal

1. Add a new object to the `DEALS` array following the existing schema (27 fields).  
2. Assign a short `id` (e.g. `"ms1"` for a new Maryland site).  
3. Add a corresponding entry to `REGSCORE`.  
4. Add the county FIPS to `CHORO` (or update the existing entry if the county already has a deal and the new total is higher).  
5. If the county has an active moratorium not yet in `MORT_COUNTIES`, add it there.  
6. Check whether the new deal's utility exists in `UR` and `UC`; add it if not.

---

## 8. Verification before publishing

```bash
# Extract script tag and run Node syntax check
python3 -c "
import re
with open('index.html','r') as f: src=f.read()
m=re.search(r'<script>(.*?)</script>',src,re.DOTALL)
open('/tmp/test.js','w').write(m.group(1))"
node --check /tmp/test.js
```

**Visual QA checklist:**
- [ ] New deal marker appears at correct map location
- [ ] Marker ring colour matches the serving utility
- [ ] Detail card shows correct values for all 12 displayed fields
- [ ] Panel A updates when type filter changed to new deal's type
- [ ] Panel B bar appears at correct year positions
- [ ] Panel F row appears with correct score colours
- [ ] Tooltip shows correct opposition and grid status
- [ ] CHORO colours match expected risk level for affected county
- [ ] MORT_COUNTIES overlay visible in correct counties

---

## 9. Deployment

The dashboard deploys automatically via GitHub Pages when `main` branch is updated.

```bash
git add index.html guide.html README.md MAINTENANCE.md
git commit -m "Data update: [brief description] — [Month Year]"
git push origin main
```

GitHub Pages typically reflects changes within 1–3 minutes. Verify at:  
**https://david-bieri.github.io/netzlast/**

---

## 10. Source reference list

Check these sources when reviewing for updates:

| Source | What to check | URL |
|---|---|---|
| Virginia SCC | Transmission case filings, new substations | scc.virginia.gov → search "transmission" |
| Maryland PSC | MPRP case 9773, other DC infrastructure filings | psc.state.md.us |
| PJM queue | Generator interconnection queue, load growth forecasts | pjm.com/planning/new-services-queue |
| Data Center Dynamics | Deal announcements, capacity changes | datacenterdynamics.com |
| Virginia Business | VA economic development deals | virginiabusiness.com |
| Baltimore Banner | MD data center regulatory news | baltimorebanner.com |
| Maryland Matters | MD legislative and county moratorium news | marylandmatters.org |
| Bisnow | Commercial real estate deals, planning filings | bisnow.com |
| VEDP | Virginia economic development incentives | vedp.org |
| JLARC | Virginia data center tax exemption analysis | jlarc.virginia.gov |
| Baxtel | NoVA facility counts for DENSITY zones | baxtel.com/data-centers |
| AWS/Google sustainability reports | PUE disclosures | investor relations pages |

---

## 11. Known limitations to keep in mind

- **CHORO and REGSCORE are separate constants** and must be manually kept in sync. There is currently no automated check that they agree. Run the audit script below if in doubt.
- **County FIPS assignments** for deals are hardcoded in CHORO based on deal coordinates. If a deal relocates within a county or moves to a different county, both the DEALS coordinates and the CHORO FIPS/score need updating.
- **Utility assignments** (`d.u`) are the primary utility serving the site. Some sites (especially in NoVA) sit near cooperative/IOU boundaries — verify with VEDP or the developer's press releases if uncertain.
- **REGSCORE reflects peak historical opposition**, not current trajectory. A deal that received a moratorium and then won a reversal should have its comm score revised downward.

---

## 12. Audit script

Run this to verify CHORO is consistent with REGSCORE after any update:

```python
import re

with open('index.html', 'r', encoding='utf-8') as f:
    src = f.read()

rs_raw = re.search(r'const REGSCORE = \{(.*?)\};', src, re.DOTALL).group(1)
regscore = {}
for m in re.finditer(r"(\w+):\s*\[([^\]]+)\]", rs_raw):
    regscore[m.group(1)] = list(map(int, m.group(2).split(',')))

ch_raw = re.search(r'const CHORO = \{(.*?)\};', src, re.DOTALL).group(1)
choro = {m.group(1): int(m.group(2)) for m in re.finditer(r"'(\d+)':\s*(\d+)", ch_raw)}

# Deal → FIPS mapping (update when new deals added)
deal_fips = {
    'bh':'51143','pwdg':'51153','aws':'51107','goog':'51041',
    'ca':'51033','stst':'51179','ph95':'51177','va4':'51179',
    'ec':'51113','qf':'24021','bs':'24033','wl':'24005','dk':'24031',
}

county_max = {}
for did, fips in deal_fips.items():
    total = sum(regscore.get(did, [0]*6))
    if fips not in county_max or total > county_max[fips]:
        county_max[fips] = total

ok = True
for fips, expected in county_max.items():
    actual = choro.get(fips)
    if actual != expected:
        print(f"MISMATCH {fips}: CHORO={actual}, computed={expected}")
        ok = False
if ok:
    print("All CHORO entries consistent with REGSCORE ✓")
```

---

*Maintained by David Bieri · bieri@vt.edu · School of Public and International Affairs, Virginia Tech*
