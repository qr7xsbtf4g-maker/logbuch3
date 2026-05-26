# HANDOFF — Dashboard DJI

## Aktueller Stand

**Datei:** `/Users/djoker/Projekt Dashboard CC/index.html`  
**Zeilen:** ~2771  
**Git:** Ja — remote auf GitHub, GitHub Pages aktiv  
**Syntaxcheck:** `node --check <(sed -n '/<script>/,/<\/script>/p' index.html | sed '1d;$d')`

---

## GitHub / Deployment

- **Repo:** https://github.com/qr7xsbtf4g-maker/logbuch3
- **GitHub Pages:** https://qr7xsbtf4g-maker.github.io/logbuch3/
- **Remote URL enthält PAT** (in `.git/config`) — Token in `.git/config` gespeichert, nicht hier dokumentieren
- Push: `git push origin main` — kein Force mehr nötig, Remote ist jetzt in Sync
- GitHub Pages baut automatisch nach jedem Push (~1–2 min)

---

## Was in dieser Session erledigt wurde

### Check-in: Wochenschnitt / Verlauf / Sonntag
- Wochenschnitt-Boxen (Gewicht + Cardio) sind jetzt klickbar → öffnen neues Modal `#wk-hist-modal-bg`
- Modal zeigt letzte 8 Wochen mit Datum-Range, Wert und Woche-vs-Vorwoche Delta
- Vergleich "vs Vorwoche" (trendArrow, Cardio-Diff, Wochenabschluss-Block) **nur am Sonntag** sichtbar

### Training: ERLEDIGT Badge
- Nach `saveTraining()` → `markTodayTrainingDone()`: Badge "GEPLANT" → "ERLEDIGT" (grün, `--okl`), Banner-Border → `--ok`
- `initTodayPlan()` prüft beim Laden ob Trainings-Eintrag für heute existiert → setzt Badge automatisch

### Abend: One Week Erledigt? + Journal
- In der Abend-Karte (unter Verdauung): Ja/Nein Toggle `togOneWeek()` → speichert `oneWeekDone` im daily-Eintrag
- Journaling-Block: feste Frage ("Wofür bin ich heute konkret dankbar?") + täglich rotierende Frage (7 Fragen, nach Wochentag)
  - Mo: Was hat mich heute angetrieben?
  - Di: Wem bin ich dankbar — und warum konkret?
  - Mi: Was hat heute besser geklappt als erwartet?
  - Do: Welche heutige Herausforderung war eigentlich eine Chance?
  - Fr: Was nehme ich aus dieser Woche als Stärke mit?
  - Sa: Worauf bin ich heute stolz — groß oder klein?
  - So: Was möchte ich morgen bewusst anders machen?
- Textarea speichert `eveningJournal` + `journalQuestion` im daily-Eintrag
- "Letzte Einträge"-Button zeigt letzte 5 Journal-Einträge inline
- Journal wird in `renderDH` Detail-Row angezeigt
- `loadTodayEntry`, `resetDaily`, `resetDailyAbend` alle angepasst

### Bug-Fix: Gym-Wechsel löscht Training-Inputs
- `selGym()` speichert jetzt alle Set-Inputs (kg + reps) vor dem `buildEx()`-Rebuild
- Nach dem Rebuild werden die Werte wiederhergestellt (identisch zu `moveEx`)
- Gym-Namen der Übungen werden korrekt aktualisiert, eingetippte Werte bleiben erhalten

### Safe-Area / Tab-Bar: iPhone 17 Pro
- `.tab-bar`: `padding-bottom: calc(6px + env(safe-area-inset-bottom,0px))` — Home-Indikator-Bereich wird respektiert
- `.view`: `padding-bottom: calc(108px + env(safe-area-inset-bottom,0px))` — Scroll-Bereich passt sich an

### Timer-Signal: 3-Ton-Chime + visuelles Banner
- `beep()` ersetzt: statt Einzelton jetzt aufsteigendes C5-E5-G5 Sinus-Chime mit Envelope
- Neue Funktion `timerDoneNotify(type)`: Vibration (150-80-150-80-300ms) + gold Banner (#timer-banner) oben im Viewport
- Banner zeigt: "Seite wechseln!" (unilateral) oder "Pause vorbei — weiter!" (normaler Rest)
- CSS: `.timer-banner` / `.timer-banner.visible` (position:fixed, z-index:500, gold, fade-in)

### Progressive Overload: 5–8 Wiederholungs-Regel
- `setProgLabel()` komplett neu nach Dustins Regel:
  - `▼` Unter 5 Wdh → zu schwer
  - `▲` Gewicht erhöht + ≥5 Wdh → Progression
  - `▲` 8 Wdh erreicht → "nächste Session Gewicht erhöhen"
  - `▲` Gleiche kg, mehr Wdh → rep-Progression
  - `→` Letzte Session war 8 Wdh, Gewicht nicht erhöht → Hinweis
  - `▼` Gewicht reduziert → Regression

---

### Design-Overhaul: Premium Dark Theme

Das gesamte Design wurde neu gestaltet. Orientierung an **kiberatung.de** (near-black Surfaces, cool blue-gray Borders) + Apple HIG.

**Finales `:root` (Premium Dark):**
```css
:root{--bg:#050507;--s1:#0B0B12;--s2:#111119;--s3:#17171F;--bd:rgba(192,192,210,0.10);--bd2:rgba(192,192,210,0.18);--gold:#c9a84c;--goldl:#e8c76a;--goldd:#8a6f30;--goldf:rgba(201,168,76,0.08);--tx:#E4ECE8;--txm:#8E8EA4;--txd:rgba(142,142,164,0.42);--ok:#1C7A3A;--okl:#30D158;--okf:rgba(48,209,88,0.10);--err:#7B2020;--errl:#FF453A;--errf:rgba(255,69,58,0.10);--warn:#8a6030;--warnl:#FF9F0A;--r:10px;--r2:13px;...}
```

- `html` Background: `linear-gradient(180deg,#050507 0%,#08080F 100%)` + `background-color:#050507` als Fallback
- `body`: `background:#050507` (vorher `transparent` → führte zu weißem Balken)
- Tab-Bar: `background:var(--s1)` + `::after` Pseudo-Element für Safe Area
- Kein Glassmorphism, kein `backdrop-filter`
- Kein Theme-Toggle — nur ein Theme (Premium Dark)

**Aktualisierte Elemente auf CSS-Vars:**
- `.ticker`, `.hero-item`, `.supp-item`, `.th-entry` → `background:var(--s2);border:1px solid var(--bd)`
- `.ta-modal`, `.author-modal` → `background:var(--s1)`
- Quote/Modal-Text → `color:var(--tx)` / `var(--txm)` / `var(--txd)`

### Bug-Fix: Weißer/schwarzer Balken unten — Safe Area komplett entfernt

- **Ursache:** `body{background:transparent}` + Safe-Area-Padding auf Tab-Bar → sichtbarer Balken
- **Fix:** `body{background:#050507}`, `html` mit `background-color:#050507` als Fallback
- **Safe Area komplett raus** (auf Wunsch): kein `env(safe-area-inset-bottom)` mehr in Tab-Bar, `.view` oder Toast
- Tab-Bar Background: `var(--bg)` statt `var(--s1)` — nahtlos mit Seitenhintergrund
- Kein `::after` Pseudo-Element

---

## Was in früheren Sessions erledigt wurde

### UX-Verbesserungen

#### Ring: Tageszeit → Tages-Completion
- `calcDayCompletion()` — 4 Aufgaben à 25%: Morgen, Supps, Training, Abend
- `updateDayRing()` zeigt `pct%`, nächste offene Aufgabe, `done/4`
- Ring-Farbe: transparent bei 0%, `--gold` bei 1–3/4, `--okl` bei 4/4
- Wird nach `saveMorning`, `saveEvening`, `saveTraining`, `saveCardio`, `toggleSuppItem` aufgerufen

#### Training-Tab: Split-Banner
- `#today-plan-banner` oben im Training-Tab mit Wochentag, Split-Label, Badge
- `initTodayPlan()` — GEPLANT/RUHETAG, gold-Border an Trainingstagen
- `_todaySplit` in `ST` gespeichert; `togKraft()` wählt Split automatisch

#### Datum-Feld & Hero-Metriken
- Datum: kompaktes Inline-Format
- Hero-Farben: `--txd` bei leer/0, `--tx` bei Wert, `--gold` bei Streak > 0

### Bug-Fix: Initialisierungsreihenfolge
- `DAILY_SUPPS`, `SLEEP_SUPPS`, `ILLNESS_SUPPS`, `suppData` nach oben zu Globals verschoben (verhinderte TDZ-ReferenceError → Ladescreen-Freeze)

### Apple Health
- Alle Health-Sync-Funktionen entfernt
- Einzige Ausnahme: **"♥ In Apple Health schreiben"-Button** — triggert `shortcuts://run-shortcut?name=Logbuch-zu-Health&input=...`, sendet Gewicht als `85,1` (Komma, deutsches iOS-Format)

### Weitere frühere Änderungen
- Umbenennung Logbuch → Dashboard
- Gewicht-History-Modal (letzte 7 Einträge mit Delta)
- Koffein: ½ Höllenfeuer (50mg) + Custom-Eingabe
- Entfernt: Wasser, Cheats, Kalorien-Ziel
- Performance: `_dbCache`, `_safeSave()`, Foto-Komprimierung mit Fallback

---

## Aktueller Supplement-Stack (in `index.html`)

### DAILY_SUPPS
| Name | Dosis | Timing |
|------|-------|--------|
| Rhodiola Rosea | 500mg · nüchtern besser | morgens |
| Elektrolyte | 5g · Mahlzeit egal | morgens |
| Kreatin | ~8g (0.1g/kg) · Mahlzeit egal | morgens |
| Curcumin NovaSol | 500–1000mg · fettlöslich · Mahlzeit zwingend | mahlzeit |
| Omega-3 | 2–4g EPA+DHA · fettlöslich · Mahlzeit zwingend | mahlzeit |
| Vitamin D3 + K2 | 5000 IE · fettlöslich · Mahlzeit zwingend | mahlzeit |
| GEN-All in One | 4–8 Kapseln · fettlöslich (ADEK) · Mahlzeit zwingend | mahlzeit |
| Zink | 25mg · nicht mit Calcium/Eisen · Mahlzeit empfohlen | abends |
| Magnesium Bisglycinat | 300–500mg · Mahlzeit empfohlen | abends |
| Ashwagandha | 600mg · mit etwas Fett · Mahlzeit empfohlen | abends |
| Lithium Orotat | 8mg · Mahlzeit zwingend (GI) | abends |

### SLEEP_SUPPS
| Name | Dosis | Timing |
|------|-------|--------|
| Melatonin | 0.5–1mg · nüchtern besser | 30 min vorher |
| 5-HTP | 50–200mg · nicht dauerhaft · nüchtern besser | 1–2h vorher |
| GABA | 100–1000mg · Mahlzeit egal | 30–60 min vorher |
| Mg L-Threonat | 1–2g · Mahlzeit egal | 2h vorher |
| L-Theanin | 100–400mg · Mahlzeit egal | 30–60 min vorher |

### ILLNESS_SUPPS
| Name | Dosis | Timing |
|------|-------|--------|
| Zink (akut) | 100mg · max. 1–2 Wochen | morgens |
| Vitamin C | 2–3g | akut |
| NAC | 1.200mg | akut |
| Glutamin | 20–30g | morgens |
| Curcumin (akut) | 500–2000mg | zur Mahlzeit |
| Omega-3 (akut) | 3g EPA+DHA | zur Mahlzeit |

---

## Wichtige technische Details

- **localStorage-Keys:** `lbv4` (Daily + Training), `lbv4_ci` (Check-in), `lbv4_ph` (Fotos), `lbv4_hi` (History), `lbv4_supp` (Supplement-Status), `lbv4_koff` (Koffein-Log), `lb_kraft_tog` / `lb_cardio_tog` (Toggle-Persistenz bis Mitternacht), `lb_backup_ts` (letzter Export-Zeitstempel)
- **Cache-Invalidierung:** Nur `dbS()` setzt `_dbCache`. `dbCI`, `dbPH`, `dbHI` haben keinen Cache
- **Health-Button Shortcut-Name:** muss exakt `Logbuch-zu-Health` heißen
- **Gewicht-Format für Health:** `val.toFixed(1).replace('.', ',')` — deutsches Dezimalformat zwingend
- **Syntaxcheck:** `node --check <(sed -n '/<script>/,/<\/script>/p' index.html | sed '1d;$d')`

---

## Prozessregeln (aus CLAUDE.md)

- **Supplement-Änderungen:** Immer zuerst `/supplement-stack-check` laufen lassen, Feedback vorlegen, erst nach Freigabe einbauen
- **Keine neuen Dateien** — alles bleibt in `index.html`
- **Vor Löschen/Überschreiben** immer nachfragen

---

## Offene Punkte / Ideen

- iOS Shortcut "Health → Logbuch" existiert nicht mehr — falls wieder gewünscht, Anleitung aus alter HANDOFF konsultieren
- GitHub Pages URL kann als PWA auf dem iPhone zum Homescreen hinzugefügt werden (Safari → Teilen → Zum Home-Bildschirm)
- Safe Area unten ist bewusst entfernt — falls Tab-Buttons schlecht tippbar werden, `padding-bottom:calc(6px + env(safe-area-inset-bottom,0px))` in `.tab-bar` wieder ergänzen
