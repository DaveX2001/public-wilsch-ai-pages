---
publish: true
---

# Kriteriennachweis — Projekt 41740: VGP Park Bernau Halle B
[[client-rekers]]

Nachweis für Design-Doc Teil 2. Datenverfügbarkeit pro Kriterium mit Quelldateien und extrahierten Werten.

Projekt: 41740 | Datum: 2024-12-18 | Wert: €2.342K

---

## Untersuchte Daten

| Ebene | Anzahl | Details |
|-------|--------|---------|
| L0: Ordnerdateien | 24 | 24 EML (1 Wurzel + 23 in 18213/) |
| L2: EML-Anhänge | ~152 | ~60 PDF, ~20 PNG/JPG, ~6 XLSX, 2 DWG, 1 ZIP, diverse |
| _extracted (ZIP) | 16 | DOC, DOCX, XLSX (Revisionsunterlagen) |

Multiplikator: 8,0x (L0 vs. L0+L2+ZIP)

---

## Übersicht

| Kriterium | Status | Wert | Quellebene |
|-----------|--------|------|------------|
| Bauort | ✅ CSV | Bernau, 16321, D | L1 |
| Kundenreferenz | ✅ CSV | 103166 | L1 |
| Kalkulatorenwissen | ✅ CSV | 1014 | L1 |
| Gebäudetyp | ✅ | Logistikhalle mit Überladegruben + 2-gesch. Büroeinbau (optional) | L2 |
| Höhe | ✅ | OK Attika +14,00 m; OK First ca. +13,50 m; UK Binder ca. +12,00 m | L2 |
| Kran | ✅ | Ja — Kranbahnbrücke 2×10 to Laufkatze je Hallenschiff | L2 |
| Dachlasten | ✅ | PV 0,15; Dachbegrünung 1,00; Trapez+PIR 0,35; Installation 0,20 kN/m²; WZ2; SLZ2 | L2 |
| Baustoff | ✅ | Spannbeton-Satteldachbinder/-Abfangeträger; Stb.-FT; B500; ST1660/1860 | L2 |
| Dachbegrünung | ✅ | Ja — 1,00 kN/m² Eigengewichtslast (DGNB) | L2 |

**9/9 nachgewiesen** (3 strukturiert + 6 extrahiert). 0 nicht vorhanden.

---

## Strukturierte Kriterien (Anfragen.csv)

### Bauort
- **Wert:** Bernau, 16321, D
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
- **Wert:** Logistikhalle mit Überladegruben und optionalem 2-geschossigem Büroeinbau
- **Quelle:** [`18213/_extracted/Angebot.pdf`](https://drive.google.com/file/d/1gZnN5Wbz-AeO9cv9T_6M9g5RWQPotbRv/view) (Angebot Nr. 41740.1, 24 Seiten)
- **Textstelle:** Bauvorhaben: „VGP Park Bernau, Halle B; Potsdamer Str., 16321 Bernau". Vorbemerkungen: „Die 2-geschossigen Büroeinbauten sind optionale Einbauten. Die hierfür angebotenen Leistungen (gemäß Titel 3 bis Titel 6) kommen vorläufig nicht zur Ausführung, die angebotene Leistung ist im Angebots-Gesamtpreis nicht enthalten." Pos. 1.33: FT-Grubenwand für „Rück- u. Seitenwände der Überladegruben". Hallensohlen-Nutzlast 70,0 kN/m², Staplerklasse FL3 (6,9 to).
- **Bestätigt durch:** [`18213/_extracted/Rekers-Plan R1_1.pdf`](https://drive.google.com/file/d/19yZgR7Hd4o8V4AK_av0_Rh0fPf-_3VFz/view) (Grundriss EG, M 1:200): Zeigt Hallenstruktur mit Überladegruben entlang einer Längsseite, Büroeinbau im Seitenbereich, Achsraster A–M / 1–9.
- **Hinweis:** VGP-typische Logistikhalle. Im Gegensatz zu anderen VGP-Projekten verfügt Halle B über eine bauseitige Kranbahnanlage in 2 Hallenschiffen. Mezzanine kommt nicht zur Ausführung.

### Höhe ✅
- **Wert:** OK Attika +14,00 m; OK Satteldachbinder im First ca. +13,50 m; UK Satteldachbinder ca. +12,00 m; UK Abfangeträger ca. +11,63 m; OK Stütze ca. +13,10 m
- **Quelle:** [`18213/_extracted/Angebot.pdf`](https://drive.google.com/file/d/1gZnN5Wbz-AeO9cv9T_6M9g5RWQPotbRv/view), Seite 3 (Konstruktionshöhen)
- **Textstelle:** „Konstruktionshöhen: OK Attika: +14,00 m; OK Satteldachbinder im First: ca. +13,50 m; UK Satteldachbinder: ca. +12,00 m; UK Abfangeträger: ca. +11,63 m. Die Binder liegen in seitlichen Auflagertaschen der Abfangeträger bzw. auf der Stütze. OK Hallensohle: +0,00 m."
- **Bestätigt durch:** Pos. 1.10 Stb.-FT-Innenstütze: „Länge ca. 14,55 m, bei OK-Stütze ca. +13,10 m". Pos. 1.30 FT-Brandwandplatten: „OK: ca. +14,05 m". Vorbemerkungen: „Die lichte Hallenhöhe bis UK Abfangeträger liegt gemäß Planung bei +11,63 m."
- **Hinweis:** Satteldachbinder mit 3 % Obergurtneigung (analog zu Halle A im VGP Park Bernau, abweichend von Konzeptplanung). Firsthöhe h ca. 1,45 m bei Spannweite 24,00 m.

### Kran ✅
- **Wert:** Ja — Kranbahnbrücke mit 2 Laufkatzen je Hallenschiff in Achse B-C/1-9 und C-D/1-9, Hublast 10,00 to je Laufkatze. Kranbahnstützen im Achsabstand e = 8,00 m.
- **Quelle:** [`18213/_extracted/Angebot.pdf`](https://drive.google.com/file/d/1gZnN5Wbz-AeO9cv9T_6M9g5RWQPotbRv/view), Seite 1 (Vorbemerkungen zum überarbeiteten Angebot)
- **Textstelle:** „Im Hallenbereich Achse B-C/1-9 und C-D/1-9 kommt je Hallenschiff eine bauseitige Kranbahnbrücke mit 2 Laufkatzen zur Ausführung. Die Hublast beträgt 10,00 to je Laufkatze. (Krandatenblätter liegen bislang noch nicht vor). In Achse B,C,D/1-9 werden die Kranbahnstützen im Achsabstand e = 8,00 m angeordnet. Die Kranbahnstützen werden bis zur Dachebene hochgeführt und über die Dachbinder gekoppelt."
- **Bestätigt durch:** Selbe Seite: „Die Horizontallasten aus der Kranbahn werden über die Kranbahnträger gleichmäßig auf die Kranbahnstützen verteilt. Die ursprünglich angebotenen Abfangeträger (Achse B,C,D/1-9) für die Auflagerung der Dachbinder kommen nicht zur Ausführung. (vgl. auch Rekers Plan R1a, Planausschnitt geänderter Bereich mit Krananlage)"
- **Hinweis:** Krananlage ist bauseitig (nicht von REKERS geliefert), aber REKERS liefert die Kranbahnstützen und passt die Tragstruktur an. Krandatenblätter lagen zum Angebotszeitpunkt noch nicht vor. Im Vergleich zu anderen VGP-Logistikhallen (41634, 40856, 40593 — ohne Kran) ist dieses Projekt ungewöhnlich.

### Dachlasten ✅
- **Wert:**
  - Photovoltaikanlage: 0,15 kN/m² (Aufbauhöhe h < 0,34 m)
  - Dachbegrünung: 1,00 kN/m²
  - Trapezblech 100 mm PIR-Dämmung, Abdichtung: 0,35 kN/m²
  - Installation: 0,20 kN/m²
  - Wind: Windlastzone WZ 2, Binnenland
  - Schnee: Schneelastzone 2, Norddeutsches Tiefland, Geländehöhe 74,68 m ü. NHN
- **Quelle:** [`18213/_extracted/Angebot.pdf`](https://drive.google.com/file/d/1gZnN5Wbz-AeO9cv9T_6M9g5RWQPotbRv/view), Seite 2 (Annahmen/Festlegungen, Dachbelastung Hallendach)
- **Textstelle:** „Dachbelastung Hallendach: Photovoltaikanlage = 0,15 kN/m² (Aufbauhöhe h < 0,34 m); Dachbegrünung = 1,00 kN/m²; Trapezblech, 100 mm PIR-Dämmung, Abdichtung = 0,35 kN/m²; Installation = 0,20 kN/m²." Sowie: „Wind- u. Schneelasten gemäß DIN EN 1991: Windlastzone WZ 2, Binnenland; Schneelastzone 2, Norddeutsches Tiefland, Geländehöhe = 74,68 m ü. NHN (= Hallensohle +-0,00 m)."
- **Bestätigt durch:** Vorbemerkungen Seite 1: „Hinsichtlich der Einwirkungen haben wir die gleichen Lastannahmen wie bei der Halle A im VGP Park Bernau angenommen." Seite 2: „Dachtrapezbleche als 3- oder 4-Feldsystem, Durchlauffaktor = 1,10."
- **Hinweis:** Lastannahmen identisch mit Halle A im VGP Park Bernau. Erdbeben nach DIN EN 1998-1: Sap,R = 0,0216 m/s² (außerhalb Erdbebenzone nach DIN 4149-2005).

### Baustoff ✅
- **Wert:** Spannbeton-Satteldachbinder (l = 24,00 m, Firsthöhe h ca. 1,45 m); Spannbeton-Abfangeträger (l = 16,00 m, Trägerhöhe ca. 1,45 m); Stb.-Fertigteile; Betonstahl B500; Spannstahl ST1660/1860 nach DIN 488; Beton nach DIN EN 206-1/DIN 1045-2, Portlandzement
- **Quelle:** [`18213/_extracted/Angebot.pdf`](https://drive.google.com/file/d/1gZnN5Wbz-AeO9cv9T_6M9g5RWQPotbRv/view), Seiten 3–4 (Beton, Bewehrung) und Seite 6 (LV Pos. 1.01, 1.02)
- **Textstelle:**
  - Beton (S. 3–4): „Die Ausführung der Betonarbeiten erfolgt nach DIN EN 206-1/DIN 1045-2 unter Verwendung von Portlandzement. Druckfestigkeitsklasse/Betongüte gemäß statischer Erfordernis."
  - Bewehrung (S. 4): „Betonstahl B500, Spannstahl ST1660/1860 nach DIN 488."
  - Pos. 1.01 (S. 6): „Spannbeton-Satteldachbinder, Systemachslänge l = 24,00 m, Firsthöhe h ca. 1,45 m, mit 3 % Obergurtneigung" — 84 Stück à 5.797,70 € = 487.006,80 €
  - Pos. 1.02 (S. 6): „Spannbeton-Abfangeträger für vorgen. Binder, Systemachslänge l = 16,00 m, Trägerhöhe ca. 1,45 m" — 40 Stück à 6.162,70 € = 246.508,00 €
- **Bestätigt durch:** Pos. 1.10–1.21: Diverse Stb.-FT-Stützentypen (Innenstütze, Traufstütze, Giebelstütze, Eckstütze, Brandwandstütze) mit Längen ca. 14,55 m. Bemessung nach DIN EN 1992-1-1 + NA.
- **Hinweis:** Betongüte als „gemäß statischer Erfordernis" angegeben, nicht als konkrete Klasse wie C50/60. Der Spannstahl ST1660/1860 bestätigt, dass Dachbinder und Abfangeträger aus Spannbeton gefertigt sind.

### Dachbegrünung ✅
- **Wert:** Ja — 1,00 kN/m² Eigengewichtslast (maximale Last, Wassergesättigt). DGNB-Zertifizierung angestrebt.
- **Quelle:** [`18213/_extracted/Angebot.pdf`](https://drive.google.com/file/d/1gZnN5Wbz-AeO9cv9T_6M9g5RWQPotbRv/view), Seiten 1–2
- **Textstelle:** Seite 1: „Für die Dachbegrünung ist dementsprechend eine Eigengewichtslast von g = 1,00 kN/qm (maximale Last, Wassergesättigt, etc.) berücksichtigt und die Aufbauhöhe der PV-Anlage haben wir ebenfalls wie bei der Halle A mit h <= 0,34 m angenommen." Seite 2: „Dachbelastung Hallendach: Dachbegrünung = 1,00 kN/m²."
- **Bestätigt durch:** Seite 1: „Im Hinblick auf eine angestrebte DGNB-Zertifizierung wurden von uns die gleichen Vertraglichen Regelungen wie bei der Halle A im VGP Park Bernau zugrunde gelegt."
- **Hinweis:** Dachbegrünung mit 1,00 kN/m² ist ein signifikanter Lastanteil (vs. PV 0,15 + Trapez+PIR 0,35 + Installation 0,20 = 0,70 kN/m² restliche Dachlasten). Spezifikation identisch mit Halle A im selben VGP Park. DGNB-Zertifizierung motiviert die Dachbegrünung.

---

## Beobachtungen

Dieses Projekt ist eines der wenigen VGP-Logistikprojekte mit einer Kranbahnanlage. Während andere VGP-Hallen (41634, 40856, 40593, 40758) reine Logistik-/Gabelstaplerbetriebe ohne Kran sind, verfügt Halle B in Bernau über 2 Kranbahnbrücken mit je 2 Laufkatzen à 10,00 to in den Hallenschiffen B-C und C-D. Dies führt zu einer aufwändigeren Tragstruktur mit separaten Kranbahnstützen im 8,00 m-Achsabstand und Wegfall der ursprünglich angebotenen Abfangeträger im Kranbereich.

Das Angebot 41740.1 ist eine Überarbeitung des Angebots 41740.0 vom 15.01.2025, basierend auf Besprechung vom 24.01.2025. Die Lastannahmen und DGNB-Regelungen sind identisch mit Halle A im VGP Park Bernau — eine explizite Referenz, die auf ein größeres VGP-Ensemble mit einheitlichen Standards hindeutet.

Alle 9 Kriterien sind nachgewiesen — eines der wenigen Projekte mit vollständiger Abdeckung. Dies liegt an der umfassenden Vorbemerkung des Angebots und der expliziten Dachbegrünungslast für die DGNB-Zertifizierung. Der 2-geschossige Büroeinbau ist als optionale Leistung angeboten aber nicht im Gesamtpreis enthalten (kommt vorläufig nicht zur Ausführung).

---

## Quelle

- **Datenverzeichnis:** `~/Documents/projects/billable/REKERS__poc/data/2026-01-28_testdaten/41740/`
- **Design-Doc:** [Feasibility Report, Teil 2](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/design-doc-feasibility-report)
- **Session:** d75f5d11-26e8-4ca4-a856-6dfafc97d900

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

