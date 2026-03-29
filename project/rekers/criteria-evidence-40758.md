---
publish: true
---

# Kriteriennachweis — Projekt 40758: Pfenning Neubau Halle
[[client-rekers]]

Nachweis für Design-Doc Teil 2. Datenverfügbarkeit pro Kriterium mit Quelldateien und extrahierten Werten.

Projekt: 40758 | Datum: 2024-01-18 | Wert: €4.678K

---

## Untersuchte Daten

| Ebene | Anzahl | Details |
|-------|--------|---------|
| L0: Ordnerdateien | 69 | 49 EML, 18 PDF, 2 sonstige |
| L2: EML-Anhänge | 282 | 167 PDF, 115 sonstige (JPG, PNG, XLSX, BMP) |

Multiplikator: 5,1x (L0 vs. L0+L2)

---

## Übersicht

| Kriterium | Status | Wert | Quellebene |
|-----------|--------|------|------------|
| Bauort | ✅ CSV | Ladbergen, 49549, D | L1 |
| Kundenreferenz | ✅ CSV | 14244 | L1 |
| Kalkulatorenwissen | ✅ CSV | 1051 | L1 |
| Gebäudetyp | ✅ | Logistikhalle (Neubau) mit 2 Hallentrakte (A+B), Mezzanin, Büro | L2 |
| Höhe | ✅ | OK Attika +17,00 m; UK Binder Tiefpunkt +14,20 m; UK Binder Hochpunkt +14,88 m | L2 |
| Kran | ❌ | Nicht gefunden | — |
| Dachlasten | ✅ | Installation 0,200; PV 0,200; Gründach 1,200 kN/m²; SLZ1; WLZ2 | L2 |
| Baustoff | ✅ | Spannbeton-Parallelbinder; Stb.-FT C50/60; B500(A); ST1660/1860 | L2 |
| Dachbegrünung | ✅ | Gründach schwer (1,200 kN/m²), ca. 50% der Dachfläche | L2 |

**8/9 nachgewiesen** (3 strukturiert + 5 extrahiert). 1 nicht vorhanden.

---

## Strukturierte Kriterien (Anfragen.csv)

### Bauort
- **Wert:** Ladbergen, 49549, D
- **Quelle:** Anfragen.csv, Zeile 2 → `BAUVORHABEN_ORT`, `PLZ`, `NATION`

### Kundenreferenz
- **Wert:** 14244
- **Quelle:** Anfragen.csv, Zeile 2 → `KUNDE`

### Kalkulatorenwissen
- **Wert:** 1051
- **Quelle:** Anfragen.csv, Zeile 2 → `KALKULATOR`

---

## Extrahierte Kriterien (Projektdateien)

### Gebäudetyp ✅
- **Wert:** Logistikhalle (Neubau) mit zwei Hallentrakte (Halle A und Halle B), integriertem Mezzanin, Bürobereich, Sprinklertechnik und Ladebrücken
- **Quelle:** [`414-25a_4_1_A-B_AN_--_Ansichten-Original.pdf`](https://drive.google.com/file/d/1_TQ6nRbWI27IJ3tJFE2vOfK3429yy8yR/view)
- **Textstelle:** Bauantragsplanung (Ansichten, M. 1:200, Stand 08.01.2024) zeigt „Neubau Halle Ladbergen" mit Nord-, Süd-, Ost- und Westansichten. Hallentrakt A und Hallentrakt B klar beschriftet. Westansicht zeigt „Pfenning"-Branding mit farbigen Sandwichfassaden. Sprinklertechnik-Bereiche an beiden Hallenstirnseiten.
- **Bestätigt durch:**
  - [`414-24a_4_1_A-B_SC_--_Schnitte-Original.pdf`](https://drive.google.com/file/d/1zNf3tXhW_5qZjPJXsaTvPpTL48Cj_qE-/view) (Schnitte A, B, C): Schnitt A zeigt Gesamtquerschnitt Halle A-B, Schnitt B zeigt Halle A mit Mezzanin, Palettenübergabeplätzen und Lastauffzug, Schnitt C zeigt Halle B.
  - [`2024-01-24 Unterlagen für die stat. Vorbemessung..pdf`](https://drive.google.com/file/d/1PvyP5T6VOs6TwUOu5_weVfVJqS0w_aLg/view) (Grundriss Erdgeschoss Halle A-B): Zeigt Speditionsgebäude Bestand (Büro), Bestandshalle, Sprinklertank (SP-00-01, NRF 107,51 m²), Ladebrücken (Nr. 15–18).
  - Angebot 40758.60 Vorbemerkungen: Aufteilung „Klasse 1: Halle A, Klasse 2: Halle B". Hinweis „SW-Fassaden: alle weiß dargestellten Flächen sind als Beton-Sandwichfassaden angeboten".
- **Hinweis:** Das Projekt ist als DGNB-Logistikgebäude (NLO 23) klassifiziert mit Ziel DGNB Gold. Bauherr ist LOG Cube I S.a r.l. (Luxemburg), Architekt Krüger Consult GmbH, Generalunternehmer Köster GmbH (Projekt-Nr. 24-0040-1-46). Neben dem Neubau existiert eine Bestandshalle auf dem Grundstück.

### Höhe ✅
- **Wert:** OK Attika +17,00 m (Lastzusammenstellung) / +17,000 m (Bauantragsplanung); UK Binder Tiefpunkt +14,20 m; UK Binder Hochpunkt +14,88 m; OK-Stütze Hochpunkt +16,05 m; OK-Stütze Tiefpunkt +15,55 m; OK-Stütze Giebel/Eck +15,80 m; ±0,000 = 49,85 m üNN
- **Quelle:** [`2024-01-26 kommentierte Lastzusammenstellung - Fundamentlasten V2.02.pdf`](https://drive.google.com/file/d/1aob6FMDCY3f-4tm5MzAd5S6zRoPLwWUn/view)
- **Textstelle:** Lastzusammenstellung (Köster, 18.01.2024, mit handschriftlichen Eintragungen von Dipl.-Ing. Tim Kammermann, REKERS, vom 26.01.2024): „Attikahöhe h = 17,00 m". Gebäudehöhe „10 m < H < 18 m" für Windlastberechnung bestätigt.
- **Bestätigt durch:**
  - [`414-24a_4_1_A-B_SC_--_Schnitte-Original.pdf`](https://drive.google.com/file/d/1zNf3tXhW_5qZjPJXsaTvPpTL48Cj_qE-/view) (Schnitt B – Halle A): Höhenangaben „+17,000 OK Attika Halle", „+14,200 UK Binder Tiefpunkt", „+14,880 UK Binder Hochpunkt", „+2,200 OK FB Mezzanin", „±0,000".
  - [`Angebot Nr. 40758.60.pdf`](https://drive.google.com/file/d/1FTYurRqIVqsy-YtSOPYiD6ZPClWAT_kL/view) Positionen: Pos. 1.2.01 „Binderaußenstütze im Hochpunkt, OK-Stütze = +16,05 m"; Pos. 1.2.07 „Innenstütze unter Abfangträger im Tiefpunkt, OK-Stütze = +15,55 m"; Pos. 1.2.13 „Giebel- und Eckstütze, OK-Stütze = +15,80 m im Mittel".
- **Hinweis:** Das Gebäude liegt am oberen Rand der Windlastkategorie „10 m < H < 18 m" mit exakt 17,00 m Attikahöhe. Die Differenz zwischen UK Binder Hochpunkt (+14,88 m) und Tiefpunkt (+14,20 m) beträgt 0,68 m und entspricht der Dachneigung von ca. 3,0% (1,718°) laut Lastzusammenstellung.

### Kran ❌
- **Wert:** Nicht gefunden
- **Quelle:** Alle Angebotsversionen (40758.51, 40758.60), Lastzusammenstellung, Bauantragsplanung (Schnitte, Ansichten, Grundriss) und DGNB-Baubeschreibung durchsucht. Keine Erwähnung von Kran, Laufkran, Kranbahn oder Kranlasten.
- **Hinweis:** Das Gebäude ist als Logistikhalle mit Gabelstaplerbetrieb konzipiert. Die Lastzusammenstellung listet Stapler FL3 (Eigengewicht 44 kN, Hublast 25 kN) als Nutzfahrzeug. Die Schnittdarstellungen zeigen Palettenübergabeplätze und Mezzanin-Regale — typische Logistikinfrastruktur ohne Kranbedarf. Konsistent mit dem DGNB-Nutzungsprofil „Neubau Industriebauten Logistikgebäude" (NLO 23).

### Dachlasten ✅
- **Wert:**
  - Dach Eigengewicht (Halle) = 0,420 kN/m² (Trapezblech 0,150 + Mineralwolle-Dämmung d=16 cm 0,240 + PVC-Folie 0,030)
  - Installation = 0,200 kN/m² (Sehr Schwer)
  - PV-Anlage = 0,200 kN/m² (Mittel; lt. FLB S. 14: 0,30 kN/m²)
  - Gründach = 1,200 kN/m² (Schwer, ca. 50% der Dachfläche)
  - Σ Dachlasten Halle = 1,600 kN/m² (ohne Klima)
  - Schnee: Schneelastzone 1 (SLZ1), sk = 0,650 kN/m², sl = 0,544 kN/m²
  - Wind: Windlastzone 2 (WLZ2), Binnenland, wd = 0,160 kN/m²
  - Σ Dachlast Halle gesamt = 2,724 kN/m² (inkl. Klima)
- **Quelle:** [`2024-01-26 kommentierte Lastzusammenstellung - Fundamentlasten V2.02.pdf`](https://drive.google.com/file/d/1aob6FMDCY3f-4tm5MzAd5S6zRoPLwWUn/view)
- **Textstelle:** Standardisiertes Lastzusammenstellungsformular (Köster GmbH, Projekt-Nr. 24-0040-1-46, 18.01.2024) mit detaillierter Aufschlüsselung aller Dach-Eigengewichte, Nutzlasten und Klimalasten. Abschnitt „Dachlasten / Halle" listet: „Installation a: Sehr Schwer → 0,200 KN/m²", „PV-Anlage: Mittel → 0,200 KN/m²", „Gründach: Schwer → 1,200 KN/m²". REKERS-Kommentar: „Lt. FLB, S. 14: 0,30 kN/m²" bei PV-Anlage und „Gründach auf ca. 50% der Dachfläche".
- **Bestätigt durch:** Separate Dachlasten-Bereiche für „Technik/SPZ" (1,300 kN/m²) und „Bürovorbau" (0,100 kN/m²) im selben Dokument. Die REKERS-Eintragungen (handschriftlich, T. Backs, 26.01.2024) zeigen aktive Prüfung der Köster-Vorgaben mit Kommentaren wie „Last ist zu gering (vgl. BV Rossmann, Burgwedel)" bei der Dämmungslast.
- **Hinweis:** Die Dachlastwerte sind ungewöhnlich hoch (2,724 kN/m² gesamt) aufgrund des schweren Gründachs (1,200 kN/m²). SLZ1 statt SLZ2 — Ladbergen liegt im Norddeutschen Tiefland mit geringerer Schneelast. Die REKERS-Kommentare deuten auf eine kritische Prüfung der Lastannahmen hin, insbesondere bei der Dämmungslast und PV-Anlagenlast.

### Baustoff ✅
- **Wert:** Spannbeton-Parallelbinder (L=24,00 m, h=1,10 m / L=16,00 m, h=1,10 m); Spannbeton-Abfangträger (l ca. 16,00 m); Stb.-Fertigteile Betongüte C50/60 (Stützen) und C35/45 (Fundamentplatten); Betonstahl B500(A); Spannstahl ST1660/1860 nach DIN 488; Beton nach DIN EN 206-1/DIN 1045-2 unter Verwendung von Portlandzement CEM I
- **Quelle:** [`Angebot Nr. 40758.60.pdf`](https://drive.google.com/file/d/1FTYurRqIVqsy-YtSOPYiD6ZPClWAT_kL/view), Vorbemerkungen Seite 2 (Abschnitte Beton, Bewehrung)
- **Textstelle:**
  - Beton: „Die Ausführung der Betonarbeiten erfolgt nach DIN EN 206-1/ DIN 1045-2 unter Verwendung von Portlandzement."
  - Bewehrung: „Betonstahl B500(A), Spannstahl ST1660/1860 nach DIN 488."
  - Pos. 1.1.1: „Spannbeton-Parallelbinder, L = 24,00, h = 1,10 m" (45 Stück, Halle A)
  - Pos. 1.1.5: „Spannbeton-Abfangträger, l ca. 16,00 m" (24 Stück)
  - Stützen: durchgehend „Betongüte: C50/60" (Pos. 1.2.01–1.2.20)
- **Bestätigt durch:**
  - [`Angebot Nr. 40758.51.pdf`](https://drive.google.com/file/d/1nRiFVEKurcZMXhAMICzqWBflPWnxI2sI/view) (Vorgängerversion): identische Beton- und Bewehrungsspezifikationen. Pos. 8–18 zeigen „Betongüte: C50/60" für Stützen, Pos. 11 „Betongüte: C35/45" für Stütze mit Fundamentplatte. Pos. 1-1: Spannbeton-Parallelbinder h=1,35 m (spätere Version 40758.60 reduziert auf h=1,10 m).
  - Feuerwiderstandsklassen: Binder/Riegel/Abfangträger F90; Stützen/Unterzüge/Deckenplatten F90; Brandwandplatten F90 (beide Angebote identisch).
- **Hinweis:** Zwischen den Angebotsversionen 40758.51 (23.02.2024) und 40758.60 (08.03.2024) wurde die Binderhöhe von h=1,35 m auf h=1,10 m reduziert, was auf eine Optimierung der Dachkonstruktion hindeutet. Die Stützenbetongüte C50/60 ist konsistent durch alle Positionen beider Angebote. Halbmontage-Pi-Plattendecke (Pos. 19 in 40758.51) mit 120 kg Spannstahl bestätigt den Einsatz vorgespannter Elemente.

### Dachbegrünung ✅
- **Wert:** Gründach, schwer (1,200 kN/m²), auf ca. 50% der Dachfläche der Halle; auch auf Technik/SPZ-Bereich (1,200 kN/m²); nicht auf Bürovorbau
- **Quelle:** [`2024-01-26 kommentierte Lastzusammenstellung - Fundamentlasten V2.02.pdf`](https://drive.google.com/file/d/1aob6FMDCY3f-4tm5MzAd5S6zRoPLwWUn/view)
- **Textstelle:** Abschnitt „Dachlasten / Halle": „Gründach: Schwer → 1,200 KN/m²". REKERS-Kommentar (handschriftlich): „Gründach auf ca. 50 % der Dachfläche". Abschnitt „Dachlasten / Technik/SPZ": ebenfalls „Gründach: Schwer → 1,200 KN/m²". Abschnitt „Dachlasten / Bürovorbau": „Gründach: keine → 0,000 KN/m²".
- **Bestätigt durch:** [`DGNB Pfenning Ladbergen.pdf`](https://drive.google.com/file/d/1xCY3JMijxR0Nwsr0KgvQ48NITUeyMD5K/view) (Baubeschreibung/Ausschreibung Los 2): DGNB-Zertifizierung „Neubau Industriebauten Logistikgebäude Version 2023 (NLO 23)" mit Ziel DGNB Gold. Die Nachhaltigkeitsanforderungen umfassen ENV 1.2 (Risiken für die lokale Umwelt) und ENV 1.3 (Umweltverträgliche Materialgewinnung) — DGNB Gold erfordert typischerweise extensive Dachbegrünung.
- **Hinweis:** Die Gründachlast von 1,200 kN/m² entspricht einem schweren extensiven Gründach (typisch: 12–15 cm Substratschicht). Dies ist ein signifikanter Kostenfaktor für die Tragwerksplanung — die Gründachlast macht 44% der Gesamt-Nutzlasten auf dem Dach aus (1,200 von 2,724 kN/m²). Die Beschränkung auf 50% der Dachfläche deutet auf eine wirtschaftliche Optimierung hin, bei der die verbleibenden 50% für PV-Anlagen und unbelastete Bereiche vorgesehen sind.

---

## Beobachtungen

Dieses Projekt ist durch seine ungewöhnlich umfangreiche Dokumentation und hohe Angebotsumme (€4,68 Mio.) eines der größten im Testdatensatz. Die zwei Hallentrakte A und B werden über vier Unterordner (16946, 17013, 17146, 18555) mit insgesamt 69 L0-Dateien und 282 extrahierten Anhängen dokumentiert — ein Multiplikator von 5,1x.

Das Angebot entwickelte sich über mindestens zwei Versionen: 40758.51 (23.02.2024, an Kögel + Nunne Bau GmbH) und 40758.60 (08.03.2024, an GE.werk GmbH). Der Adressatenwechsel deutet auf einen Wechsel des Generalunternehmers/Koordinators hin. Zwischen den Versionen wurde die Binderhöhe von h=1,35 m auf h=1,10 m reduziert — eine wesentliche Konstruktionsänderung.

Die Lastzusammenstellung (Köster GmbH, 18.01.2024) mit handschriftlichen REKERS-Kommentaren (T. Backs, 26.01.2024) zeigt eine aktive Prüfung der Lastannahmen mit kritischen Anmerkungen wie „Last ist zu gering (vgl. BV Rossmann, Burgwedel)" bei der Dämmungslast und Querverweise auf das Funktionale Leistungsbuch (FLB).

Die DGNB Gold-Zertifizierung (NLO 23) mit Taxonomie-Konformität und die schwere Dachbegrünung auf 50% der Fläche sind für REKERS-Projekte eher untypisch und erhöhen die Tragwerksanforderungen signifikant. Kein Kran ist vorgesehen — das Gebäude ist als reine Logistik-/Distributionshalle mit Gabelstaplerbetrieb (FL3) und Mezzanin-Regalsystem konzipiert.

---

## Quelle

- **Datenverzeichnis:** `~/Documents/projects/billable/REKERS__poc/data/2026-01-28_testdaten/40758/`
- **Design-Doc:** [Feasibility Report, Teil 2](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/design-doc-feasibility-report)
- **Session:** de4f90c7-8925-4d44-a4c8-0ac36ff9de3f

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

