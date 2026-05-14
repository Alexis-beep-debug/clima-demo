# CleanOS — Folk-Style Redesign Spec

## Kontext

Demo-Dashboard für Reinigungssoftware "CleanOS". Ziel: Screenshots für einen Kunden (Gebäudereinigungsunternehmen) der kurz vor Abschluss steht. Muss professionell und modern aussehen — nicht wie AI-generiertes Dashboard.

**Design-Referenz:** Folk CRM (folk.app)
**Zielgruppe:** Geschäftsführer Reinigungsunternehmen, ~42 Mitarbeiter, 84 Objekte
**Tech:** Single-file HTML, Tailwind CDN, Chart.js, Lucide Icons, deployed auf Railway
**Zweck:** Nur Screenshots, keine Interaktivität nötig

---

## Design-System

### Stil-Prinzipien (von Folk übernommen)

1. **Jede Seite IST ihre Daten** — keine Meta-Übersichten, kein Dashboard mit KPIs von anderen Bereichen
2. **Stats nur seitenrelevant** — Rechnungen zeigt Rechnungs-Zahlen, Einsätze zeigt Einsatz-Zahlen
3. **Tabellen als Hauptelement** — borderless, clean hover, volle Breite
4. **Minimale Farbe** — schwarz/weiß/grau als Basis, Farbe nur für Status-Badges
5. **Große fette Headlines** — Seitenname als große Überschrift, nicht als kleiner Header-Text
6. **Keine Cards/Sections** — Daten stehen frei, nicht in Container verpackt
7. **Breadcrumb-Navigation** oben statt Page-Title im Header

### Farbpalette

| Token | Wert | Verwendung |
|-------|------|------------|
| text-primary | #1a1a1a | Headlines, Tabellenwerte |
| text-secondary | #666 | Nav-Items, Labels |
| text-muted | #999 / #aaa | Subtitles, Meta-Info |
| text-placeholder | #bbb | Placeholder-Text |
| border | #e8e8e8 | Sidebar-Border, Topbar-Border |
| border-subtle | #f0f0f0 / #f5f5f5 | Tabellen-Zeilen |
| bg-page | #fff | Haupthintergrund |
| bg-hover | #fafafa | Tabellen-Hover |
| bg-active | #f0f0f0 | Aktiver Nav-Item |
| badge-green | #dcfce7 / #15803d | Erledigt, Aktiv, Gewonnen |
| badge-yellow | #fef9c3 / #a16207 | Läuft, In Verhandlung |
| badge-red | #fee2e2 / #dc2626 | Überfällig, Fehler |
| badge-gray | #f3f4f6 / #6b7280 | Geplant, Pausiert |
| badge-blue | #dbeafe / #1d4ed8 | Info, Besichtigung |
| badge-orange | #ffedd5 / #c2410c | Angebot, Warnung |
| badge-purple | #f3e8ff / #7c3aed | Team-Markierung |
| accent | #1a1a1a | Primary Buttons, aktiver Tab |

### Typografie

| Element | Font | Gewicht | Größe |
|---------|------|---------|-------|
| Seitentitel | Inter | 700 | 26px, letter-spacing -0.5px |
| Section-Header | Inter | 600 | 13px |
| Body/Tabellen | Inter | 400-500 | 13px |
| Labels/Meta | Inter | 500 | 11px, uppercase, tracking 0.3px |
| Badges | Inter | 500 | 10.5px |

### Komponenten

**Sidebar:** Weiß, 220px breit, rechter Border. Gruppiert (Betrieb / Verwaltung / Einstellungen). Items mit 15px Icons, Counts rechts ausgerichtet. Aktiver Item: bg #f0f0f0, font-weight 500.

**Topbar:** 44px hoch, Breadcrumb links (Seitenname · Datum), Search + Icons rechts.

**Tabellen:** Keine vertikalen Borders. Thead: 11px uppercase, #aaa. Tbody: 13px, rows mit border-bottom #f5f5f5. Hover: #fafafa. Sticky thead.

**Badges/Tags:** 10.5px, 500 weight, 2px 7px padding, 4px border-radius. Verschiedene Farben je Status.

**Buttons:** Primary = schwarz bg, weiß text, 32px hoch, 6px border-radius. Secondary = weißer bg, 1px border #e0e0e0.

**Team-Tags:** Kleine inline-Elemente mit farbigem 8x8px Quadrat + Team-Name.

---

## Views (8 Stück)

### 1. Heutige Einsätze (Landing Page)

**Seitentitel:** "Heutige Einsätze"
**Stats-Zeile:** "6 Einsätze geplant · 8 Teams im Einsatz · 21 Std. geplant · 1 überfällig"
**Tabs:** Tagesansicht | Wochenplan | Alle Einsätze
**Buttons:** + Einsatz planen (primary), Export (secondary)

**Tabelle:**

| Spalte | Inhalt |
|--------|--------|
| Zeit | Uhrzeit, tabular-nums |
| Objekt | Name (bold) + Adresse (sub) |
| Reinigungsart | Grauer Text |
| Team | Farbiger Team-Tag |
| Mitarbeiter | "M. Kowalski +3" |
| Dauer | "4 Std." |
| Status | Badge: Erledigt (grün), Läuft (gelb), Geplant (grau), Überfällig (rot) |

**Besonderheiten:**
- Aktuell laufender Einsatz: Zeile mit #fffbeb Hintergrund
- Überfälliger Einsatz: Zeile mit #fef2f2 Hintergrund, am Ende der Liste
- Zeit-Spalte bei aktuellem Einsatz: bold, schwarz statt grau

### 2. Objekte

**Seitentitel:** "Objekte"
**Stats-Zeile:** "84 Objekte · 6 pausiert · 2 gekündigt"
**Tabs:** Alle | Aktiv | Pausiert
**Buttons:** + Neues Objekt (primary), Filter (secondary), Import (secondary)

**Tabelle:**

| Spalte | Inhalt |
|--------|--------|
| Objekt | Name (bold) + Adresse (sub) |
| Typ | Büroreinigung, Praxisreinigung, etc. |
| Intervall | Täglich, 3× wöchentlich, etc. |
| Team | Team-Tag |
| Nächster Einsatz | Datum |
| Vertragswert | €/Monat |
| Status | Badge: Aktiv (grün), Pausiert (grau) |

### 3. Aufträge

**Seitentitel:** "Aufträge"
**Stats-Zeile:** "23 offen · 18 in Bearbeitung · 5 überfällig"
**Tabs:** Alle | Geplant | In Arbeit | Überfällig | Abgeschlossen
**Buttons:** + Neuer Auftrag (primary)

**Tabelle:** (NICHT Kanban — Folk macht keine Kanban-Boards, sondern Tabellen mit Status-Badges)

| Spalte | Inhalt |
|--------|--------|
| Auftrag | #Nummer |
| Kunde | Name |
| Objekt | Objektname |
| Art | Reinigungsart |
| Fällig | Datum (rot wenn überfällig) |
| Team | Team-Tag |
| Status | Badge |

### 4. Kunden

**Seitentitel:** "Kunden"
**Stats-Zeile:** "47 Kunden · €142.800 monatl. Vertragswert"
**Tabs:** Alle | Pipeline | Geschlossen
**Buttons:** + Neuer Kunde (primary), Email all (secondary)

**Pipeline-View (Tab "Pipeline"):** Tabelle mit Status-Spalte die Pipeline-Phase als farbigen Badge zeigt (Anfrage → Besichtigung → Angebot → Verhandlung → Gewonnen). Wie Folk's Recruitment Pipeline — Tabelle, kein Kanban.

**Tabelle:**

| Spalte | Inhalt |
|--------|--------|
| Kunde | Initialen-Circle + Name (bold) + Ansprechpartner (sub) |
| Typ | Großkunde, Praxiskunde, etc. |
| Vertragswert | €/Mo |
| Phase | Badge: Anfrage (grau), Besichtigung (blau), Angebot (orange), Verhandlung (gelb), Gewonnen (grün) |
| Letzter Kontakt | Datum |
| Nächste Aktion | Text |

### 5. Mitarbeiter

**Seitentitel:** "Mitarbeiter"
**Stats-Zeile:** "42 Mitarbeiter · 36 im Einsatz · 10 Teams"
**Tabs:** Alle | Nach Team | Verfügbarkeit
**Buttons:** + Mitarbeiter anlegen (secondary)

**Tabelle:**

| Spalte | Inhalt |
|--------|--------|
| Name | Initialen-Circle + Name (bold) + Rolle (sub) |
| Team | Team-Tag |
| Objekte | Anzahl |
| Std./Woche | Zahl |
| Seit | Datum |
| Bewertung | "4,8 ★" |
| Status | Badge: Im Einsatz (grün), Frei (grau), Urlaub (orange), Krank (rot) |

### 6. Einsatzplanung

**Seitentitel:** "Einsatzplanung"
**Stats-Zeile:** "KW 20 · 12.–16. Mai 2026 · 5 Teams eingeplant"
**Tabs:** Tagesansicht | Wochenplan
**Buttons:** ← Vorwoche / Nächste → (secondary), Heute (primary)

**Wochenplan:** Gantt-Timeline, ABER in Folk-Farben:
- Zeitachse: 06:00–20:00, Labels in #aaa
- Team-Labels links: 140px, weiß, rechter Border
- Gantt-Bars: #1a1a1a (Hauptfarbe) mit weißem Text, NICHT bunt
- Verschiedene Opacity-Stufen für verschiedene Reinigungsarten (100%, 70%, 40%)
- Legende unten in grauem Text

### 7. Rechnungen

**Seitentitel:** "Rechnungen"
**Stats-Zeile:** "€34.200 ausstehend · €10.600 bezahlt (Mai) · €1.200 überfällig"
**Tabs:** Alle | Offen | Bezahlt | Überfällig
**Buttons:** + Neue Rechnung (primary), Export (secondary)

**Tabelle:**

| Spalte | Inhalt |
|--------|--------|
| Rechnung | #Nummer |
| Kunde | Name |
| Leistungszeitraum | "April 2026" |
| Betrag | €-Wert, rechtsbündig |
| Fällig | Datum (rot wenn überfällig) |
| Status | Badge: Offen (orange), Bezahlt (grün), Überfällig (rot) |

### 8. Auswertungen

**Seitentitel:** "Auswertungen"
**Stats-Zeile:** "Mai 2026 · Gesamtumsatz YTD €216.750"
**Tabs:** Übersicht | Umsatz | Qualität | Personal
**Buttons:** Export (secondary), Zeitraum-Select

**Inhalt:** Dies ist die EINZIGE Seite die Charts zeigen darf — hier macht es Sinn weil die Seite Analytics IST.
- **Umsatz-Barchart:** 6 Monate, Balken in #1a1a1a (letzter) und #e8e8e8 (Rest), subtil, minimal
- **Top-Kunden Liste:** Tabelle mit Kunde + Umsatz, sortiert
- **Erfüllungsquote:** Progress-Bars pro Reinigungsart (4px, grün/rot)
- **Kennzahlen als Text-Zeilen:** "Ø Marge: 34,2% (+2,1 PP)" als einfacher Text, keine Cards

---

## Sidebar-Struktur

```
CleanOS [Logo]

Search
Notifications (3)

── Betrieb ──
■ Heutige Einsätze     [landing]
■ Objekte               84
■ Aufträge              (5) rot
■ Einsatzplanung

── Verwaltung ──
■ Kunden                47
■ Mitarbeiter           42
■ Rechnungen
■ Auswertungen

── ──
■ Settings & members

[ML] Markus Lehmann
     Geschäftsführer
```

---

## Was NICHT enthalten ist

- Kein übergreifendes Dashboard mit KPIs aus verschiedenen Bereichen
- Keine Charts außer auf der Auswertungen-Seite
- Keine Card-Layouts oder Card-Grids
- Keine Progress-Bars außer auf Auswertungen
- Keine bunten Gantt-Bars (nur schwarz mit Opacity-Variation)
- Keine Activity-Feeds oder Notification-Center
- Keine Modals oder Slide-Over-Panels
- Keine Hero-Metric-Layouts
- Keine Gradient-Texte, Glassmorphism, Glows
- Keine Emoji-Icons in der Sidebar

## Was explizit enthalten ist

- Inter als einzige Font (nicht Bricolage Grotesque + Figtree)
- Weiße Sidebar statt dunkle
- Schwarze Primary-Buttons statt orange
- Borderless Tabellen als Hauptelement jeder Seite
- Farbige Status-Badges als einziges Farb-Element
- Team-Tags mit kleinen farbigen Quadraten
- Breadcrumb in der Topbar
- Stats-Zeile unter jedem Seitentitel (nur seitenrelevant)
- View-Switcher Tabs unter dem Titel
