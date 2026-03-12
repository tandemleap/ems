# EMT Field Protocols — CLAUDE.md

## Project Overview
Mobile-optimized web app for pre-hospital EMTs to use en route to calls.
Live at: **ems.tandemleap.com** (Vercel, connected to GitHub: tandemleap/ems)

**Purpose:** Beta test with local EMT crew. Local protocols will be uploaded to align app with agency-specific standards.

---

## File Structure
```
/Users/scottgriffiths/emt-protocols/
├── index.html           — Dispatch hub, keyword matcher, protocol grid
├── chest-pain.html      — Chest Pain / ACS
├── respiratory.html     — Respiratory Distress
├── stroke.html          — Stroke / CVA
├── ams.html             — Altered Mental Status
├── trauma.html          — Trauma Assessment
├── abdominal-pain.html  — Abdominal Pain
├── anaphylaxis.html     — Anaphylaxis
├── cardiac-arrest.html  — Cardiac Arrest / CPR
├── diabetic.html        — Diabetic Emergency
├── overdose.html        — Overdose / Toxicology
├── seizure.html         — Seizure
└── pediatrics.html      — Pediatric Emergencies
```

---

## Design Rules (must be consistent across all pages)

### CSS Variables
```css
--red: #c0392b;       --red-light: #fadbd8;
--orange: #d35400;    --orange-light: #fdebd0;
--blue: #1a6da8;      --blue-light: #d6eaf8;
--green: #1e8449;     --green-light: #d5f5e3;
--yellow: #b7950b;    --yellow-light: #fef9e7;
--purple: #7d3c98;    --purple-light: #e8daef;
--gray-50/#f8f9fa  --gray-100/#f1f3f4  --gray-200/#e0e0e0
--gray-400/#9e9e9e  --gray-600/#616161  --gray-800/#212121
```

### Step Boxes
`.step` + `.step-red/.step-orange/.step-blue/.step-green/.step-yellow/.step-purple`
Padding: `14px 14px 14px 48px` — `.step-num` is absolute-positioned circle left:10px

### Collapsible Steps (Scene Size-Up, SAMPLE History, OPQRST are collapsed by default)
```html
<div class="step step-red collapsed">
  <div class="step-num">1</div>
  <div class="step-title step-toggle" onclick="toggleStep(this)">Title <span class="step-chevron">&#9660;</span></div>
  <div class="step-content">...</div>
</div>
```
```css
.step-toggle { cursor:pointer; user-select:none; -webkit-user-select:none; display:flex; justify-content:space-between; align-items:center; }
.step-chevron { font-size:11px; flex-shrink:0; margin-left:8px; transition:transform 0.2s; opacity:0.6; }
.step.collapsed .step-content { display:none; }
.step.collapsed .step-chevron { transform:rotate(-90deg); }
```
```js
function toggleStep(el) { el.closest('.step').classList.toggle('collapsed'); }
```

### Diamond Decision Boxes
```css
clip-path: polygon(28px 0%, calc(100% - 28px) 0%, 100% 50%, calc(100% - 28px) 100%, 28px 100%, 0% 50%);
min-width: 280px; min-height: 100px; padding: 20px 50px;
display: flex; align-items: center; justify-content: center;
```

### BLS/ALS Badges
```html
<span class="badge-bls">BLS</span>
<span class="badge-als">ALS</span>
```

### Key Layout Rules
- Every page is fully self-contained (all CSS inline in `<style>`) — no external files
- No CDN dependencies — 100% offline capable
- System fonts only: `-apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif`
- Touch targets: min-height 44px on all interactive elements
- Vanilla JS only — no libraries
- Back button in header links to `index.html`
- Footer: "Clinical Decision Support Only — Always follow local medical direction"

---

## Wisconsin / Local Protocol Notes

### Confirmed Scope of Practice
| Intervention | BLS | ALS |
|---|---|---|
| Glucagon 1mg IM | ✅ BLS | — |
| Midazolam | ❌ Not BLS | ✅ ALS only |
| D50W IV | ❌ Not BLS | ✅ ALS only |
| Epinephrine 1:1000 IM (anaphylaxis) | ✅ BLS | — |
| Naloxone IN/IM | ✅ BLS | — |
| Oral glucose | ✅ BLS | — |
| Aspirin 324mg | ✅ BLS | — |

### Pending Local Protocol Upload
User will upload local agency protocols for full alignment. Items to verify:
- BGL threshold for hypoglycemia treatment (60 vs 70 vs 80 mg/dL)
- Albuterol — BLS or ALS at this agency
- Nitroglycerin — BLS-assist or ALS only
- Epinephrine — auto-injector only or can draw from vial
- Spinal motion restriction criteria
- Termination of resuscitation criteria
- Transport destination protocols (stroke center, trauma center)

### Reference Document
WI DHS Protocol: https://www.dhs.wisconsin.gov/publications/p02875a.pdf (PDF is encrypted — cannot be read programmatically; must open manually)

---

## index.html Architecture

### Dispatch Keyword Matcher
`protocols[]` array — each entry has `keywords[]`, `label`, `href`, `color`, `icon`
Input text is scored by substring match count → top matches shown as chips

### Category Filter Tabs
All | Cardiac | Respiratory | Neuro | Trauma | Medical | Pediatric
Protocol cards have `data-category` attribute

### Quick-Access Row
Currently: Cardiac Arrest, Chest Pain/ACS, Anaphylaxis, Altered Mental Status

---

## Git / Deployment
- Repo: git@github.com:tandemleap/ems.git
- Branch: main
- Auto-deploys to Vercel on push
- Custom domain: ems.tandemleap.com (CNAME: c49f2ca11cd1e16f.vercel-dns-017.com)
- DNS: Namecheap (nameservers: dns1/dns2.registrar-servers.com)
