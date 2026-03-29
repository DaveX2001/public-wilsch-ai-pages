---
publish: true
---

# Kriteriennachweis — Projekt 41807: NB Logistikhalle Spedition Neukirchen
[[client-rekers]]

Nachweis für Design-Doc Teil 2. Datenverfügbarkeit pro Kriterium mit Quelldateien und extrahierten Werten.

Projekt: 41807 | Datum: 2025-01-21 | Wert: €1.282K

---

## Untersuchte Daten

| Ebene | Anzahl | Details |
|-------|--------|---------|
| L0: Ordnerdateien | 23 | 21 EML, 2 PDF |
| L2: EML-Anhänge | 48 | 38 PDF, 1 XLSX, 9 Bilder/sonstige |

Multiplikator: 3,1x (L0 vs. L0+L2)

---

## Übersicht

| Kriterium | Status | Wert | Quellebene |
|-----------|--------|------|------------|
| Bauort | ✅ CSV | Niederzissen, 56651, D | L1 |
| Kundenreferenz | ✅ CSV | 14244 | L1 |
| Kalkulatorenwissen | ✅ CSV | 1014 | L1 |
| Gebäudetyp | ✅ | Logistikhalle (Spedition) mit 2-gesch. Büroeinbau | L2 |
| Höhe | ✅ | OK Attika +10,26 m; OK Brandwand +10,38 m; UK Binder +8,50 m | L2 |
| Kran | ❌ | Kein permanenter Hallenkran | — |
| Dachlasten | ✅ | Blech+Aufbau 0,41; PV 0,25; Installation 0,15 kN/m²; WLZ2; SLZ1 | L2 |
| Baustoff | ✅ | Spannbeton (Dachbinder F90); Stb.-Fertigteile (Stützen F90, Wände) | L2 |
| Dachbegrünung | ❌ | Nicht gefunden | — |

**7/9 nachgewiesen** (3 strukturiert + 4 extrahiert). 2 nicht vorhanden.

---

## Strukturierte Kriterien (Anfragen.csv)

### Bauort
- **Wert:** Niederzissen, 56651, D
- **Quelle:** Anfragen.csv, Zeile → `BAUVORHABEN_ORT`, `PLZ`, `NATION`

### Kundenreferenz
- **Wert:** 14244
- **Quelle:** Anfragen.csv, Zeile → `KUNDE`

### Kalkulatorenwissen
- **Wert:** 1014
- **Quelle:** Anfragen.csv, Zeile → `KALKULATOR`

---

## Extrahierte Kriterien (Projektdateien)

### Gebäudetyp ✅
- **Wert:** Logistikhalle (Speditionsgebäude) mit integriertem 2-geschossigem Büroeinbau, 4 Hallenbereiche (Brandabschnitte in Achsen 6, 11 und I)
- **Quelle:** [`Anlage 4.5_2306_NZ_IS_GU-Vergabe_Anlage_05_Bauantrag_Schnitte.pdf`](https://drive.google.com/file/d/1hmcHNwpmTcGl-XRVw0lbZN_Nn5MfxPup/view)
- **Textstelle:** Bauantragsschnitte (GOLDBECK Südwest GmbH, Plan KO1149) zeigen Logistikhalle mit 4 Brandabschnitten, 2-geschossigem Büroeinbau (EG + OG) im Bereich Achse 8-11/P-Q. Schnitte 1-3 im Maßstab 1/100 mit Gesamtlänge 55,71 m (10 Felder à 5,48 m). Bauherr: Logistik und Lagerhaus, Gebrüder Neukirchen GmbH Co.KG, Bad Neuenahr-Ahrweiler.
- **Bestätigt durch:**
  - [`Leistungsbeschreibung_Kurzversion.pdf`](https://drive.google.com/file/d/1d7hHZ41hQUEZOVUyHBkDM4FIW54QJ5Lo/view): „Stahlbetonfertigteile" als Gewerk bestätigt Fertigteilhalle.
  - [`ANL01_24_0823_5422_Rekers_20250128.pdf`](https://drive.google.com/file/d/13J6hUhmv2FiypMdg8JKBuKPaCEQ0h8Tl/view) (Anlage 1 zum VHP): „Dachbinder F90 in allen vier Hallenbereichen (Achsen A bis Q)" und „Sandwich-Wandelemente des Einbaus aus Stahlbeton mit Kerndämmung" bestätigen 4 Hallenbereiche mit Büroeinbau.
  - [`56651 Niederzissen.pdf`](https://drive.google.com/file/d/1QMBCAf-uFrE6TJA41b5HqFgQMRJwdsf6/view) (Frachtanfrage): „Baustelle: 56651 Niederzissen, Im Schülert" mit 28 Bindern, 70 Stützen, Wände im Innenlader ca. 1000 to — Dimensionierung einer großen Logistikhalle.
- **Hinweis:** Entwurfsverfasser ist GOLDBECK Südwest GmbH (Niederlassung Koblenz), GU ist Köster GmbH (Hochbau Bielefeld Logistikimmobilien). Bauvorhaben „NB Logistikhalle Spedition Neukirchen" ist eine Speditions-Logistikhalle für die Gebrüder Neukirchen in Niederzissen.

### Höhe ✅
- **Wert:** OK Attika +10,26 m (299,485 m ü.NN); OK Brandwand +10,38 m (299,56 m ü.NN); UK Dachkonstruktion (Auflager) +8,50 m (297,70 m ü.NN); Büroeinbau OG +6,75 m; OF Gelände ±0,00 m (289,20 m ü.NN)
- **Quelle:** [`Anlage 4.5_2306_NZ_IS_GU-Vergabe_Anlage_05_Bauantrag_Schnitte.pdf`](https://drive.google.com/file/d/1hmcHNwpmTcGl-XRVw0lbZN_Nn5MfxPup/view)
- **Textstelle:** Schnitt 2 und Schnitt 3 (1/100) zeigen konsistente Höhenangaben: „OK Attika +10,26 = 299,485 m ü.NN", „OK Brandwand +10,38 = 299,56 m ü.NN", „+8,50 = 297,70 m ü.NN" (Dachkonstruktionsebene am Auflager). Gesamtgebäudehöhe ca. 10,45 m ab OF Gelände.
- **Bestätigt durch:**
  - [`Anlage 4.8_2306_NZ_IS_GU-Vergabe_Anlage_08_Vorstatik.pdf`](https://drive.google.com/file/d/17iViQt1X8Hgj661kGTo52o81eCYv-_VT/view) (Vorstatik GOLDBECK): Spannbetonbinder mit h_First = 1,41 m und h_Auflager = 1,125 m. Schnittdarstellungen bestätigen die Ebenen aus dem Bauantrag.
  - [`56651 Niederzissen.pdf`](https://drive.google.com/file/d/1QMBCAf-uFrE6TJA41b5HqFgQMRJwdsf6/view) (Frachtanfrage): „Stützen ohne Fundamentplatte, Länge bis ca. 12,50 m" — konsistent mit Stützenlänge von Fundamentsohle (-1,50 m) bis OK Stütze (+11,00 m).
- **Hinweis:** Bezugshöhe 289,20 m ü.NN = ±0,00 (OF Gelände). Die OK Brandwand (+10,38 m) liegt 0,12 m über OK Attika — Brandwand ragt über Dachkante hinaus (baurechtlich erforderlich). Dachneigung 3 % (aus ANL01).

### Kran ❌
- **Wert:** Kein permanenter Hallenkran
- **Quelle:** Alle verfügbaren Dokumente durchsucht: ANL01 (Anlage 1 zum VHP), Leistungsbeschreibung_Kurzversion, Vorstatik (Plan Stützen und Fundamente / Dachtragwerk), Bauantrag Schnitte und Grundriss, Frachtanfrage. Keine Erwähnung von Laufkran, Kranbahn oder permanenter Krananlage.
- **Hinweis:** In Angebot 41807.2 (Nachtrag) wird unter Pos. 4.3 ein „Montagekran" für die Montage der Fertigteilwand erwähnt — dies ist REKERS' eigener Mobilkran für die Baustellenmontage, kein permanenter Gebäudekran. Die Vorstatik-Grundrisse zeigen keine Kranbahn in der Hallenstruktur. Die Lastannahmen enthalten keine Kranlasten. Als Speditions-Logistikhalle (Umschlag/Lagerung, kein Schwerlastbetrieb) ist das Fehlen eines Hallenkrans plausibel.

### Dachlasten ✅
- **Wert:**
  - Eigenlasten: Blech + Aufbau g = 0,41 kN/m² (Dämmung/Abdichtung 0,15 + Trapezblech 0,20 + Sonstiges 0,06); davon Blech 135/0,88 mm
  - Nutzlasten: PV-Anlage 0,25 kN/m²; Installation 0,15 kN/m²
  - Wind: Windlastzone WLZ 2, w = 0,14 kN/m²
  - Schnee: Schneelastzone SLZ 1, s = 0,6 kN/m² (auf PV)
  - Erdbebenzone: 1
- **Quelle:** [`ANL01_24_0823_5422_Rekers_20250128.pdf`](https://drive.google.com/file/d/13J6hUhmv2FiypMdg8JKBuKPaCEQ0h8Tl/view) (Anlage 1 zum Verhandlungsprotokoll, Köster GmbH), Seite 3
- **Textstelle:** „Lastannahmen: Eigenlasten: Dämmung/Abdichtung: 0,15 kN/m², Trapezblech: 0,20 kN/m². Nutzlasten: PV-Anlage: 0,25 kN/m², Installation: 0,15 kN/m². Schneelastzone: 1. Erdbebenzone: 1."
- **Bestätigt durch:**
  - [`Anlage 4.8_2306_NZ_IS_GU-Vergabe_Anlage_08_Vorstatik.pdf`](https://drive.google.com/file/d/17iViQt1X8Hgj661kGTo52o81eCYv-_VT/view) (Vorstatik GOLDBECK): Lastannahmen-Kasten im Plan: „Blech + Aufbau: g = 0,11+0,3 = 0,41 kN/m²", „PV: g = 0,25 kN/m²", „Wind WLZ 2: w = 0,14 kN/m²", „Schnee SLZ 1: s = 0,6 kN/m² (auf PV)", „Installation: p = 0,15 kN/m²". Detaillierter als ANL01 mit konkretem Wind- und Schneelastwert.
- **Hinweis:** Schneelastzone 1 und Windlastzone 2 für Niederzissen (Rhein-Eifel-Region) plausibel. PV-Anlage auf dem gesamten Flachdach vorgesehen (aus Leistungsbeschreibung_Kurzversion: „einschl. Berücksichtigung der Photovoltaik-Anlage auf dem gesamten Flachdach"). Die Vorstatik liefert die präziseren Werte mit Wind 0,14 kN/m² und Schnee 0,6 kN/m².

### Baustoff ✅
- **Wert:** Spannbeton-Dachbinder F90 (l ≈ 27,4–28,0 m, h_First = 1,41 m, h_Auflager = 1,125 m, I-Querschnitt); Stahlbeton-Fertigteile (Stützen F90, Brandwände F90, Sandwich-Wandelemente D=20 cm mit Kerndämmung); Spannbetonhohldiele SBHD d=200 mm (Decken Büroeinbau)
- **Quelle:** [`ANL01_24_0823_5422_Rekers_20250128.pdf`](https://drive.google.com/file/d/13J6hUhmv2FiypMdg8JKBuKPaCEQ0h8Tl/view) (Anlage 1 zum VHP), Seiten 2 und 5
- **Textstelle:** „Dachbinder F90 in allen vier Hallenbereichen (Achsen A bis Q) als Spannbetonbinder" (S. 5). „Stützen F90 aus Stahlbetonfertigteilen in den Achsen 1, 6 und 11 sowie A, I und Q" (S. 2). „Sandwich-Wandelemente des Einbaus aus Stahlbeton mit Kerndämmung (D = 20 cm) in F90" (S. 4). „Brandwände F90 in ganzer Höhe aus Stahlbetonfertigteilen in den Achsen 6, 11 und I" (S. 3).
- **Bestätigt durch:**
  - [`Anlage 4.8_2306_NZ_IS_GU-Vergabe_Anlage_08_Vorstatik.pdf`](https://drive.google.com/file/d/17iViQt1X8Hgj661kGTo52o81eCYv-_VT/view): Querschnittszeichnung „Spannbetonbinder Pos. 300" mit I-Profil, b/h = 24/50 cm (Ortgang Pos. 301). Legende: „Stahlbeton- oder Spannbeton-Binder", „SBHD = Spannbetonhohldiele". Binder 27,4 m lang, 26 to (ACHTUNG-Vermerk im Plan).
  - [`56651 Niederzissen.pdf`](https://drive.google.com/file/d/1QMBCAf-uFrE6TJA41b5HqFgQMRJwdsf6/view) (Frachtanfrage): „Binder, Länge bis ca. 28,00 m, Stückgewicht ca. 22-24 to, Anzahl ca. 28 Stk." — leichte Abweichung zur Vorstatik (27,4 m / 26 to), da Frachtanfrage konservative Transportmaße angibt.
  - [`Leistungsbeschreibung_Kurzversion.pdf`](https://drive.google.com/file/d/1d7hHZ41hQUEZOVUyHBkDM4FIW54QJ5Lo/view): „Stahlbetonfertigteile" als Gewerk, „prüffähige statische Berechnung unter Berücksichtigung von EC 8".
- **Hinweis:** Konkrete Betongüte (z.B. C50/60) und Stahlgüte (z.B. B500(A), ST1660/1860) sind in den verfügbaren Dokumenten nicht explizit angegeben. Die Bezeichnung „Spannbetonbinder" impliziert Spannstahl (typischerweise ST1660/1860 nach REKERS-Standard). Die Leistungsbeschreibung verweist auf „gemäß statischer Berechnung" für die Dimensionierung.

### Dachbegrünung ❌
- **Wert:** Nicht gefunden
- **Quelle:** Alle Lastannahmen-Dokumente durchsucht (ANL01, Vorstatik). Lastannahmen vollständig aufgelistet ohne Gründachbelastung: Dämmung/Abdichtung 0,15 + Trapezblech 0,20 + PV 0,25 + Installation 0,15 kN/m². Keine Erwähnung von Dachbegrünung, Gründach, extensiver/intensiver Begrünung oder Substratlasten.
- **Hinweis:** Standardmäßiges Industrieflachdach mit PV-Anlage. Die Lastannahmen in der Vorstatik sind explizit und vollständig — eine Gründachlast (typischerweise 0,80–1,20 kN/m²) fehlt vollständig. Für eine Speditions-Logistikhalle ohne DGNB-Zertifizierungsanspruch ist das Fehlen von Dachbegrünung erwartungsgemäß.

---

## Beobachtungen

Dieses Projekt zeichnet sich durch eine ungewöhnlich vollständige GU-Vertragsdokumentation aus. Die Köster GmbH als Generalunternehmer hat das Verhandlungsprotokoll mit umfangreichen Anlagen (Vorstatik, Bauantragsplanung, Brandschutzkonzept, Geogutachten) bereitgestellt, was eine hohe Quellendichte für die Kriterienextraktion ermöglicht. Der Entwurfsverfasser GOLDBECK Südwest lieferte die Planunterlagen inkl. detaillierter Vorstatik mit Lastannahmen-Kasten.

Die Angebotsentwicklung zeigt drei Stufen: Hauptangebot vom 30.01.2025 (Pauschalpreis 1.160.000 € nach Verhandlung), Ergänzungs-Angebot 41807.1 vom 21.02.2025, Ergänzungs-Angebot 41807.2 vom 19.06.2025 (Nachträge 15.198,20 € für Gründungshöhenänderung, Sandwichwandausführung, Mehr-/Minderleistungen, Wandplatte Einfahrbereich, Stahltreppenturmfundament).

Auffällig ist die Erdbebenzone 1 — Niederzissen liegt in der Eifel-Region nahe dem Laacher-See-Vulkangebiet. Die Leistungsbeschreibung fordert explizit „Berücksichtigung von EC 8 (Erdbebensicherheit)" für die Tragwerksplanung.

Die Frachtanfrage aus dem Werk Spelle zeigt das Gesamtvolumen: ca. 28 Binder (22-24 to, 28 m), 70 Stützen (600 to), 250 to Riegel, 1000 to Wände — insgesamt rund 1.870 to Stahlbetonfertigteile. Transport von Spelle (Emsland) nach Niederzissen (Eifel) = 265 km.

Kein Hallenkran dokumentiert — plausibel für eine Speditions-Logistikhalle (Umschlagbetrieb mit Gabelstaplern, nicht Schwerlastfertigung). Keine Dachbegrünung — kein DGNB-Bezug in den Unterlagen.

---

## Quelle

- **Datenverzeichnis:** `~/Documents/projects/billable/REKERS__poc/data/2026-01-28_testdaten/41807/`
- **Design-Doc:** [Feasibility Report, Teil 2](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/design-doc-feasibility-report)
- **Session:** b35c5ce8-07e1-449c-88a2-2d651676d5b5

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

