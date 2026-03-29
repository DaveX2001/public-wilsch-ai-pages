---
publish: true
---

# Kriteriennachweis — Projekt 41793: Lager- und Logistikhalle Roy Böhlke Stendal
[[client-rekers]]

Nachweis für Design-Doc Teil 2. Datenverfügbarkeit pro Kriterium mit Quelldateien und extrahierten Werten.

Projekt: 41793 | Datum: 2025-01-14 | Wert: €2.105K

---

## Untersuchte Daten

| Ebene | Anzahl | Details |
|-------|--------|---------|
| L0: Ordnerdateien | 27 | 20 EML, 6 PDF, 1 ZIP |
| L2: EML-Anhänge | 75 | 40 PDF, 4 ZIP, 15 JPG/BMP, 16 Duplikate |
| _extracted (ZIP) | 15 | 15 PDF (Architekturpläne, LB, MBB, BSK, Bodengutachten) |

Multiplikator: 4,3x (L0 vs. L0+L2+ZIP)

---

## Übersicht

| Kriterium | Status | Wert | Quellebene |
|-----------|--------|------|------------|
| Bauort | ✅ CSV | Arneburg, 39596, D | L1 |
| Kundenreferenz | ✅ CSV | 335 | L1 |
| Kalkulatorenwissen | ✅ CSV | 1051 | L1 |
| Gebäudetyp | ✅ | Lager- und Logistikhalle mit 2-gesch. Büroeinbau + Technikanbau | L2 |
| Höhe | ✅ | OK Attika +11,20 m; UK Dachkonstruktion +9,05 m (TP); lichte Höhe 9,00 m | L1/L2 |
| Kran | ✅ | Ja — Koppelbare Zweiträgerlaufkrananlage 2×20 t (Abus ZLK), Spannweite 25 m | L1/L2 |
| Dachlasten | ✅ | PV 0,20; Abhang 0,15; Ständig 0,22 kN/m²; WLZ2; SLZ2 Sonderfall Nordd. Tiefebene | L1/L2 |
| Baustoff | ✅ | Spannbeton-Parallelgurtbinder; Stb.-FT; B500(A); ST1660/1860 | L2 |
| Dachbegrünung | ❌ | Explizit ausgeschlossen | L1 |

**8/9 nachgewiesen** (3 strukturiert + 5 extrahiert). 1 explizit ausgeschlossen.

---

## Strukturierte Kriterien (Anfragen.csv)

### Bauort
- **Wert:** Arneburg, 39596, D
- **Quelle:** Anfragen.csv, Zeile → `BAUVORHABEN_ORT`, `PLZ`, `NATION`

### Kundenreferenz
- **Wert:** 335
- **Quelle:** Anfragen.csv, Zeile → `KUNDE`

### Kalkulatorenwissen
- **Wert:** 1051
- **Quelle:** Anfragen.csv, Zeile → `KALKULATOR`

---

## Extrahierte Kriterien (Projektdateien)

### Gebäudetyp ✅
- **Wert:** Lager- und Logistikhalle mit 2-geschossigem Büroeinbau (ca. 103 m²) und Technikanbau (ca. 35 m²); 3 Brandabschnitte (Blocklager 3.023 m², Handlingfläche/PSA 1.610 m², Palettenlager 4.303 m²); Brutto-Grundfläche ca. 9.230 m² (75,00 × 120,12 m)
- **Quelle:** [`24-0799-1-66_Böhlke_Stendal_ Leistungsbeschreibung.pdf`](https://drive.google.com/file/d/1ITUATtIaL1vFRkRXfaUy64gSdG5TdHkX/view) (Köster GmbH, Leistungsbeschreibung 54221)
- **Textstelle:** Tabelle „Technische Daten - Halle": „Länge x Breite (Außenkante Fassade): 75,00 m x 120,12 m", „Brutto-Grundfläche (Außenmaß): ca. 9.230 m²", „Anzahl der Units / Anzahl Brandabschnitte: 3 Stück". Tabelle „Büroeinbau (2-geschossig)": „Länge x Breite: ca. 16,535 m x 6,25 m", „Brutto-Grundfläche: ca. 103,00 m²". Tabelle „Technikanbau": „Länge x Breite: ca. 8,335 m x 4,00 m", „Brutto-Grundfläche: ca. 35,00 m²".
- **Bestätigt durch:** [`geprüft-Angebot Nr. 41793.0.pdf`](https://drive.google.com/file/d/10H7i3_tQljL4FnQDLpX1ZZedfUM-KZHg/view) — Angebotskopf: „Lager- und Logistikhalle Roy Böhlke Stendal". Lageplan-Auszug zeigt: „NEUBAU Gesamt ca. 9.230 m²", „lichte Hallenhöhe ca. 9,00 m", sowie separate Zonen Handlingflächen/PSA (ca. 1.800 m²), Palettenlager, Blocklager (ca. 3.100 m²). [`Grundrisse EG, OG`](https://drive.google.com/file/d/1idNCrZbyEUWX3gxgBgIJqAbel4spGWB8/view) (Architekt Bals, Bauantrag) zeigt detaillierte Raumaufteilung mit Büro (55,34 m² + 16,64 m²), Sozialräume, Technik (HAR, NSHV, SiBe, BMZ).
- **Hinweis:** Mieter ist Mercer Stendal GmbH (Neubau Ersatzteillager). Bauherr: Grundstücksverwaltungsgesellschaft BZM GmbH, Stendal. GU: Köster GmbH, Hamburg. Gabelstaplerklasse FL3 (7,00 t Gesamtgewicht), Regallast bis 80 kN/Regalstiel.

### Höhe ✅
- **Wert:** OK Attika +11,20 m (= +48,90 m üNHN); UK Dachkonstruktion +9,05 m (Tiefpunkt); OK Dachkonstruktion +10,75 m (Hochpunkt); OK Trapezblech +10,95 m (Hochpunkt); OK Stütze +10,50 m; lichte Höhe (installationsfreier Raum) 9,00 m; Dachneigung ca. 2,00 % (Parallelgurtbinder)
- **Quelle:** [`24-0799-1-66_Böhlke_Stendal_ Leistungsbeschreibung.pdf`](https://drive.google.com/file/d/1ITUATtIaL1vFRkRXfaUy64gSdG5TdHkX/view)
- **Textstelle:** Tabelle „Technische Daten - Halle": „Lichte Höhe (installationsfreier Raum): 9,00 m über OKFF Hallensohle", „Unterkante Dachkonstruktion: 9,05 m über OKFF Hallensohle (im Tiefpunkt)", „Oberkante Dachkonstruktion: 10,75 m über OKFF Hallensohle (im Hochpunkt)", „Oberkante Trapezblech: 10,95 über OKFF Hallensohle (im Hochpunkt)", „Attikahöhe: ca. 11,20 über OKFF Hallensohle", „Dachform/Dachneigung: Parallelgurtbinder, ca. 2,00 %".
- **Bestätigt durch:** [`Schnitte g., 11.02.25.pdf`](https://drive.google.com/file/d/1b42kWvqNWip4XG3_A0yXNUPVoiaA3r6C/view) (Schnitt A-A, B-B, C-C M 1:100): „OK Attika +11,20 = +48,90 üNHN", „+/-0,00 OKF = +37,70 m üNHN", Kran-Symbol in Schnitt A-A sichtbar. [`Ansichten g., 11.02.25.pdf`](https://drive.google.com/file/d/1-Ne27w1lLnpQe7bRzzrNneEWeYfT_dfQ/view): alle vier Ansichten bestätigen „+11,20 = +48,90 üNHN" und „~+6,05 = ~+43,75 üNHN" (Betonsockel/Lichtband). Angebot 41793.0 Pos. 2.01/2.08/2.14: „OK-Stütze i.M. = +10,50 m".
- **Hinweis:** Oberfläche Fertigfußboden bei ca. 37,70 m üNHN. Büroeinbau lichte Deckenhöhe 3,00 m (max. Raumhöhe 2,75 m). Technikanbau OK Attika +3,85 m = +41,55 m üNHN.

### Kran ✅
- **Wert:** Ja. Koppelbare Zweiträgerlaufkrananlage mit je 20 t (z.B. Abus ZLK), Spannweite ca. 25 m; Stützen in Achsen A und B ausgelegt; 17 Kranbahnstützen + 17 Kranbahnkonsolen im REKERS-Angebot; Kranbahnbalken und Krananlagen bauseitig
- **Quelle:** [`Mieterbaubeschreibung Index 02 Stand 12.02.25.pdf`](https://drive.google.com/file/d/1HJIpqiGf1h_ih3AY2cYCFoLbIEebhlBY/view) (Mercer Stendal GmbH), Abschnitt 8.11
- **Textstelle:** „Die Stützen der Achsen A und B werden für eine koppelbare Zweiträgerlaufkrananlage mit je 20 t (z.B. Abus ZLK), Spannweite ca. 25 m, ausgelegt. Der Kranbahnbalken und die Krananlagen sind im Angebot nicht berücksichtigt."
- **Bestätigt durch:**
  - [`geprüft-Angebot Nr. 41793.0.pdf`](https://drive.google.com/file/d/10H7i3_tQljL4FnQDLpX1ZZedfUM-KZHg/view): LV-Positionen für Kranbahnstützen und -konsolen: Pos. 2.06 (2 Stk Kranbahnstütze), 2.07 (2 Stk Konsolen), 2.12 (13 Stk Kranbahnstütze), 2.13 (13 Stk Konsolen), 2.18 (2 Stk Kranbahnstütze), 2.19 (2 Stk Konsolen) = 17 Stützen + 17 Konsolen gesamt.
  - [`Schnitte g., 11.02.25.pdf`](https://drive.google.com/file/d/1b42kWvqNWip4XG3_A0yXNUPVoiaA3r6C/view): Schnitt A-A zeigt „Kran" als Beschriftung im Bereich Blocklager zwischen Achsen A und B.
  - Angebot 41793.0 Vorbemerkungen: „die spätere Erweiterung erfolgt mit einem 25,00 m Binder [...]; die Erweiterung ist ohne optionaler Kranbahn angeboten".
- **Hinweis:** Die Kraninfrastruktur ist vollständig in den REKERS-Fertigteilen berücksichtigt — Stützen als Kranbahnstützen ausgebildet mit angeformten Konsolen. Lediglich die Kranbahnbalken (Schienen) und die Krananlagen selbst sind bauseitig (nicht im REKERS-Angebot). Im Erweiterungsbereich (Achse A) ist die Kranbahn nicht vorgesehen. Kosten für Kranbahnstützen-Zulagen: 17 × ~€1.050 + 17 × €185 = ca. €21.000 netto.

### Dachlasten ✅
- **Wert:**
  - Ständige Lasten: 0,22 kN/m² (Dachabdichtung 0,02 + Dämmung 0,04 + Dampfsperre 0,03 + Trapezblech 0,13)
  - Veränderliche Lasten: 0,35 kN/m² (PV 0,20 + Abhanglasten 0,15)
  - Wind: Windlastzone 2 (WLZ2)
  - Schnee: Schneelastzone 2, Sonderfall Norddeutsche Tiefebene
  - Geländekategorie: Binnenland
  - Trapezblech: Koppellast 1,50 kN/m, Durchlauffaktor = 1,10
  - Erdbebenzone: keine
- **Quelle:** [`24-0799-1-66_Böhlke_Stendal_ Leistungsbeschreibung.pdf`](https://drive.google.com/file/d/1ITUATtIaL1vFRkRXfaUy64gSdG5TdHkX/view), Seiten 3–4
- **Textstelle:** Standort-Tabelle: „Windlastzone: 2", „Schneelastzone: 2 Sonderfall Norddeutsche Tiefebene", „Geländekategorie: Binnenland", „Erdbebenzone: keine". Lasttabelle „Lasten Dachtragwerk": „Ständige Lasten: 0,22 kN/m2" (Dachabdichtung 0,02, Dämmung 0,04, Dampfsperre 0,03, Trapezblech 0,13), „Veränderliche Lasten: 0,35 kN/m²" (PV-Anlage 0,20, Abhanglasten 0,15).
- **Bestätigt durch:** [`geprüft-Angebot Nr. 41793.0.pdf`](https://drive.google.com/file/d/10H7i3_tQljL4FnQDLpX1ZZedfUM-KZHg/view) Vorbemerkungen (Seite 4): „Die Trapezbleche sind für eine Koppellast von 1,50 kN/m auszulegen", „Der Nachweis der Dachbinder erfolgt mit einheitlichem Durchlauffaktor = 1,10". Schnittzeichnung: „PV-Anlage vorrüsten" als Beschriftung auf dem Dach.
- **Hinweis:** Arneburg liegt in der Norddeutschen Tiefebene — der „Sonderfall" für SLZ2 führt zu erhöhten Schneelastannahmen gegenüber normalem SLZ2. PV-Aufbauhöhe ≤ 35 cm laut LB. Fassadenlasten: 0,20 kN/m² (Isopaneele bis d=80 mm). Hallensohle: 50,00 kN/m² Flächenlast, Regalsystem bis 80 kN/Regalstiel.

### Baustoff ✅
- **Wert:** Spannbeton-Parallelgurtbinder (l=25,00 m, h=1,20 m); Spannbeton-Abfangträger (l=17,16 m, h=1,20 m); Stahlbeton-Fertigteile; Betonstahl B500(A); Spannstahl ST1660/1860 nach DIN 488; Beton nach DIN EN 206-1/DIN 1045-2 mit Portlandzement; Druckfestigkeitsklasse gemäß statischer Erfordernis
- **Quelle:** [`geprüft-Angebot Nr. 41793.0.pdf`](https://drive.google.com/file/d/10H7i3_tQljL4FnQDLpX1ZZedfUM-KZHg/view), Vorbemerkungen Seiten 4–5 und LV Seite 5
- **Textstelle:**
  - Beton (Seite 4): „Die Ausführung der Betonarbeiten erfolgt nach DIN EN 206-1/DIN 1045-2 unter Verwendung von Portlandzement. Druckfestigkeitsklasse/Betongüte gemäß statischer Erfordernis."
  - Bewehrung (Seite 5): „Betonstahl B500(A), Spannstahl ST1660/1860 nach DIN 488."
  - LV Pos. 1.1: „Parallelbinder L = 25,00 m ‚Achslänge', h = 1,20 m" — 37 Stück à 4.940,30 €
  - LV Pos. 1.3: „Spannbeton-Abfangträger, l ca. 17,16 m (Achsmaß), h = 1,20 m" — 7 Stück à 5.606,50 €
  - Expositionsklassen: XC1 (Innenbauteile), XC2 (Gründung), XC4 (Außenbauteile)
- **Bestätigt durch:** Angebotskopf: „Herstellung, Lieferung und Montage von Stb.-Fertigteilen für das Bauvorhaben: Lager- und Logistikhalle Roy Böhlke in Stendal". LV Pos. 1.4: Giebelrandriegel l=8,35 m, h=0,50 m (18 Stk). Vorbemerkungen: Sandwichsockel mit XPS WLG 035, Sandwichfassaden mit EPS WLG 0,35.
- **Hinweis:** Dachkonstruktion gesamt €254.063,90 netto. Betongüte nicht als konkrete Klasse angegeben (z.B. C50/60), sondern „gemäß statischer Erfordernis" — typisch für REKERS-Angebote, da Querschnitte nach System Rekers dimensioniert werden. Parallelgurtbinder (nicht Satteldachbinder) mit 2% Neigung.

### Dachbegrünung ❌
- **Wert:** Explizit ausgeschlossen
- **Quelle:** [`Mieterbaubeschreibung Index 02 Stand 12.02.25.pdf`](https://drive.google.com/file/d/1HJIpqiGf1h_ih3AY2cYCFoLbIEebhlBY/view), Abschnitt 6.5 Hallendach (Seite 30)
- **Textstelle:** „Eine extensive Dachbegrünung ist nicht berücksichtigt."
- **Bestätigt durch:** Die Lasttabelle in der Leistungsbeschreibung enthält keine Gründach-Belastungsposition — nur PV (0,20 kN/m²) und Abhanglasten (0,15 kN/m²) als veränderliche Lasten. Dachaufbau laut Schnittzeichnung und MBB: „Abdichtung, Dämmung gem. BSK, PIR/PUR D=8 cm, WLG gem. WSN, Dampfbremse/-sperre, Trapezblech" — standardmäßiges Industrieflachdach ohne Vegetationsschicht.
- **Hinweis:** Explizite Ausschlussformulierung in der Mieterbaubeschreibung — keine Interpretationsunsicherheit. Das Projekt verwendet stattdessen PV-Vorbereitung auf dem Dach (Aufbauhöhe ≤ 35 cm, 0,20 kN/m²).

---

## Beobachtungen

Dieses Projekt zeichnet sich durch eine ungewöhnlich vollständige Dokumentation aus — neben dem REKERS-Angebot (41793.0) liegt eine detaillierte Leistungsbeschreibung der Köster GmbH (GU) vor, die alle technischen Daten in Tabellenform strukturiert (Halle, Büroeinbau, Technikanbau, Standort, Lasten). Zusätzlich existiert eine 48-seitige Mieterbaubeschreibung (Mercer Stendal GmbH), die bis zur Krananlage und Gebäudetechnik alle Gewerke spezifiziert.

Die Kraninfrastruktur ist bemerkenswert umfangreich: 17 Kranbahnstützen und 17 Kranbahnkonsolen sind im REKERS-LV enthalten (Pos. 2.06–2.19), verteilt über Giebelstützen, Traufstützen und Eckstützen. Die Mieterbaubeschreibung spezifiziert eine koppelbare Zweiträgerlaufkrananlage mit je 20 t — eines der wenigen Projekte in der Testreihe mit explizit dokumentierter Kranlast. Im Erweiterungsbereich (Achse A, 25-m-Binder) ist die Kranbahn jedoch nicht vorgesehen.

Die Projektstruktur zeigt einen klaren Vergabeweg: Anfrage → geprüftes Angebot → VHP (Vergabehandlungsprotokoll) → Auftragserteilung → Nachkalkulation → Zusatzleistungen. Der Vergabetermin laut LB: 14.02.2025, Montagebeginn ca. Juni 2025. Die Frachtanfrage (254477) für den Erstteiltransport liegt ebenfalls vor.

Dachbegrünung ist explizit ausgeschlossen (MBB S. 30), was die „❌ explizit ausgeschlossen"-Klassifikation eindeutig stützt — kein Interpretationsspielraum.

---

## Quelle

- **Datenverzeichnis:** `~/Documents/projects/billable/REKERS__poc/data/2026-01-28_testdaten/41793/`
- **Design-Doc:** [Feasibility Report, Teil 2](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/design-doc-feasibility-report)
- **Session:** bb6dbff1-f23e-4941-ad11-4066d7154d40

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

