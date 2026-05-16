# CLAUDE.md — Logbuch Web-App

## Kommunikation
- Antworte **immer auf Deutsch**.
- Jeden Schritt kurz erklären, bevor er ausgeführt wird.
- Rückfragen stellen bei Unklarheiten — keine Annahmen.
- Vor dem Löschen oder Überschreiben von Dateien immer nachfragen.

---

## Projekt-Überblick

**"LOGBUCH · DJI"** — Eine Single-File PWA (`index.html`) als persönliches Trainings- und Tageslogbuch.  
Kein Build-System, kein Framework — reines HTML/CSS/JavaScript mit `localStorage` als Datenbank.

---

## Trainingsplan

**Split:** 4-Tage-Woche mit Ruhetagen

| Tag       | Einheit          |
|-----------|-----------------|
| Montag    | OK — Oberkörper  |
| Dienstag  | UK — Unterkörper |
| Mittwoch  | OFF              |
| Donnerstag| GK1 — Ganzkörper 1 |
| Freitag   | OFF              |
| Samstag   | GK2 — Ganzkörper 2 |
| Sonntag   | OFF              |

**Fokus:** Hypertrophie  
**Cardio:** Zone 2 auf dem BikeErg (zusätzlich zum Krafttraining)

---

## Gyms

| Index | Name                  |
|-------|-----------------------|
| 0     | CleverFit Leipzig     |
| 1     | JoyFitness Lübeck     |
| 2     | CleverFit Lübeck      |

---

## Technischer Stack

- **Sprache:** HTML5 / CSS3 / Vanilla JavaScript (ES6+)
- **Datenhaltung:** `localStorage` (Keys: `lbv4`, `lbv4_ci`, `lbv4_ph`, `lbv4_hi`)
- **Fonts:** Bebas Neue, DM Mono, Barlow Condensed (Google Fonts)
- **Charts:** Chart.js 4.4.1
- **PWA:** Apple-optimiert (meta tags, safe-area, no-bounce scroll)

---

## Design-System

```
--bg: #080808         Hintergrund
--gold: #c9a84c       Primärfarbe / Akzent
--tx: #ede9e0         Text
--ok: #1a7a3f         Grün (Erfolg)
--err: #9b2c2c        Rot (Fehler/Warnung)
```

Schriftarten:
- `Bebas Neue` — Headlines, Labels, Timer
- `DM Mono` — Zahlen, Daten, Werte
- `Barlow Condensed` — Fließtext, Beschriftungen

---

## App-Struktur (Tabs)

1. **Daily** — Morgen- und Abend-Check-in (Gewicht, Schlaf, Kalorien, Wasser, Stimmung, Energie)
2. **Training** — Gym-Auswahl, Split-Auswahl, Session-Timer, Übungsblöcke, Cardio
3. **Weekly** — Statistiken, Gewichtsverlauf, Check-in, Foto-Vergleich, Export/Import

---

## Cardio / Zone 2

Das Zone 2 Cardio wird auf dem **BikeErg** absolviert.  
Im Trainings-Tab gibt es bereits ein Cardio-Feld (Art, Dauer, Uhrzeit, Nüchtern-Cardio).  
Zone-2-spezifische Felder (Herzfrequenz-Zone, Watt, Kalorien) ggf. ergänzen.

---

## Arbeitsregeln

- **Keine unnötigen Änderungen** über den Auftrag hinaus.
- **Schrittweise vorgehen** — eine Sache nach der anderen.
- **Keine neuen Dateien** anlegen, sofern nicht explizit gewünscht (alles bleibt in `index.html`).
- **Kein Build-System** einführen — die App ist bewusst frameworkfrei.
- Vor destruktiven Aktionen (Datei löschen, großflächige Umstrukturierung) immer Rückfrage.
