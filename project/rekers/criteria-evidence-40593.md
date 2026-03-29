---
publish: true
---

# Kriteriennachweis — Projekt 40593: Neubau Logistikzentrum
[[client-rekers]]

Nachweis für Design-Doc Teil 2. Datenverfügbarkeit pro Kriterium mit Quelldateien und extrahierten Werten.

Projekt: 40593 | Datum: 2023-11-21 | Wert: €6.132K

---

## Untersuchte Daten

| Ebene | Anzahl | Details |
|-------|--------|---------|
| L0: Ordnerdateien | 39 | 34 EML, 5 PDF |
| L2: EML-Anhänge | 91 | 48 PDF, 12 PNG, 6 x81/D81/P83, 25 sonstige |
| _extracted (root) | 4 | 3 PDF, 1 HTML |

Multiplikator: 3,4x (L0 vs. L0+L2+root)

---

## Übersicht

| Kriterium | Status | Wert | Quellebene |
|-----------|--------|------|------------|
| Bauort | ✅ CSV | Niederlangen, 49779, D | L1 |
| Kundenreferenz | ✅ CSV | 108636 | L1 |
| Kalkulatorenwissen | ✅ CSV | 1053 | L1 |
| Gebäudetyp | ✅ | Logistikzentrum mit 4 Hallen und Mezzanine | L2 |
| Höhe | ✅ | OK Attika +14,00 m; OK Stütze +13,50 m (HP); UK-Binder +12,02/+12,18 m; OK Mezzanine +6,06 m | L2 |
| Kran | ❌ | Nicht gefunden | — |
| Dachlasten | ❌ | Nicht gefunden | — |
| Baustoff | ✅ | Spannbeton C50/60; Stb.-FT C45/55; BSt 500S+M; St1660/1860 | L2 |
| Dachbegrünung | ❌ | Nicht gefunden | — |

**6/9 nachgewiesen** (3 strukturiert + 3 extrahiert). 3 nicht vorhanden.

---

## Strukturierte Kriterien (Anfragen.csv)

### Bauort
- **Wert:** Niederlangen, 49779, D
- **Quelle:** Anfragen.csv, Zeile → `BAUVORHABEN_ORT`, `PLZ`, `NATION`

### Kundenreferenz
- **Wert:** 108636
- **Quelle:** Anfragen.csv, Zeile → `KUNDE`

### Kalkulatorenwissen
- **Wert:** 1053
- **Quelle:** Anfragen.csv, Zeile → `KALKULATOR`

---

## Extrahierte Kriterien (Projektdateien)

### Gebäudetyp ✅
- **Wert:** Logistikzentrum mit 4 Halleneinheiten (Unit 1–4) und Mezzanine-Geschoss, Überladebrücken, Treppenhäuser und Büro-/Sozialbereich
- **Quelle:** [`23-23_BA-2000_Schnitte_VA 07.12.2023.pdf`](https://drive.google.com/file/d/1SY00tKKWuSVkY1zjYFZMhxqqB789srJv/view)
- **Textstelle:** Schnitt A-A zeigt die 4 Halleneinheiten (Halle 1–4 bzw. Unit 1–4) mit jeweils 24,00 m Spannweite und 8,00 m Achsraster. OK Mezzanine = +6,06 m in jeder Halle. Plankopf: „Neubau eines Logistikzentrum", Bauherr „Logistikzentrum Niederlangen GmbH".
- **Bestätigt durch:**
  - [`23-23_BA-3000_Ansichten_VA 07.12.2023.pdf`](https://drive.google.com/file/d/1riEYKz9WmACUB6dvOaSM1WuExxB8_6Js/view): Ansichten West, Nord, Süd, Ost zeigen ein langes eingeschossiges Logistikgebäude mit Bürotrakt (verglaster Bereich links in Ansicht West) und „Landguth premium partner"-Branding. Mehrere Andockstationen (Laderampen) an der Südseite sichtbar.
  - [`23-23_E-2100_VA-21-11-2023_Schnitt A-A Halle1.pdf`](https://drive.google.com/file/d/1ss8iu2xKrmtLT_I2Po_ZRY6RyvfS9ONd/view): Entwurfsplanung bestätigt identische Hallenstruktur mit Mezzanine, RWA-Kuppeln und Abhangdecken.
  - Neutrale Anfrage (LV Stb.-Fertigteile, 84 Seiten): Pos. 1.56–1.62 beschreiben Sandwich-Pfeilerelemente, Sturzelemente und Überladebrücken-Deckenplatten — bestätigt Logistikzentrum-Funktion mit Lkw-Andockzone.
- **Hinweis:** Bauherr ist „Logistikzentrum Niederlangen GmbH" (Vertreter: Herr Alfred Saathoff, Benzstraße 1, 26632 Ihlow/Riepe). Endnutzer ist Landguth (Tiernahrungshersteller), erkennbar am Fassadenbranding.

### Höhe ✅
- **Wert:** OK Attika +14,00 m; OK Stütze +13,50 m (HP) / +13,02 m (TP); OK-Binder +13,38 m (Sattel, HP) / +13,50 m (Parallel, HP); UK-Binder +12,18 m (Sattel) / +12,02 m (Parallel, TP); OK Mezzanine +6,06 m; OK Sockel +0,30 m
- **Quelle:** [`23-23_BA-2000_Schnitte_VA 07.12.2023.pdf`](https://drive.google.com/file/d/1SY00tKKWuSVkY1zjYFZMhxqqB789srJv/view)
- **Textstelle:** Schnitt A-A (M. 1:200) zeigt konsistente Höhenangaben über alle 4 Hallen: ±0,00 (Hallensohle), +0,30 (OK Sockel), +6,06 (OK Mezzanine), +9,16 (Treppenhausdecke), +12,108 bis +12,175 (UK Dachbinder), +14,00 (OK Attika).
- **Bestätigt durch:**
  - [`23-23_E-2100_VA-21-11-2023_Schnitt A-A Halle1.pdf`](https://drive.google.com/file/d/1ss8iu2xKrmtLT_I2Po_ZRY6RyvfS9ONd/view): Entwurfsplanung bestätigt identische Höhenwerte: +14,00, +12,175, +12,108, +6,06, +5,36, +3,50/+3,60 (Abhangdecken).
  - [`23-23 LV Stb.-Fertigteile.pdf`](https://drive.google.com/file/d/1LgflBh8DxdqKYGRmMxrqeWLXpvNjIkfq/view) (REKERS-LV): Pos. 1.01 Spannbeton-Satteldachbinder OK +13,38 m, UK +12,18 m; Pos. 1.02 Paralleldachbinder OK +13,50 m, UK +12,02 m; Pos. 1.11 Traufstütze OK +13,50 m (HP), L=15,05 m; Pos. 1.16 Innenstütze OK +13,02 m (TP), L=14,57 m.
  - Angebot 40593.14 (Brandwandtore): Brandwandplatten OK = +14,40 m — Wandhöhe übersteigt Attika für Brandschutz-Anforderung.
- **Hinweis:** Zwei Bindertypen: Satteldachbinder (3,0% Obergurtneigung, First bei +13,38 m) und Paralleldachbinder (2,0% Gefälle, HP +13,50 m). Die differierenden UK-Werte (+12,18 m vs. +12,02 m) ergeben sich aus der unterschiedlichen Binderbauform. Die Abhangdecken in den Bürobereichen liegen bei +3,50/+3,60 m.

### Kran ❌
- **Wert:** Nicht gefunden
- **Quelle:** Alle Angebote (40593.14, 40593.16, 40593.30, 40593.31), beide Leistungsverzeichnisse (Neutrale Anfrage 84 Seiten, REKERS-LV 54 Seiten, LGZ Dörpen LV 55 Seiten), Bauantragsplanung (BA-2000 Schnitte, BA-3000 Ansichten), Entwurfsplanung (E-2100, E-900) und Baugrundgutachten durchsucht. Keine Erwähnung von Kran, Laufkran, Kranbahn oder Kranlasten.
- **Hinweis:** Als Logistikzentrum mit Überladebrücken und Mezzanine-Lagergeschoss ist dieses Bauvorhaben typischerweise für Gabelstaplerbetrieb ausgelegt, nicht für Kranbetrieb. Die Stützenköpfe im LV werden ausschließlich mit „Gabelausbildung für Dachbinder" und „Konsolen für Mezzaninebalken" beschrieben — keine Kranbahnkonsolen. Konsistent mit dem Gebäudetyp Logistikzentrum.

### Dachlasten ❌
- **Wert:** Nicht gefunden
- **Quelle:** Alle verfügbaren Angebote (40593.14, 40593.16, 40593.30, 40593.31), alle drei Leistungsverzeichnisse (Neutrale Anfrage, REKERS-LV 23-23, LGZ Dörpen LV) und sämtliche Bauantrags-/Entwurfsplanungen durchsucht. Keine expliziten Werte für Dachlast (kN/m²), WLZ, SLZ oder PV-Lasten gefunden.
- **Hinweis:** Das REKERS-Hauptangebot Nr. 40593.10 vom 26.01.2024 (referenziert in allen Ergänzungsangeboten als „Auftrags-LV 40593.10 vom 26.01.2024") ist nicht als separate PDF-Datei in den Projektdateien vorhanden. Dieses Hauptangebot würde typischerweise die Vorbemerkungen mit Wind-/Schneelastzonen und Dachlastannahmen enthalten — analog zu den Angeboten 28273.x im Projekt 35764, wo die Dachlasten explizit in den Satteldachbinder-Positionen aufgelistet sind. Die verfügbaren LV-Dokumente (Neutrale Anfrage und REKERS-LV) beschreiben Bauteilpositionen ohne Lastannahmen.

### Baustoff ✅
- **Wert:** Spannbeton-Dachbinder (C50/60, XC1); Stahlbeton-Fertigteile (C45/55, C35/45); Betonstahl BSt 500S+M; Spannstahl St1660/1860; Köcherverguß C25/30; Sandwich-Fassadenelemente (C35/45 Tragschale + C30/37 Vorsatzschale + EPS 035)
- **Quelle:** [`23-23 LV Stb.-Fertigteile.pdf`](https://drive.google.com/file/d/1LgflBh8DxdqKYGRmMxrqeWLXpvNjIkfq/view) (REKERS-LV, 54 Seiten)
- **Textstelle:**
  - Pos. 1.01 Spannbeton-Satteldachbinder: „Betongüte C50/60; Expositionsklasse XC1", L=24,00 m, „Betonstahl und Spannstahl gesondert"
  - Pos. 1.02 Spannbeton-Paralleldachbinder: „Betongüte C50/60; Expositionsklasse XC1", L=24,00 m
  - Pos. 1.06 Spannbeton-Abfangträger: „Betongüte C50/60; Expositionsklasse XC1", L=16,00 m
  - Pos. 1.11 Stahlbeton-Traufstütze: „Betongüte C45/55; Expositionsklasse XC1", Köcherverguß C25/30
  - Pos. 1.04 Stahlbeton-Randriegel: „Betongüte C35/45; Expositionsklasse XC1"
  - Pos. 1.33 Stahlbeton-Brandwandplatten: „Betongüte C35/45; Expositionsklasse XC1", F90
  - Pos. 1.64 Betonstahl BSt 500S+M: 214,00 to
  - Pos. 1.65 Spannstahl St1660/1860: 27,00 to
  - Pos. 1.56 Sandwich-Sturzelemente: „Betongüte C35/45 bzw. C30/37 (Vorsatzschale)", „Aufbau: 16 cm Tragschale + 8 cm EPS 035 + 8 cm Vorsatzschale", Gesamtstärke d = 32 cm
- **Bestätigt durch:**
  - [`Angebot Nr. 40593.16`](https://drive.google.com/file/d/1M4WBcFkPDlbVg2kH-tFpXrQYO6fpIn_M/view) (Mezzanine-Veränderungen): Pos. 1.1.6 „Betonstahl BSt 500S+M" (13 to), Pos. 1.1.7 „Spannstahl St1660/1860" (4 to), Pos. 1.1.2 Spannbeton-TT-Deckenplatte „Betongüte C45/55; Expositionsklasse XC1, W0"
  - [`LGZ Dörpen LV Stand 21.12.2023.pdf`](https://drive.google.com/file/d/1qc9ppi7alwHPJhwddtWJHJ1QtFFpqIVQ/view) (55 Seiten): Identische Positionsbeschreibungen und Betongüten bestätigt.
- **Hinweis:** Die Betongüte wird positionsabhängig differenziert: C50/60 für alle Spannbetonbauteile (Dachbinder, Abfangträger, Halbmontagebalken), C45/55 für Stützen und TT-Deckenplatten, C35/45 für Wandplatten/Riegel und Sandwich-Tragschalen, C30/37 für Sandwich-Vorsatzschalen, C25/30 für Köcherverguß. Die Expositionsklasse XC4/XF1 wird nur für Brandwandplatten mit Außenkontakt verwendet (Pos. 1.36). Die Spannstahl-Menge von 27 to für Unit 1 allein (bei 4 Units) deutet auf ca. 100+ to Spannstahl für das Gesamtprojekt — konsistent mit der Gebäudegröße.

### Dachbegrünung ❌
- **Wert:** Nicht gefunden
- **Quelle:** Alle Angebote, Leistungsverzeichnisse, Bauantrags- und Entwurfsplanung durchsucht. Keine Erwähnung von Dachbegrünung, Gründach, extensiver/intensiver Begrünung oder zusätzlichen Dachlasten aus Vegetationsschichten.
- **Hinweis:** Der Dachaufbau basiert auf Dachtrapezblechen (Pos. 1.05 Trapezblechschiene HTU-S 60/25/2,5 für alle Dachbinder, 1.296 lfdm). In der Schnittdarstellung sind ausschließlich RWA-Kuppeln auf dem Dach sichtbar — kein Hinweis auf Vegetationsschichten. Konsistent mit dem Gebäudetyp Logistikzentrum.

---

## Beobachtungen

Dieses Projekt hat eine außergewöhnlich umfangreiche Dokumentation durch zahlreiche Angebotsversionen und Nachträge. Die Angebotshistorie umfasst ein Hauptangebot (40593.10, 26.01.2024), gefolgt von mehreren Ergänzungsangeboten: 40593.12 (Fundamente), 40593.13 (27.03.2024), 40593.14 (Brandwandtore, 09.04.2024), 40593.16 (Mezzanine-Veränderungen, 18.04.2024), 40593.17a, 40593.18, 40593.19 (alle 22.08.2024), 40593.20, 40593.30 (Sockelschaden, 22.11.2024) und 40593.31 (Toröffnung, 03.11.2025). Diese Entwicklung über fast zwei Jahre zeigt ein aktives Bauprojekt mit fortlaufenden Anpassungen.

Bemerkenswert ist die Ortsbezeichnung: Die Anfragen.csv und alle REKERS-Angebote referenzieren „49779 Niederlangen", während die Leistungsverzeichnisse (Neutrale Anfrage und LGZ Dörpen LV) „26892 Dörpen" im Titel tragen. Tatsächlich liegt das Baugrundstück laut Baugrundgutachten an der „A 31 in Niederlangen" (Oberlangener Weg 2). Die Diskrepanz erklärt sich durch die Nähe beider Orte — Dörpen und Niederlangen grenzen aneinander im Emsland.

Die Brandwandplatten OK-Höhe von +14,40 m (Pos. 1.34, Achse 4/B–I.1) übersteigt die Attikahöhe (+14,00 m) um 40 cm — dies ist brandschutztechnisch erforderlich für die F90-Abschottung zwischen den Halleneinheiten.

Das Fehlen der Dachlastwerte ist ungewöhnlich und liegt am nicht verfügbaren Hauptangebot 40593.10. Alle Ergänzungsangebote verweisen auf dessen Vorbemerkungen, liefern aber keine eigenständigen Lastannahmen.

---

## Quelle

- **Datenverzeichnis:** `~/Documents/projects/billable/REKERS__poc/data/2026-01-28_testdaten/40593/`
- **Design-Doc:** [Feasibility Report, Teil 2](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/design-doc-feasibility-report)
- **Session:** 4f6e2de6-1482-49fd-b1e0-f660e55dfd9b

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

