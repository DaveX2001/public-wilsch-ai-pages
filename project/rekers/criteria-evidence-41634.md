---
publish: true
---

# Kriteriennachweis — Projekt 41634: VGP Park Leipzig Flughafen 2 – Halle C
[[client-rekers]]

Nachweis für Design-Doc Teil 2. Datenverfügbarkeit pro Kriterium mit Quelldateien und extrahierten Werten.

Projekt: 41634 | Datum: 2024-11-11 | Wert: €5.569K

---

## Untersuchte Daten

| Ebene | Anzahl | Details |
|-------|--------|---------|
| L0: Ordnerdateien | 23 | 21 EML, 1 PDF, 1 ZIP |
| L2: EML-Anhänge | 142 | 60 PDF, 18 XLSX, 46 PNG, 12 JPG, 6 sonstige |
| _extracted (ZIP) | 16 | 8 XLSX, 5 DOC/DOCX, 3 sonstige |

Multiplikator: 7,9x (L0 vs. L0+L2+ZIP)

---

## Übersicht

| Kriterium | Status | Wert | Quellebene |
|-----------|--------|------|------------|
| Bauort | ✅ CSV | Schkopau, 06258, D | L1 |
| Kundenreferenz | ✅ CSV | 103166 | L1 |
| Kalkulatorenwissen | ✅ CSV | 1014 | L1 |
| Gebäudetyp | ✅ | Logistikhalle mit Überladegruben, 2-gesch. Büroeinbau, Hausanschlussräume | L2 |
| Höhe | ✅ | OK Attika +14,00 m; OK Satteldachbinder First +13,34 m; UK Paralleldachbinder +11,88 m | L2 |
| Kran | ❌ | Kein Kran — Gabelstapler FL3 (6,9 to) | L2 |
| Dachlasten | ✅ | PV 0,15; Installation 0,20; Trapezblech+PIR 0,35 kN/m²; WZ2; SLZ2 | L2 |
| Baustoff | ✅ | Spannbeton-Paralleldachbinder/-Satteldachbinder; Stb.-FT; B500; ST1660/1860 | L2 |
| Dachbegrünung | ❌ | Explizit ausgeschlossen (0,00 kN/m², „kommt nicht zur Ausführung") | L2 |

**7/9 nachgewiesen** (3 strukturiert + 4 extrahiert). 2 nicht vorhanden (Kran, Dachbegrünung).

---

## Strukturierte Kriterien (Anfragen.csv)

### Bauort
- **Wert:** Schkopau, 06258, D
- **Quelle:** Anfragen.csv → `BAUVORHABEN_ORT`, `PLZ`, `NATION`

### Kundenreferenz
- **Wert:** 103166
- **Quelle:** Anfragen.csv → `KUNDE`

### Kalkulatorenwissen
- **Wert:** 1014
- **Quelle:** Anfragen.csv → `KALKULATOR`

---

## Extrahierte Kriterien (Projektdateien)

### Gebäudetyp ✅
- **Wert:** Logistikhalle mit 66 Überladegruben, integriertem 2-geschossigem Büroeinbau und Hausanschlussräumen
- **Quelle:** [`Angebot.pdf`](https://drive.google.com/file/d/14D5hVNuUFZJtq_N08tEoNoqHobHfkQhw/view) (= Angebot Nr. 41634.0, Seite 5–10)
- **Textstelle:** LV-Titel „1 Stb.-Fertigteile Hallenkonstruktion" (€4.255.136,80) umfasst Paralleldachbinder, Satteldachbinder, Abfangeträger, Randriegel, Stützen, Sandwichelemente, Grubenwände und Überladebrücken. LV-Titel „2 Stb.-Fertigteile Hausanschlussraum" enthält Massivdeckenplatten und Innenwandplatten. Pos. 1.32 „FT-Grubenwand" und Pos. 1.33 „FT-HM-Deckenplatte zwischen den Überladebrücken" belegen die Überladegruben-Infrastruktur. Pos. 1.22 „Zulage zur FT-Stütze im Bereich 2-gesch. Büroeinbau" bestätigt den zweigeschossigen Bürobereich.
- **Bestätigt durch:** [`Rekers-Plan R1.pdf`](https://drive.google.com/file/d/1WqoKxNEEjBQwpr7R_IlTB8NNo4hxa2vF/view) (Grundriss EG, M 1:250) zeigt Hallenstruktur mit regelmäßigem Achsraster (Achsen A–O × 1–19), Überladegruben an den Längsseiten und Büroeinbau an Achse 1/E-F. [`Anschreiben 41634.0.pdf`](https://drive.google.com/file/d/1HX9q-TJ-14ih5h5zv6PsIuDISylBVwpK/view): „Herstellung, Lieferung und Montage von Stb.-Fertigteilen für das Bauvorhaben: VGP Park Leipzig Flughafen 2 – Halle C".
- **Hinweis:** Analoges Bauvorhaben zu „VGP Park Leipzig Flughafen 2 – Halle B" — Angebot 41634.0 Vorbemerkungen: „Die Ausführung der angebotenen FT-Konstruktion (Lastannahmen, Konstruktionshöhen, Detailpunkte etc.) erfolgt prinzipiell analog der bereits ausgeführten Halle B im VGP Park Leipzig Flughafen 2." Staplerklasse FL3 (6,9 to Gesamtgewicht) bestätigt Logistiknutzung. Hallensohle Nutzlast 70,0 kN/m², Verladebereich 35,0 kN/m².

### Höhe ✅
- **Wert:** OK Attika +14,00 m; UK Abfangeträger ca. +11,63 m; UK Paralleldachbinder Tiefpunkt ca. +11,88 m; OK Paralleldachbinder Hochpunkt ca. +13,46 m; UK Satteldachbinder ca. +12,14 m; OK Satteldachbinder First ca. +13,34 m; OK Sohle +0,00 m (Geländehöhe 125,76 m ü. NHN)
- **Quelle:** [`Angebot.pdf`](https://drive.google.com/file/d/14D5hVNuUFZJtq_N08tEoNoqHobHfkQhw/view) (Angebot Nr. 41634.0, Seite 3 — Konstruktionshöhen)
- **Textstelle:** „Konstruktionshöhen: OK Attika: +14,00 m / UK Abfangeträger: ca. +11,63 m im Tiefpunkt / UK Paralleldachbinder im Tiefpunkt: ca. +11,88 m / OK Paralleldachbinder im Hochpunkt: ca. +13,46 m / UK Satteldachbinder: ca. +12,14 m / OK Satteldachbinder im First: ca. +13,34 m / OK Sohle: +0,00 m; OK Gelände = +0,00 m (Überladegrubenbereich = -1,20 m)"
- **Bestätigt durch:** Angebot 41634.0 Pos. 1.13 (Stb.-FT-Innenstütze): „Länge ca. 14,43 m, bei OK-Stütze ca. +12,98 m im Tiefpunkt" bzw. „Länge ca. 14,91 m, bei OK-Stütze ca. +13,46 m im Hochpunkt". Bürodecken: „OK Rohbeton Decke über EG/1.OG Büroeinbau: +3,87 m / +7,86 m". UK Decke über HA-Raum: +3,05 m.
- **Hinweis:** Stützenlängen von 14,43–14,91 m (OK Köcherfundament -0,30 m bis OK Stütze +12,98/+13,46 m) repräsentieren die gesamte Konstruktionshöhe. Die Geländehöhe 125,76 m ü. NHN ist im Zusammenhang mit der Schneelastzone 2 relevant.

### Kran ❌
- **Wert:** Kein Kran. Gabelstaplerbetrieb der Staplerklasse FL3 (zulässiges Gesamtgewicht = 6,9 to).
- **Quelle:** [`Angebot.pdf`](https://drive.google.com/file/d/14D5hVNuUFZJtq_N08tEoNoqHobHfkQhw/view) (Angebot Nr. 41634.0, Seite 2)
- **Textstelle:** Unter „Anprallasten": „Aus einem Gabelstapler der Staplerklasse FL3 (zulässiges Gesamtgewicht = 6,9 to) — Auf alle eingespannten Fertigteilstützen". Keine Erwähnung von Laufkran, Kranbahn oder Kranlasten in Vorbemerkungen oder LV-Positionen.
- **Hinweis:** Konsistent mit Logistikhallentyp — Überladegruben + Gabelstapler statt Kran. Alle 14 Tore (BS-Schiebetore, 4,00/4,50 m, Pos. 1.31) bestätigen LKW-/Gabelstaplerbetrieb. Keine Kranbahnstützen, keine Kranlasten in der Lastaufstellung.

### Dachlasten ✅
- **Wert:**
  - Photovoltaikanlage = 0,15 kN/m² (Aufbauhöhe h < 0,34 m)
  - Dachbegrünung = 0,00 kN/m² (kommt nicht zur Ausführung)
  - Trapezblech, 100 mm PIR-Dämmung, Abdichtung = 0,35 kN/m²
  - Installation = 0,20 kN/m²
  - Wind: Windlastzone WZ 2, Binnenland (DIN EN 1991)
  - Schnee: Schneelastzone 2, Geländehöhe = 125,76 m ü. NHN
  - Dachtrapezbleche als 3- oder 4-Feldsystem, Durchlauffaktor = 1,10
- **Quelle:** [`Angebot.pdf`](https://drive.google.com/file/d/14D5hVNuUFZJtq_N08tEoNoqHobHfkQhw/view) (Angebot Nr. 41634.0, Seiten 1–2)
- **Textstelle:** „Dachbelastung Hallendach: Photovoltaikanlage = 0,15 kN/m² (Aufbauhöhe h < 0,34 m) / Dachbegrünung = 0,00 kN/m² (kommt nicht zur Ausführung) / Trapezblech, 100 mm PIR-Dämmung, Abdichtung = 0,35 kN/m² / Installation = 0,20 kN/m². Wind- u. Schneelasten gemäß DIN EN 1991: Windlastzone WZ 2, Binnenland / Schneelastzone 2, Geländehöhe = 125,76 m ü. NHN (= Hallensohle +-0,00 m)."
- **Bestätigt durch:** Angebot 41634.0 Pos. 1.01 (Spannbeton-Parallelgurtbinder) und Pos. 1.02 (Spannbeton-Satteldachbinder) mit Systemachslänge L = 24,00 m im Gefälle verlegt. Dachform: „Paralleldachbinder mit ca. 2,00 % Neigung verlegt, bzw. Satteldachbinder mit 3 % Obergurtneigung".
- **Hinweis:** Erdbeben nach DIN 4149-2005 bzw. EC 8: „Das Gebäude befindet sich nicht im Erdbebengebiet, bzw. es ist kein Erdbebennachweis erforderlich." Hallensohle Nutzlast = 70,0 kN/m², Verladebereich Nutzlast = 35,0 kN/m², Traglast Überladebrücken = 60 kN.

### Baustoff ✅
- **Wert:** Spannbeton-Parallelgurtbinder (l = 24,00 m, Trägerhöhe ca. 1,10 m) + Spannbeton-Satteldachbinder (l = 24,00 m, Firsthöhe ca. 1,20 m) + Spannbeton-Abfangeträger (l = 16,00 m, Trägerhöhe ca. 1,35 m); Stahlbeton-Fertigteile (Stützen, Wände, Decken, Sockel); Betonstahl B500; Spannstahl ST1660/1860 nach DIN 488; Beton nach DIN EN 206-1/DIN 1045-2 mit Portlandzement
- **Quelle:** [`Angebot.pdf`](https://drive.google.com/file/d/14D5hVNuUFZJtq_N08tEoNoqHobHfkQhw/view) (Angebot Nr. 41634.0, Seiten 3 und 5)
- **Textstelle:**
  - Beton: „Die Ausführung der Betonarbeiten erfolgt nach DIN EN 206-1/DIN 1045-2 unter Verwendung von Portlandzement. Druckfestigkeitsklasse/Betongüte gemäß statischer Erfordernis."
  - Bewehrung: „Betonstahl B500, Spannstahl ST1660/1860 nach DIN 488."
  - Pos. 1.01: „Spannbeton-Parallelgurtbinder, Systemachslänge L = 24,00 m, Trägerhöhe ca. 1,10 m, im Gefälle mit 2 % verlegt" (192 Stück, €802.348,80)
  - Pos. 1.02: „Spannbeton-Satteldachbinder, Systemachslänge L = 24,00 m, Firsthöhe ca. 1,20 m, mit 3 % Obergurtneigung" (32 Stück, €138.268,80)
  - Pos. 1.04: „Spannbeton-Abfangeträger für vorgen. Binder, Systemachslänge l = 16,00 m, Trägerhöhe ca. 1,35 m" (99 Stück, €464.230,80)
- **Bestätigt durch:** Expositionsklassen aus Vorbemerkungen: Innenbauteile XC1 (W0), Gründungsbauteile XC2 (XF1/WF), Außenbauteile XC4 (XF1/WF). Feuerwiderstandsklassen: Binder/Riegel/Abfangträger F90, Stützen F90, Pi-Platten/Unterzüge F90. Dämmung: EPS 035 dm (Sandwichfassade Überladegruben), XPS 035 dh (Sockelbereich).
- **Hinweis:** Betongüte als „gemäß statischer Erfordernis" angegeben, nicht als konkrete Druckfestigkeitsklasse. Die Kombination aus Parallelgurt- und Satteldachbindern zeigt ein differenziertes Dachtragwerk — 192 Parallelgurtbinder im Regelfeld + 32 Satteldachbinder in Sonderbereichen. Gesamtwert Hallenkonstruktion €4.255.136,80 netto (Titel 1).

### Dachbegrünung ❌
- **Wert:** Explizit ausgeschlossen — „Dachbegrünung = 0,00 kN/m² (kommt nicht zur Ausführung)"
- **Quelle:** [`Angebot.pdf`](https://drive.google.com/file/d/14D5hVNuUFZJtq_N08tEoNoqHobHfkQhw/view) (Angebot Nr. 41634.0, Seite 1)
- **Textstelle:** Unter „Dachbelastung Hallendach" explizit aufgeführt: „Dachbegrünung = 0,00 kN/m² (kommt nicht zur Ausführung)".
- **Hinweis:** Der Dachaufbau ist standardmäßiges Industrieflachdach: Trapezblech + 100 mm PIR-Dämmung + Abdichtung (0,35 kN/m²). Trotz angestrebter DGNB-Zertifizierung (VHP Abschnitt 23; EML „GERLFH2-C_Rekers_DGNB_BFT.eml") wird keine Dachbegrünung realisiert. PV-Anlage (0,15 kN/m²) kommt stattdessen zur Ausführung.

---

## Beobachtungen

Dieses Projekt ist als Analogbauwerk zur bereits ausgeführten Halle B im VGP Park Leipzig Flughafen 2 konzipiert — das Angebot 41634.0 verweist explizit darauf, dass „die Ausführung der angebotenen FT-Konstruktion prinzipiell analog der bereits ausgeführten Halle B" erfolgt. Im Projektordner findet sich sogar das VHP der Halle B (`VHP BFT- VGP Park Leipzig Flughafen 2- Halle B- Rekers-Endversion-Endversion.pdf`).

Bemerkenswert ist das differenzierte Dachtragwerk: 192 Spannbeton-Parallelgurtbinder (2 % Neigung) im Regelfeld, ergänzt durch 32 Spannbeton-Satteldachbinder (3 % Obergurtneigung) in Sonderbereichen — beide mit L = 24,00 m Systemachslänge. 99 Spannbeton-Abfangeträger (l = 16,00 m) verbinden die Primärbinder. Diese Kombination ist typisch für große Logistikflächen mit variablem Dachgefälle.

Die Überladegrubeninfrastruktur wurde zwischen Angebots- und Ausführungsplanung erweitert: ursprünglich 56 Stück, zur Ausführung 66 Stück (Ergänzungsangebot 41634.1, Titel 3). Die VHP-Vergütung zeigt 11,23 % Nachlass auf die Angebotssumme von €4.368.256,00 → Pauschalpreis €3.877.828,00 netto.

Die DGNB-Zertifizierung ist vertraglich vereinbart (VHP Abschnitt 23), mit EU-Taxonomie-Anforderung für 70 % Recyclingquote der Bau- und Abbruchabfälle (VHP Abschnitt 2.9.4). Trotz DGNB wird keine Dachbegrünung realisiert — PV-Anlage wird stattdessen installiert.

Flughafennähe wird explizit adressiert: „Anforderungen bzw. Auflagen etc. aufgrund des Bauens in Flughafennähe sind uns nicht bekannt und nicht berücksichtigt" (Vorbemerkungen Seite 1).

---

## Quelle

- **Datenverzeichnis:** `~/Documents/projects/billable/REKERS__poc/data/2026-01-28_testdaten/41634/`
- **Design-Doc:** [Feasibility Report, Teil 2](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/design-doc-feasibility-report)
- **Session:** 98386095-a7e1-42c9-b3f8-f3fa1853eea7

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

