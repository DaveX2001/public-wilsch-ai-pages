---
publish: true
---

# Kriteriennachweis — Projekt 38882: VGP Park Erfurt 3
[[client-rekers]]

Nachweis für Design-Doc Teil 2. Datenverfügbarkeit pro Kriterium mit Quelldateien und extrahierten Werten.

Projekt: 38882 | Datum: 2022-03-15 | Wert: €3.494K

---

## Untersuchte Daten

| Ebene | Anzahl | Details |
|-------|--------|---------|
| L0: Ordnerdateien | 42 | 41 EML, 1 ZIP |
| L2: EML-Anhänge | 47 | 30 PDF, 5 XLSX, 12 sonstige |
| _extracted (ZIP) | 39 | 25 PDF, 14 sonstige (XLSX, DOC, DOCX) |

Multiplikator: 3,0x (L0 vs. L0+L2+ZIP)

---

## Übersicht

| Kriterium | Status | Wert | Quellebene |
|-----------|--------|------|------------|
| Bauort | ✅ CSV | Erfurt-Stotternheim, 99195, D | L1 |
| Kundenreferenz | ✅ CSV | 103166 | L1 |
| Kalkulatorenwissen | ✅ CSV | 1014 | L1 |
| Gebäudetyp | ✅ | Logistikhalle (Gewerbehalle) mit Büro- und Sozialflächen, ca. 27.980 m² | L2 |
| Höhe | ✅ | Attika +14,00 m (REKERS); +14,50 m (Architektenplanung); First +13,34 m | L2 |
| Kran | ❌ | Nicht vorhanden — Gabelstaplerbetrieb | — |
| Dachlasten | ✅ | Dachlast 0,35; PV 0,20; Installation 0,15 kN/m²; WZ2; SLZ2 | L2 |
| Baustoff | ✅ | Stb.-Fertigteile; Portlandzement CEM I; B500(A); ST1660/1860 | L2 |
| Dachbegrünung | ❌ | Explizit ausgeschlossen (0,00 kN/m²) | L2 |

**7/9 nachgewiesen** (3 strukturiert + 4 extrahiert). 2 nicht vorhanden.

---

## Strukturierte Kriterien (Anfragen.csv)

### Bauort
- **Wert:** Erfurt-Stotternheim, 99195, D
- **Quelle:** Anfragen.csv, Zeile 2 → `BAUVORHABEN_ORT`, `PLZ`, `NATION`

### Kundenreferenz
- **Wert:** 103166
- **Quelle:** Anfragen.csv, Zeile 2 → `KUNDE`

### Kalkulatorenwissen
- **Wert:** 1014
- **Quelle:** Anfragen.csv, Zeile 2 → `KALKULATOR`

---

## Extrahierte Kriterien (Projektdateien)

### Gebäudetyp ✅
- **Wert:** Logistikhalle (Gewerbehalle) mit Büro- und Sozialflächen, Sprinklertank und -zentrale; ca. 27.980 m² Neubaufläche
- **Quelle:** [`04-Ansichten.pdf`](https://drive.google.com/file/d/1VJm9zzBxtJjrE06Y7nG1wrILsUmjUUXE/view)
- **Textstelle:** Plankopf: „Neubau einer Gewerbehalle mit Büro- und Sozialflächen, Sprinklertank und -zentrale, sowie PKW- und LKW Stellplätzen". Grundriss zeigt Hallenstruktur mit Achsraster 1–9 (A–J), 16,00 m Achsabstand in Querrichtung, 8,00 m in Längsrichtung. Gesamtfläche „27980 m² Neubau" im Planstempel. Lichtbänder 2,00 × 16,00 m mit RWA in regelmäßigem Raster über die gesamte Dachfläche. Drei Überladetor-Bereiche (TOR 1–3) mit Tiefhof -1,20 m. Brandwände „50cm übers Dach" unterteilen die Halle in Brandabschnitte.
- **Bestätigt durch:** [`Angebot 38882.1`](https://drive.google.com/file/d/1-maVSqRu6X6iiPgvujR5DbaDPxOLgrV3/view) Leistungsumfang: „Herstellung, Lieferung und Montage der angebotenen Stb.-Fertigteile" für „VGP Park Erfurt 3". Abschnitt 1: „Stb.-Fertigteile Hallenkonstruktion Unit 1 - Unit 3". Grünflächenplan (Genehmigungsplanung) betitelt „Neubau einer Logistikhalle".
- **Hinweis:** Das Gebäude ist Teil des VGP Park Erfurt 3 (Gewerbepark). Der Bauherr ist VGP Industriebau GmbH / VGP Park Erfurt 3 S.à.r.l. Die Halle wird in Units (1–3) mit Brandabschnitten aufgeteilt. DGNB-Zertifizierung wird angestrebt.

### Höhe ✅
- **Wert:** Attika OK +14,00 m (REKERS Angebot); Architektenplanung +14,50 m (190,00 m ü. NHN – OKFF 175,50 m ü. NHN); OK Satteldachbinder im First ca. +13,34 m; UK Satteldachbinder ca. +12,14 m; UK Abfangträger ca. +11,63 m; Büroeinbau OK Rohbeton Decke EG/1.OG: +3,87 m / +7,86 m
- **Quelle:** [`Angebot 38882.1`](https://drive.google.com/file/d/1-maVSqRu6X6iiPgvujR5DbaDPxOLgrV3/view), Seite 1 und Seite 3 (Konstruktionshöhen)
- **Textstelle:** Seite 1: „Die Attikahöhe bleibt bei OK: +14,00 m". Seite 3 (Konstruktionshöhen): „UK Abfangträger: ca. +11,63 m im Tiefpunkt", „UK Paralleldachbinder im Tiefpunkt: ca. +11,88 m", „OK Paralleldachbinder im Hochpunkt: ca. +13,46 m", „OK Satteldachbinder im First: ca. +13,34 m", „UK Satteldachbinder: ca. +12,14 m", „OK Sohle: +0,00 m; OK Gelände = +0,00 m (Anlieferung = -1,20 m)".
- **Bestätigt durch:** [`04-Ansichten.pdf`](https://drive.google.com/file/d/1VJm9zzBxtJjrE06Y7nG1wrILsUmjUUXE/view): „190,00m ü NN, Attika 14,50m" und „OKFF 175,50 m ü NN". Die VGP Standardleistungsbeschreibung (Index 6) nennt für Variante 2: „lichte Hallenhöhe ca. 11,80 m und Attika Höhe ca. 14,00 m" — konsistent mit dem REKERS-Angebot.
- **Hinweis:** Die Differenz von 0,50 m zwischen REKERS-Attika (+14,00 m) und Architekten-Attika (+14,50 m) erklärt sich durch den Unterschied zwischen OK Betonfertigteil und OK Attika inkl. Aufmauerung/Abdeckung. Das REKERS-Angebot bezieht sich auf die Fertigteil-Oberkante. Paralleldachbinder mit ca. 2,00 % Neigung, Satteldachbinder mit 3 % Obergurtneigung.

### Kran ❌
- **Wert:** Nicht vorhanden
- **Quelle:** [`Angebot 38882.1`](https://drive.google.com/file/d/1-maVSqRu6X6iiPgvujR5DbaDPxOLgrV3/view), vollständig durchsucht (16 Seiten)
- **Hinweis:** Keine Erwähnung von Kran, Laufkran, Kranbahn oder Kranlasten in Angebot, Vorbemerkungen oder Leistungspositionen. Die einzige Fahrzeug-Anprallast stammt von einem „Gabelstapler der Staplerklasse FL3 (zulässiges Gesamtgewicht = 6,9 to)". Dies ist konsistent mit dem Gebäudetyp Logistikhalle — Warenumschlag erfolgt per Gabelstapler, nicht per Hallenkran. Auch in den Architektenplanungen (Ansichten, Grundrisse) und der VGP Standardleistungsbeschreibung findet sich kein Hinweis auf Kranbahnen.

### Dachlasten ✅
- **Wert:**
  - Trapezblech, 100 mm PIR-Dämmung, Abdichtung = 0,35 kN/m²
  - Installation = 0,15 kN/m²
  - PV = 0,20 kN/m² (Aufbauhöhe h < 0,35 m)
  - Wind: Windlastzone WZ 2
  - Schnee: Schneelastzone 2
  - Dachbegrünung = 0,00 kN/m² (kommt nicht zur Ausführung)
  - Dachtrapezbleche als 3- oder 4-Feldsystem, Durchlauffaktor = 1,10
- **Quelle:** [`Angebot 38882.1`](https://drive.google.com/file/d/1-maVSqRu6X6iiPgvujR5DbaDPxOLgrV3/view), Seite 2 (Abschnitt „Dachbelastung" und „Wind- u. Schneelasten")
- **Textstelle:** „Wind- u. Schneelasten gemäß DIN EN 1991: Windlastzone WZ 2, Schneelastzone 2, Geländehöhe = 175,50 m ü. NHN". Dachbelastung: „Photovoltaikanlage = 0,20 kN/m² (Aufbauhöhe h < 0,35 m)", „Dachbegrünung = 0,00 kN/m² (kommt nicht zur Ausführung)", „Trapezblech, 100 mm PIR-Dämmung, Abdichtung = 0,35 kN/m²", „Installation = 0,15 kN/m²".
- **Bestätigt durch:** Architektenplanungen (Ansichten) zeigen Dachaufbau: „FOLIENABDICHTUNG, WÄRMEDÄMMUNG 100mm, DAMPFSPERRE, TRAPEZBLECH 200mm, BETONBINDER 1100mm" — konsistent mit den Angebotswerten. Das Gebäude befindet sich außerhalb einer Erdbebenzone (Angebot Seite 2).
- **Hinweis:** Im Vergleich zum Referenzprojekt 35764 (Gescher) ist die Dachlast ohne Abdichtung/Dämmung aufgeteilt: 35764 hatte 0,45 kN/m² Dachlast, während 38882 „0,35 kN/m² Trapezblech+Dämmung+Abdichtung" ausweist — möglicherweise geringere Dämmdicke oder anderes System. PV-Last identisch mit 0,20 kN/m². Installation identisch mit 0,15 kN/m². Wind- und Schneezonen identisch (WZ2, SLZ2).

### Baustoff ✅
- **Wert:** Stahlbeton-Fertigteile; Portlandzement CEM I nach DIN EN 206-1/DIN 1045-2; Druckfestigkeitsklasse gemäß statischer Erfordernis; Betonstahl B500(A); Spannstahl ST1660/1860 nach DIN 488; FT-Parallelgurtbinder L=24,00 m (Trägerhöhe ca. 1,10 m)
- **Quelle:** [`Angebot 38882.1`](https://drive.google.com/file/d/1-maVSqRu6X6iiPgvujR5DbaDPxOLgrV3/view), Seite 3–5 (Abschnitte „Beton", „Bewehrung", Pos. 1.01)
- **Textstelle:**
  - Beton: „Die Ausführung der Betonarbeiten erfolgt nach DIN EN 206-1/DIN 1045-2 unter Verwendung von Portlandzement CEM I. Druckfestigkeitsklasse/Betongüte gemäß statischer Erfordernis."
  - Bewehrung: „Betonstahl B500(A), Spannstahl ST1660/1860 nach DIN 488."
  - Pos. 1.01: „FT-Parallelgurtbinder, Systemachslänge L = 24,00 m, Trägerhöhe ca. 1,10 m, im Gefälle mit 2 % verlegt" — 90 Stück à €5.157,20 = €464.148,00
  - Feuerwiderstandsklassen: Binder/Riegel/Abfangträger F90; Stützen F90; Pi-Platten und Unterzüge F90; Brandwände F90; Treppenhausfertigteile F90
- **Bestätigt durch:** Überarbeitetes Anschreiben 38882.1 (Seite 2): „400 to Betonstahl sowie 45 to Spannstahl für eine Index-Verrechnung zu erwarten" — bestätigt den signifikanten Spannstahlanteil. VGP Standardleistungsbeschreibung (Kap. 3.2): „Das Tragwerk besteht aus bewehrten Betonfertigteilen und wird als Skelettbauweise realisiert."
- **Hinweis:** Im Vergleich zum Referenzprojekt 35764 (Gescher) verwendet 38882 Parallelgurtbinder (L=24,00 m) statt Satteldachbinder (L=30,00 m) — geringere Spannweite bei größerer Gesamtfläche. Die Betongüte wird projektspezifisch durch Statik bestimmt, nicht als feste Klasse (z.B. C50/60) angegeben. Expositionsklassen: Innenbauteile XC1/W0, Gründungsbauteile XC2/XF1/WF, Außenbauteile XC4/XF1/WF.

### Dachbegrünung ❌
- **Wert:** Nicht vorhanden — explizit ausgeschlossen
- **Quelle:** [`Angebot 38882.1`](https://drive.google.com/file/d/1-maVSqRu6X6iiPgvujR5DbaDPxOLgrV3/view), Seite 2 (Abschnitt Dachbelastung)
- **Hinweis:** „Dachbegrünung = 0,00 kN/m² (kommt nicht zur Ausführung)" — die Dachbegrünung wird im Angebot explizit als Lastposition aufgeführt, aber mit 0,00 kN/m² und dem Vermerk „kommt nicht zur Ausführung". Dies ist ein eindeutiger Ausschluss, kein fehlendes Datum. Der Dachaufbau (Folienabdichtung, PIR-Dämmung, Dampfsperre, Trapezblech) ist ein standardmäßiges Industrieflachdach ohne Vegetationsschicht. Der Grünflächenplan (pkb consulting, 13.01.2021) zeigt ausschließlich bodengebundene Bepflanzung (Bodendecker, Sträucher, Bäume, Wiesenflächen) — keine Dachbegrünung.

---

## Beobachtungen

Dieses Projekt ist Teil einer gebündelten Vergabe der VGP-Objekte VGP Park Halle 2 (Halle/Saale), VGP Park Hochheim und VGP Park Erfurt 3. Der Sondernachlass von €35.000 im Anschreiben bezieht sich auf diese Paketvergabe. Im _extracted/-Verzeichnis finden sich daher auch Angebote für Schwesterprojekte (38722.11 für ein anderes VGP-Objekt, 38915.1 als Pauschalangebot).

Das Angebot wurde erheblich von der Marktsituation 2022 geprägt: Betonstahl-Marktpreis 1.080 €/to (Kalkulationsbasis März 2022, aktuell Juni 2022 bei ca. 1.350 €/to), Spannstahl 1.470 €/to (aktuell ca. 1.770 €/to), Dieselbasispreis 1,60 €/l. Die vorläufigen Mehrkosten (Bewehrung €121.500 + Fracht €9.860 = €131.360) werden separat als Verrechnungsleistung ausgewiesen. Die Bindefrist war auf 30.06.2022 begrenzt.

Die Schnitte-Zeichnung im Unterordner 15026 (21099 4 SC 00 200) gehört zum VGP Park Gießen (Projekt 21099), nicht zu Erfurt 3. Sie zeigt „Gründach" auf der Zeichnung — dies bezieht sich auf das Gießener Projekt und ist für Erfurt 3 nicht relevant.

Die DGNB-Zertifizierung wird angestrebt (Angebot: „Im Hinblick auf die angestrebte DGNB-Zertifizierung wurden von uns die gleichen Vertraglichen Regelungen wie beim VGP Objekt Magdeburg Halle C zugrunde gelegt"). Im Ordner 04_DGNB finden sich Kriterienmatrix, PRO 2.1-Nachweise, RAL UZ 53 und NU-Vortext.

Im Vergleich zum Referenzprojekt 35764 (Werkhalle Gescher, €491K) ist 38882 ein Faktor 7x größeres Projekt (€3.494K) mit deutlich größerer Hallenfläche (ca. 27.980 m² vs. ca. 2.000 m²) und Parallelgurtbindern statt Satteldachbindern. Die Logistiknutzung (Gabelstapler statt Hallenkran) spiegelt sich in den fehlenden Kranlasten wider.

---

## Quelle

- **Datenverzeichnis:** `~/Documents/projects/billable/REKERS__poc/data/2026-01-28_testdaten/38882/`
- **Design-Doc:** [Feasibility Report, Teil 2](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/design-doc-feasibility-report)
- **Session:** cdd46b0f-44da-4a15-9855-b08d573d5035

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

