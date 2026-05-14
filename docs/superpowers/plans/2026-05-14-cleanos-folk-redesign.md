# CleanOS Folk-Style Redesign — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Complete visual rewrite of CleanOS demo dashboard from warm-orange card-based layout to Folk CRM-inspired minimal black/white table-centric design.

**Architecture:** Single-file HTML app (`index.html`). All CSS inline in `<style>`. All JS inline in `<script>`. No build step. CDNs: Inter (Google Fonts), Chart.js v4, Lucide Icons. Deployed on Railway via `npx serve`.

**Tech Stack:** HTML, inline CSS, vanilla JS, Chart.js v4 (only for Auswertungen view), Lucide Icons (sidebar), Google Fonts (Inter)

**Reference:** Approved mockup at `.superpowers/brainstorm/80610-1778770538/content/dashboard-mockup-v2.html` — use this as the CSS/structure foundation. Spec at `docs/superpowers/specs/2026-05-14-cleanos-folk-redesign.md`.

**Important constraints:**
- NO Tailwind CDN (current version uses it — remove it, use plain CSS like the mockup)
- NO cards, no card-grids, no hero KPI layouts
- NO charts except on Auswertungen page
- Color ONLY in status badges and team-tags
- Each view = full-width table as main content element

---

### Task 1: Foundation — HTML head, CSS design system, sidebar, topbar, navigation JS

**Files:**
- Create: `index.html` (complete rewrite — replaces existing file)

This task creates the complete shell: `<head>` with fonts/CDNs, all CSS classes from the design system, the sidebar, topbar, empty view containers for all 8 views, and the JS navigation function. After this task, clicking sidebar items switches views (showing empty placeholders).

- [ ] **Step 1: Write the complete HTML shell with CSS + sidebar + topbar + empty views + JS**

Write the full `index.html` with:

**`<head>`:**
- Google Fonts: Inter (400, 500, 600, 700)
- Lucide Icons CDN
- Chart.js v4 CDN (for Auswertungen only)
- NO Tailwind CDN

**`<style>`:** Copy ALL CSS from the approved mockup (`dashboard-mockup-v2.html`). This includes: `.app`, `.sidebar`, `.s-*`, `.main`, `.topbar`, `.bc`, `.tb-*`, `.content`, `.page-*`, `.btn-*`, `.stats-row`, `.stat`, `.tabs`, `.tab`, `.tbl`, `.cell-*`, `.tag-*`, `.dot-*`, `.team-tag`. Add these additional classes needed for other views:

```css
/* View visibility */
.view { display: none; }
.view.active { display: block; }

/* Gantt-specific (Einsatzplanung) */
.gantt-row { display: flex; border-bottom: 1px solid #f5f5f5; }
.gantt-row:last-child { border-bottom: none; }
.gantt-label { width: 140px; flex-shrink: 0; padding: 14px 16px; border-right: 1px solid #eee; }
.gantt-track { flex: 1; position: relative; min-height: 48px; padding: 6px 0; }
.gantt-bar { position: absolute; top: 8px; bottom: 8px; border-radius: 4px; display: flex; align-items: center; padding: 0 8px; font-size: 11px; font-weight: 500; color: #fff; background: #1a1a1a; overflow: hidden; white-space: nowrap; }
.gantt-bar-70 { background: rgba(26,26,26,0.7); }
.gantt-bar-40 { background: rgba(26,26,26,0.4); }
.gantt-time-header { display: flex; }
.gantt-time-header > div { flex: 1; text-align: center; font-size: 10px; color: #aaa; padding: 6px 0; border-left: 1px solid #f0f0f0; }

/* Auswertungen chart area */
.chart-container { padding: 0 24px 24px; }
.chart-wrap { max-width: 600px; height: 200px; }

/* Analytics text rows */
.metric-row { display: flex; align-items: center; justify-content: space-between; padding: 8px 24px; border-bottom: 1px solid #f5f5f5; }
.metric-row:last-child { border-bottom: none; }
.metric-label { font-size: 13px; color: #1a1a1a; }
.metric-val { font-size: 13px; font-weight: 600; color: #1a1a1a; }
.metric-change { font-size: 11px; margin-left: 8px; }

/* Progress bar (Auswertungen only) */
.pbar { display: flex; align-items: center; gap: 10px; padding: 6px 24px; }
.pbar-label { font-size: 12px; color: #1a1a1a; width: 140px; flex-shrink: 0; }
.pbar-track { flex: 1; height: 4px; background: #f0f0f0; border-radius: 2px; overflow: hidden; }
.pbar-fill { height: 100%; border-radius: 2px; }
.pbar-fill-green { background: #22c55e; }
.pbar-fill-red { background: #ef4444; }
.pbar-val { font-size: 11px; font-weight: 600; width: 40px; text-align: right; flex-shrink: 0; }

/* Section divider for Auswertungen */
.section-label { font-size: 11px; font-weight: 500; color: #aaa; text-transform: uppercase; letter-spacing: 0.3px; padding: 20px 24px 8px; }

/* Row highlights */
.row-active td { background: #fffbeb !important; }
.row-overdue td { background: #fef2f2 !important; }

/* Avatar circle */
.avatar { width: 28px; height: 28px; border-radius: 50%; background: #f0f0f0; display: flex; align-items: center; justify-content: center; font-size: 10px; font-weight: 600; color: #888; flex-shrink: 0; }
```

**Sidebar HTML:** Exact structure from spec — CleanOS logo, Search, Notifications (3), grouped nav (Betrieb: Heutige Einsätze/Objekte/Aufträge/Einsatzplanung, Verwaltung: Kunden/Mitarbeiter/Rechnungen/Auswertungen, Settings), user footer (ML, Markus Lehmann, Geschäftsführer). Each nav item has `onclick="navigate('viewId')"` and `data-view="viewId"`.

**Topbar HTML:** Breadcrumb left (`<span id="bc-title">Heutige Einsätze</span> / <span id="bc-date"></span>`), search input + bell icon + dots icon right.

**View containers:** 8 `<div>` elements with IDs `view-einsaetze` (class `view active`), `view-objekte`, `view-auftraege`, `view-kunden`, `view-mitarbeiter`, `view-planung`, `view-rechnungen`, `view-auswertungen` (all class `view`). Each contains a placeholder paragraph: `<!-- Content added in Task N -->`.

**JavaScript:**
```javascript
// Dynamic date
const d = new Date();
const days = ['Sonntag','Montag','Dienstag','Mittwoch','Donnerstag','Freitag','Samstag'];
const months = ['Jan','Feb','Mär','Apr','Mai','Jun','Jul','Aug','Sep','Okt','Nov','Dez'];
document.getElementById('bc-date').textContent = days[d.getDay()] + ', ' + d.getDate() + '. ' + months[d.getMonth()] + ' ' + d.getFullYear();

// View titles for breadcrumb
const viewTitles = {
  einsaetze: 'Heutige Einsätze',
  objekte: 'Objekte',
  auftraege: 'Aufträge',
  kunden: 'Kunden',
  mitarbeiter: 'Mitarbeiter',
  planung: 'Einsatzplanung',
  rechnungen: 'Rechnungen',
  auswertungen: 'Auswertungen'
};

function navigate(viewId) {
  document.querySelectorAll('.view').forEach(v => v.classList.remove('active'));
  document.getElementById('view-' + viewId).classList.add('active');
  document.querySelectorAll('.s-item[data-view]').forEach(i => i.classList.remove('active'));
  const btn = document.querySelector('[data-view="' + viewId + '"]');
  if (btn) btn.classList.add('active');
  document.getElementById('bc-title').textContent = viewTitles[viewId] || viewId;
  lucide.createIcons();
  if (viewId === 'auswertungen' && !window._chartInit) initCharts();
}

lucide.createIcons();
```

- [ ] **Step 2: Open in browser and verify**

Run: `open /Users/alexispittadakis/clima-demo/index.html`

Expected: White sidebar with grouped navigation, topbar with breadcrumb, clicking nav items switches views (showing empty content areas). All styling matches Folk aesthetic.

- [ ] **Step 3: Commit**

```bash
cd /Users/alexispittadakis/clima-demo
git add index.html
git commit -m "refactor: complete rewrite — Folk-style foundation with sidebar, topbar, navigation

Replaces warm-orange card-based dashboard with Folk CRM-inspired
minimal design. White sidebar, black/white palette, Inter font.
8 empty view containers with JS navigation ready.

Co-Authored-By: Claude Opus 4.6 (1M context) <noreply@anthropic.com>"
```

---

### Task 2: View 1 — Heutige Einsätze (Landing Page)

**Files:**
- Modify: `index.html` — replace placeholder in `#view-einsaetze`

- [ ] **Step 1: Add the Heutige Einsätze view content**

Inside `<div id="view-einsaetze" class="view active">`, add:

1. **Page header:** Title "Heutige Einsätze", buttons "+ Einsatz planen" (primary) + "↓ Export" (secondary)
2. **Stats line:** "6 Einsätze geplant · 8 Teams im Einsatz · 21 Std. geplant · 1 überfällig" (using `.stat` classes, `<b>` for numbers, `.down` for überfällig)
3. **Tabs:** "Tagesansicht" (active) | "Wochenplan" | "Alle Einsätze"
4. **Table** with columns: Zeit, Objekt, Reinigungsart, Team, Mitarbeiter, Dauer, Status

**Table data (7 rows):**

| Zeit | Objekt | Adresse | Art | Team | Farbe | MA | Dauer | Status |
|------|--------|---------|-----|------|-------|----|-------|--------|
| 07:00 | Bürokomplex Alexanderplatz | Alexanderstr. 12, Berlin | Unterhaltsreinigung | Team A | #22c55e | M. Kowalski +3 | 4 Std. | Erledigt (tag-green) |
| 08:30 | Arztpraxis Dr. Weber | Schönhauser Allee 45, Berlin | Grundreinigung | Team B | #3b82f6 | T. Bergmann +1 | 2 Std. | Erledigt (tag-green) |
| 09:00 | Einkaufszentrum CityMall | Friedrichstr. 88, Berlin | Unterhaltsreinigung | Team C | #f59e0b | L. Fernandez +4 | 5 Std. | Läuft (tag-yellow) — **row-active** |
| 11:00 | Wohnanlage Parkstraße 12–16 | Parkstr. 12-16, Berlin | Treppenhausreinigung | Team D | #a855f7 | P. Schulz +2 | 3 Std. | Geplant (tag-gray) |
| 14:00 | Hotel Residenz Berlin | Kurfürstendamm 25, Berlin | Zimmerreinigung | Team E | #ec4899 | J. Nowak +5 | 5 Std. | Geplant (tag-gray) |
| 16:00 | Fitnessstudio PowerGym | Torstr. 99, Berlin | Grundreinigung | Team B | #3b82f6 | T. Bergmann +1 | 2 Std. | Geplant (tag-gray) |
| Fällig | WohnBau AG — Fensterreinigung | Prenzlauer Berg · seit 08.05. | Fensterreinigung | Team D | #a855f7 | — | — | Überfällig (tag-red) — **row-overdue** |

Row for 09:00 (CityMall): add class `row-active`. Zeit cell: `font-weight:600; color:#1a1a1a`.
Row for Fällig: add class `row-overdue`. Zeit cell: `color:#dc2626; font-weight:500`.

- [ ] **Step 2: Verify in browser**

Expected: Full table fills the viewport. Active row has subtle yellow bg. Overdue row has subtle red bg at bottom. Stats line is subtle text, not KPIs.

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: add Heutige Einsätze view — Folk-style table with status badges"
```

---

### Task 3: View 2 — Objekte + View 3 — Aufträge

**Files:**
- Modify: `index.html` — replace placeholders in `#view-objekte` and `#view-auftraege`

- [ ] **Step 1: Add Objekte view**

Page header: "Objekte", stats "84 Objekte · 6 pausiert · 2 gekündigt", tabs "Alle | Aktiv | Pausiert", buttons "+ Neues Objekt" (primary) + "Filter" (secondary) + "Import" (secondary).

**Table columns:** Objekt (name+address), Typ, Intervall, Team, Nächster Einsatz, Vertragswert, Status.

**8 rows of data:**

| Objekt | Adresse | Typ | Intervall | Team | Farbe | Nächster | Wert | Status |
|--------|---------|-----|-----------|------|-------|----------|------|--------|
| Bürokomplex Alexanderplatz | Alexanderstr. 12, Berlin | Büroreinigung | Täglich | Team A | #22c55e | Mo, 12.05. | €2.400/Mo | Aktiv (green) |
| Arztpraxis Dr. Weber | Schönhauser Allee 45 | Praxisreinigung | 3× wöchentlich | Team B | #3b82f6 | Mo, 12.05. | €1.200/Mo | Aktiv (green) |
| Einkaufszentrum CityMall | Friedrichstr. 88 | Unterhaltsreinigung | Täglich | Team C | #f59e0b | Mo, 12.05. | €8.500/Mo | Aktiv (green) |
| Wohnanlage Parkstraße 12–16 | Parkstr. 12-16 | Treppenhausreinigung | Wöchentlich | Team D | #a855f7 | Mi, 14.05. | €680/Mo | Aktiv (green) |
| Hotel Residenz Berlin | Kurfürstendamm 25 | Zimmerreinigung | Täglich | Team E | #ec4899 | Mo, 12.05. | €12.000/Mo | Aktiv (green) |
| RA Kanzlei Müller & Partner | Leipziger Str. 7 | Büroreinigung | 2× wöchentlich | Team F | #6366f1 | Di, 13.05. | €850/Mo | Aktiv (green) |
| Fitnessstudio PowerGym | Torstr. 99 | Grundreinigung | 3× wöchentlich | Team B | #3b82f6 | Mo, 12.05. | €2.100/Mo | Aktiv (green) |
| Schule am Stadtpark | Greifswalder Str. 30 | Schulreinigung | Täglich | — | — | — | €3.200/Mo | Pausiert (gray) |

Vertragswert column: right-aligned, `font-weight: 500`.

- [ ] **Step 2: Add Aufträge view**

Page header: "Aufträge", stats "23 offen · 18 in Bearbeitung · 5 überfällig", tabs "Alle | Geplant | In Arbeit | Überfällig | Abgeschlossen", button "+ Neuer Auftrag" (primary).

**Table columns:** Auftrag, Kunde, Objekt, Art, Fällig, Team, Status.

**6 rows:**

| # | Kunde | Objekt | Art | Fällig | Team | Status |
|---|-------|--------|-----|--------|------|--------|
| #2026-047 | Hotel Residenz | Hotel Residenz Berlin | Sonderreinigung | Heute (orange text, font-weight 600) | Team E | In Arbeit (yellow) |
| #2026-046 | CityMall GmbH | CityMall | Grundreinigung | 12.05.2026 | Team C | Geplant (gray) |
| #2026-045 | WohnBau AG | Wohnanlage Parkstr. | Fensterreinigung | 08.05.2026 (#dc2626) | Team D | Überfällig (red) — **row-overdue** |
| #2026-044 | Kanzlei Müller | Kanzlei Büro | Büroreinigung | 13.05.2026 | Team F | Geplant (gray) |
| #2026-043 | PowerGym Berlin | PowerGym | Grundreinigung | 14.05.2026 | Team B | Abgeschlossen (green) |
| #2026-042 | Dr. med. Weber | Arztpraxis | Desinfektion | 15.05.2026 | Team B | Geplant (gray) |

- [ ] **Step 3: Verify both views in browser**

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "feat: add Objekte + Aufträge views — tables with team tags and status badges"
```

---

### Task 4: View 4 — Kunden + View 5 — Mitarbeiter

**Files:**
- Modify: `index.html` — replace placeholders in `#view-kunden` and `#view-mitarbeiter`

- [ ] **Step 1: Add Kunden view**

Page header: "Kunden", stats "47 Kunden · €142.800 monatl. Vertragswert", tabs "Alle | Pipeline | Geschlossen", buttons "+ Neuer Kunde" (primary) + "✉ Email all" (secondary).

**Table columns:** Kunde (avatar + name + contact), Typ, Vertragswert, Phase, Letzter Kontakt, Nächste Aktion.

Kunde column: `<div style="display:flex;align-items:center;gap:10px"><div class="avatar">CM</div><div><div class="cell-main">CityMall GmbH</div><div class="cell-sub">Thomas Richter · Facility Manager</div></div></div>`

**7 rows:**

| Initials | Kunde | Contact | Typ | Wert | Phase | Letzter | Aktion |
|----------|-------|---------|-----|------|-------|---------|--------|
| CM | CityMall GmbH | Thomas Richter · Facility Manager | Großkunde | €8.500/Mo | Gewonnen (green) | 08.05.2026 | QK-Bericht senden |
| HR | Hotel Residenz Berlin | Sandra Hoffmann · Hoteldirektorin | Hotelkunde | €12.000/Mo | Gewonnen (green) | 09.05.2026 | Vertragsverlängerung |
| WB | WohnBau AG | Klaus Bergmann · Hausverwaltung | Hausverwaltung | €3.400/Mo | Gewonnen (green) | 06.05.2026 | Neue Objekte besprechen |
| MP | Müller & Partner RA | Dr. Claudia Müller · Senior Partner | Bürokunde | €850/Mo | Angebot (orange) | 10.05.2026 | Angebot nachfassen |
| DW | Dr. med. Weber | Dr. Michael Weber · Praxisinhaber | Praxiskunde | €1.200/Mo | Gewonnen (green) | 07.05.2026 | Monatsabrechnung |
| PG | PowerGym Berlin | Markus Stein · Betriebsleiter | Fitnessstudio | €2.100/Mo | Verhandlung (yellow) | 09.05.2026 | Besichtigung Dienstag |
| SP | Stadtpark Grundschule | Frau Lehmann · Schulleitung | Bildungseinr. | €3.800/Mo | Anfrage (gray) | 10.05.2026 | Erstgespräch planen |

- [ ] **Step 2: Add Mitarbeiter view**

Page header: "Mitarbeiter", stats "42 Mitarbeiter · 36 im Einsatz · 10 Teams", tabs "Alle | Nach Team | Verfügbarkeit", button "+ Mitarbeiter anlegen" (secondary — NOT primary).

**Table columns:** Name (avatar + name + role), Team, Objekte, Std./Woche, Seit, Bewertung, Status.

**8 rows:**

| Init | Name | Rolle | Team | Farbe | Objekte | Std. | Seit | Bew. | Status |
|------|------|-------|------|-------|---------|------|------|------|--------|
| MK | Maria Kowalski | Teamleiterin | Team A | #22c55e | 8 | 38,5 | 03/2022 | 4,8 ★ | Im Einsatz (green) |
| AH | Ahmed Hassan | Reinigungskraft | Team A | #22c55e | 5 | 40,0 | 08/2023 | 4,6 ★ | Im Einsatz (green) |
| TB | Thomas Bergmann | Teamleiter | Team B | #3b82f6 | 6 | 39,0 | 01/2021 | 4,9 ★ | Im Einsatz (green) |
| AP | Anna Petrova | Reinigungskraft | Team B | #3b82f6 | 4 | 32,0 | 11/2023 | 4,5 ★ | Frei (gray) |
| LF | Luis Fernandez | Teamleiter | Team C | #f59e0b | 7 | 40,0 | 06/2022 | 4,7 ★ | Im Einsatz (green) |
| PS | Peter Schulz | Teamleiter | Team D | #a855f7 | 5 | 38,0 | 04/2022 | 4,6 ★ | Im Einsatz (green) |
| SK | Sarah Krüger | Reinigungskraft | Team D | #a855f7 | 3 | 25,0 | 03/2024 | 4,4 ★ | Urlaub (orange) |
| JN | Jan Nowak | Teamleiter | Team E | #ec4899 | 6 | 40,0 | 09/2021 | 4,8 ★ | Im Einsatz (green) |

- [ ] **Step 3: Verify both views**

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "feat: add Kunden + Mitarbeiter views — avatars, pipeline badges, team tags"
```

---

### Task 5: View 6 — Einsatzplanung (Gantt) + View 7 — Rechnungen

**Files:**
- Modify: `index.html` — replace placeholders in `#view-planung` and `#view-rechnungen`

- [ ] **Step 1: Add Einsatzplanung view**

Page header: "Einsatzplanung", stats "KW 20 · 12.–16. Mai 2026 · 5 Teams eingeplant", tabs "Tagesansicht | Wochenplan" ("Wochenplan" active), buttons "← Vorwoche" (secondary) + "Heute" (primary) + "Nächste →" (secondary).

**Gantt structure:**
- Time axis header: 8 columns (06:00, 08:00, 10:00, 12:00, 14:00, 16:00, 18:00, 20:00) using `.gantt-time-header`
- Each team row uses `.gantt-row` with `.gantt-label` (team name + info) + `.gantt-track` (positioned bars)

**Bar positions** (timeline 06:00–20:00 = 14 hours):
- Team A: Bürokomplex 07:00–11:00 → left: 7.14%, width: 28.57%, `.gantt-bar`
- Team B: Dr. Weber 08:30–10:30 → left: 17.86%, width: 14.29%, `.gantt-bar-70`; PowerGym 16:00–18:00 → left: 71.43%, width: 14.29%, `.gantt-bar-70`
- Team C: CityMall 09:00–14:00 → left: 21.43%, width: 35.71%, `.gantt-bar`
- Team D: Wohnanlage 11:00–14:00 → left: 35.71%, width: 21.43%, `.gantt-bar-40`
- Team E: Hotel Residenz 14:00–19:00 → left: 57.14%, width: 35.71%, `.gantt-bar`

Label text on each bar: Object name + time range (e.g., "Bürokomplex · 07–11").

**Legend below** (using `.stat` styling): "■ 100% Unterhalts-/Zimmerreinigung · ■ 70% Grund-/Praxisreinigung · ■ 40% Treppenhausreinigung" — squares as small inline spans with matching backgrounds.

- [ ] **Step 2: Add Rechnungen view**

Page header: "Rechnungen", stats "€34.200 ausstehend · €10.600 bezahlt (Mai) · €1.200 überfällig", tabs "Alle | Offen | Bezahlt | Überfällig", buttons "+ Neue Rechnung" (primary) + "↓ Export" (secondary).

**Table columns:** Rechnung, Kunde, Leistungszeitraum, Betrag (right-aligned), Fällig, Status.

**5 rows:**

| # | Kunde | Zeitraum | Betrag | Fällig | Status |
|---|-------|----------|--------|--------|--------|
| #R-2026-091 | Hotel Residenz Berlin | April 2026 | €12.000,00 | 15.05.2026 | Offen (orange) |
| #R-2026-090 | WohnBau AG | April 2026 | €3.400,00 | 12.05.2026 | Offen (orange) |
| #R-2026-089 | CityMall GmbH | April 2026 | €8.500,00 | 10.05.2026 | Bezahlt (green) |
| #R-2026-088 | Dr. med. Weber | April 2026 | €1.200,00 | 01.05.2026 (#dc2626) | Überfällig (red) — **row-overdue** |
| #R-2026-087 | PowerGym Berlin | April 2026 | €2.100,00 | 08.05.2026 | Bezahlt (green) |

- [ ] **Step 3: Verify Gantt + Rechnungen**

Gantt: bars positioned correctly across timeline. Black bars at different opacities. Team labels on the left.
Rechnungen: clean table, overdue row highlighted red.

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "feat: add Einsatzplanung (Gantt) + Rechnungen views"
```

---

### Task 6: View 8 — Auswertungen + Chart.js initialization

**Files:**
- Modify: `index.html` — replace placeholder in `#view-auswertungen` and add `initCharts()` function to script

- [ ] **Step 1: Add Auswertungen view**

Page header: "Auswertungen", stats "Mai 2026 · Gesamtumsatz YTD €216.750", tabs "Übersicht | Umsatz | Qualität | Personal" ("Übersicht" active), buttons "↓ Export" (secondary) + Zeitraum `<select>` (Mai/April/März 2026).

**Content sections** (NOT in cards — use `.section-label` as dividers):

**Section: Umsatz**
- `.section-label`: "UMSATZ"
- Bar chart in `.chart-container > .chart-wrap` — Canvas `id="auswertungenChart"`
- Chart config: type 'bar', labels ['Dez','Jan','Feb','Mär','Apr','Mai'], data [38200,41500,39800,43100,45600,47850], backgroundColor last bar #1a1a1a rest #e8e8e8, borderRadius 3, barPercentage 0.5, no legend, no grid on x, subtle grid on y (#f0f0f0), y-axis ticks format "€Xk", font Inter 11px #aaa

**Section: Kennzahlen**
- `.section-label`: "KENNZAHLEN"
- 4 `.metric-row` items:
  - Ø Marge: 34,2% / `.metric-change.up` +2,1 PP
  - Kundenzufriedenheit: 4,7 / 5 / 124 Bewertungen
  - Personalauslastung: 87% / Ø 36,8 Std./Woche
  - Neue Kunden (YTD): 8 / +3 ggü. Vorjahr

**Section: Erfüllungsquote**
- `.section-label`: "ERFÜLLUNGSQUOTE"
- 4 `.pbar` rows: Büroreinigung 94% (green), Praxisreinigung 98% (green), Treppenhausrein. 82% (red), Hotelreinigung 91% (green)

**Section: Top-Kunden**
- `.section-label`: "TOP-KUNDEN NACH UMSATZ"
- Simple table (reuse `.tbl`): Kunde, Monatsumsatz (right-aligned). 5 rows: Hotel Residenz €12.000, CityMall €8.500, WohnBau €3.400, PowerGym €2.100, Dr. Weber €1.200

- [ ] **Step 2: Add initCharts() function to script block**

```javascript
function initCharts() {
  const ctx = document.getElementById('auswertungenChart');
  if (!ctx) return;
  new Chart(ctx, {
    type: 'bar',
    data: {
      labels: ['Dez', 'Jan', 'Feb', 'Mär', 'Apr', 'Mai'],
      datasets: [{
        data: [38200, 41500, 39800, 43100, 45600, 47850],
        backgroundColor: ['#e8e8e8','#e8e8e8','#e8e8e8','#e8e8e8','#e8e8e8','#1a1a1a'],
        borderRadius: 3,
        barPercentage: 0.5,
      }]
    },
    options: {
      responsive: true,
      maintainAspectRatio: false,
      plugins: { legend: { display: false }, tooltip: {
        backgroundColor: '#1a1a1a', titleFont: { family: 'Inter', size: 11 },
        bodyFont: { family: 'Inter', size: 12, weight: '600' },
        padding: 10, cornerRadius: 6,
        callbacks: { label: ctx => '€' + ctx.parsed.y.toLocaleString('de-DE') }
      }},
      scales: {
        x: { grid: { display: false }, ticks: { font: { family: 'Inter', size: 11 }, color: '#aaa' }, border: { display: false } },
        y: { grid: { color: '#f0f0f0' }, ticks: { font: { family: 'Inter', size: 11 }, color: '#aaa', callback: v => '€' + (v/1000) + 'k', maxTicksLimit: 5 }, border: { display: false } }
      }
    }
  });
  window._chartInit = true;
}
```

- [ ] **Step 3: Verify Auswertungen view**

Expected: Subtle bar chart (gray bars, last one black). Kennzahlen as text rows. Progress bars. Top-Kunden as mini-table. All without cards or containers.

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "feat: add Auswertungen view — bar chart, metrics, progress bars, top customers"
```

---

### Task 7: Deploy + push + verify live

**Files:**
- No file changes — deployment only

- [ ] **Step 1: Push to GitHub**

```bash
cd /Users/alexispittadakis/clima-demo
git push
```

- [ ] **Step 2: Deploy to Railway**

```bash
railway up --detach
```

- [ ] **Step 3: Wait for deploy and verify**

```bash
sleep 30 && railway service status
```

Expected: Status SUCCESS.

- [ ] **Step 4: Verify live URL**

Open https://clima-demo-production.up.railway.app and check:
- All 8 views navigable via sidebar
- Folk-style aesthetic: white bg, minimal color, black/white palette
- Tables fill viewport, no cards
- Gantt bars positioned correctly
- Chart renders on Auswertungen
- Status badges are the only color elements
