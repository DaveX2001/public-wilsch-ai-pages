---
publish: true
---

# Kriteriennachweis — Projekt 37369: Kühl- und Lagerhalle Schütte
[[client-rekers]]

Nachweis für Design-Doc Teil 2. Datenverfügbarkeit pro Kriterium mit Quelldateien und extrahierten Werten.

Projekt: 37369 | Datum: 2020-12-02 | Wert: €1.980K

---

## Untersuchte Daten

| Ebene | Anzahl | Details |
|-------|--------|---------|
| L0: Ordnerdateien | 19 | 19 EML |
| L2: EML-Anhänge | 74 | 48 PDF, 22 PNG, 2 JPG, 2 GIF |

Multiplikator: 4,9x (L0 vs. L0+L2)

---

## Übersicht

| Kriterium | Status | Wert | Quellebene |
|-----------|--------|------|------------|
| Bauort | ✅ CSV | Wietmarschen Lohne, D | L1 |
| Kundenreferenz | ✅ CSV | 108636 | L1 |
| Kalkulatorenwissen | ✅ CSV | 1051 | L1 |
| Gebäudetyp | ✅ | Kühl- und Lagerhallen (4 Brandabschnitte) mit 2-gesch. Büroeinbau + 36 Überladegruben | L2 |
| Höhe | ✅ | OK Attika ~+11,70 m; UK Binder +9,60 m; OK Binder First +11,35 m | L2 |
| Kran | ❌ | Nicht gefunden | — |
| Dachlasten | ✅ | Trapezblech/Dämm./Abkl. 0,45; Installation 0,10; PV 0,20 kN/m²; Wind/Schnee gemäß DIN | L2 |
| Baustoff | ✅ | Spannbeton-Satteldachbinder C 50/60 L=40,00 m; Stb.-Fertigteile; Expositionsklasse XC1 | L2 |
| Dachbegrünung | ❌ | Nicht gefunden | — |

**7/9 nachgewiesen** (3 strukturiert + 4 extrahiert). 2 nicht vorhanden.

---

## Strukturierte Kriterien (Anfragen.csv)

### Bauort
- **Wert:** Wietmarschen Lohne, D
- **Quelle:** Anfragen.csv, Zeile 2 → `BAUVORHABEN_ORT`, `BAUVORHABEN_NATION`

### Kundenreferenz
- **Wert:** 108636
- **Quelle:** Anfragen.csv, Zeile 2 → `KUNDE`

### Kalkulatorenwissen
- **Wert:** 1051
- **Quelle:** Anfragen.csv, Zeile 2 → `KALKULATOR`

---

## Extrahierte Kriterien (Projektdateien)

### Gebäudetyp ✅
- **Wert:** Kühl- und Lagerhallen — 4 Brandabschnitte (Halle 1.01: 1.929,75 m², Halle 1.02: 1.188,24 m², Halle 1.03: 1.183,80 m², Halle 2: 4.469,27 m², Halle 3: 4.468,43 m², Halle 4: 4.475,72 m²) mit 2-geschossigem Büroeinbau + 36 Überladegruben nach System Rekers
- **Quelle:** [`03_BA-1000_EG.pdf`](https://drive.google.com/file/d/1-B5mdf6SwtW7Ts1wP9ER1RSMA5eE8G5A/view)
- **Textstelle:** Grundriss EG M 1:200 zeigt sechs benannte Hallenbereiche: „Lager- und Logistikhalle 1.01" (1929,75 m²), „Lager- und Logistikhalle 1.02" (1188,24 m²), „Lager- und Logistikhalle 1.03" (1183,80 m²), „Lager- und Logistikhalle 2" (4469,27 m²), „Lager- und Logistikhalle 3" (4468,43 m²), „Lager- und Logistikhalle 4" (4475,72 m²). 2-geschossiger Büroeinbau an Achsen N-P/1-4 mit Empfang, Büro 1+2, SiBel, WC, Umkleide D/H, Aufenthalt (44,67 m²), Technik, ZBV im OG.
- **Bestätigt durch:** [`20-43_Schütte_Neubau Kühl- und Lagerhallen_11-2020.pdf`](https://drive.google.com/file/d/1n5WTUVaLv2ZYGhUGBHtfPqS7lEZpg1or/view) (3D-Rendering) zeigt „Kühlhalle 1-2, Höhe 16m, je 4.500 m²", „Lagerhalle 1-4, Höhe 12m, je 4.500 m²", „Verladeeinheit, Höhe 6m, je 2.230 m²", „Büroeinheit 2-Geschossig". Angebot 28687.1 Kopfzeile: „(Kühl-) und Lagerhalle Schütte Halle 1 - 4". Rekers Plan R1 zeigt 36 Ü-Gruben (blaue Quadrate) entlang Achse A.
- **Hinweis:** Gebäudetyp eindeutig als Kühl- und Logistikhalle mit integriertem Bürotrakt. Gesamtfläche ca. 17.715 m² (160,30 × 112,75 m). Gebäude in 4 Brandabschnitte unterteilt (Brandwände in Achsen 9, 17, 25, 33). Hallen 1.02/1.03 dienen als Kühlhallen mit abgehängter Kühldecke (Last < 0,15 kN/m² laut Pos. 1.02).

### Höhe ✅
- **Wert:** OK Attika ca. +11,70 m; OK Binder im First +11,35 m; OK Binder in der Traufe +10,65 m; UK Binder +9,60 m; Binderhöhe 1,15-1,75-1,15 m (Doppel-I-Profil); Bürotrakt +6,62 m (OG-Decke)
- **Quelle:** [`20-43_BA-2000_Ansichten Schnitt_2021-03-16.pdf`](https://drive.google.com/file/d/10r02lUgVMS8LbBHAzD4dfcEM82Nw3-k7/view)
- **Textstelle:** Alle vier Ansichten (Nord, Süd, Ost, West) und zwei Schnitte zeigen konsistent „~ +11.70" als OK Attika auf allen Giebelseiten. Schnitt 1-1 zeigt die Hallenstruktur mit 3% Dachgefälle (First mittig), Stb.-FT-Binder gem. Statik, Stb.-FT-Stütze mit angeformtem Fundament, Brandwand als Betonwand. Dachaufbau: Foliendachabdichtung als harte Bedachung, Dämmung, Dampfsperre, Trapezblech.
- **Bestätigt durch:**
  - [`Angebot Nr. 28687.1.pdf`](https://drive.google.com/file/d/1Ycoy4WmhQ4Q3UHRwirStnszw6qlwIz2W/view), Pos. 1.01: „Spannbeton.-Satteldachbinder L = 40,00 m (Achsmaß), in C 50/60, Querschnitt: Doppel-I-Profil, UK Binder + 9,60 m, 1,15-1,75-1,15 m hoch, OK Binder im First: + 11,35 m, OK Binder in d. Traufe: + 10,65 m"
  - [`Angebot Nr. 28687.12.pdf`](https://drive.google.com/file/d/1_u9kdBXjtIWbpt1h7TxD4sIu-FKP2ook/view), Schnittzeichnung Achse P/32-33: zeigt +11,70 (OK Attika), +11,54, +10,81 OK Stütze, +10,66 OK Stütze, +7,813, +4,063, +0,32, +0,30, −1,57, −1,60, −2,10, −2,15 (Fundamentunterkanten)
  - BA-1000 Grundriss: +3,40 m (OG-Fußboden Bürotrakt), +6,62 m (OG-Decke)
- **Hinweis:** Die Ansichten der Bauantragsplanung (BA-2000, Dezember 2020) zeigen konsistente Höhen „~ +11,70 m" für die OK Attika. Das REKERS-Angebot (März 2021) präzisiert UK Binder +9,60 m und OK First +11,35 m. Die 3D-Konzeptdarstellung (November 2020) nennt abweichend „Kühlhalle Höhe 16m" und „Lagerhalle Höhe 12m" — diese Werte beziehen sich vermutlich auf Gesamthöhen ab Geländeniveau, während die Bauantragsplanung die maßgeblichen Werte liefert.

### Kran ❌
- **Wert:** Nicht gefunden
- **Quelle:** Alle Angebotsversionen (28687.0/28687.1/28687.11/28687.12), Bauantragsplanung (BA-1000, BA-2000), Rekers Plan R1, 3D-Rendering und Schlussrechnung SR 439.012 durchsucht. Keine Erwähnung von Kran, Laufkran, Kranbahn, Kranbahnstütze oder Kranlasten.
- **Hinweis:** Gebäudetyp ist Kühl- und Logistikhalle mit Überladegruben für LKW-Andockung — typischer Speditions-/Logistikbetrieb ohne Hallenkran. Die Nutzlast auf der Hallensohle beträgt max. 20 kN/m² (Angebot Vorbemerkungen), konsistent mit Gabelstapler-/Flurförderzeugbetrieb. Kein Krandatenblatt, keine Kranzulage und keine Kranbahnstützen in den Angebotspositionen.

### Dachlasten ✅
- **Wert:**
  - Trapezblech, Dämmung, Abklebung = 0,45 kN/m²
  - Installation = 0,10 kN/m² (in Teilbereichen Last aus abgehängten Decken g < 0,15 kN/m²)
  - PV = 0,20 kN/m²
  - Wind- u. Schneelast gemäß DIN (keine expliziten WLZ/SLZ-Zonennummern angegeben)
  - Dachtrapezbleche als 3- bzw. 4-Feldträger ausgeführt
  - 3% Dachgefälle (Satteldachbinder, First mittig)
- **Quelle:** [`Angebot Nr. 28687.1.pdf`](https://drive.google.com/file/d/1Ycoy4WmhQ4Q3UHRwirStnszw6qlwIz2W/view), Vorbemerkungen Seite 1 (Abschnitt Grundlage, aus Angebot 28687.0 vom 21.12.2020 übernommen)
- **Textstelle:** Unter „Grundlage — Folgende Annahmen sind Grundlage des Angebots": „Dachbelastung: Wind- u. Schneelast gemäß DIN", „Photovoltaikanlage = 0,20 kN/m²", „Trapezblech, Dämmung, Abklebung = 0,45 kN/m²", „Installation = 0,10 kN/m² (in Teilbereichen Last aus einer abgehängten Decken g < 0,15 kN/m²)", „Dachtrapezbleche werden als 3- bzw. 4-Feldträger ausgeführt".
- **Bestätigt durch:** Pos. 1.01 „Dachbelastung: siehe Vorbemerkungen"; BA-2000 Schnitt zeigt Dachaufbau: „Foliendachabdichtung als harte Bedachung, Dämmung, Dampfsperre, Trapezblech"; Pos. 1.02 bestätigt Kühldecke-Zulage „Last < 0,15 kN/m²" für 8 Binder in Hallen 1.02/1.03.
- **Hinweis:** Anders als bei anderen REKERS-Projekten sind hier keine expliziten Windlast- und Schneelastzonen (WLZ/SLZ) im Angebot aufgeführt — nur der generische Verweis „gemäß DIN". Wietmarschen-Lohne (49835, Niedersachsen) liegt in der Norddeutschen Tiefebene. Die Installation ist mit 0,10 kN/m² etwas niedriger als bei anderen Projekten (typisch 0,15 kN/m²), was auf den Logistik-/Kühlhallen-Charakter ohne aufwendige Haustechnik hinweist. Die Zulässige Bodenpressung ist >= 250 kN/m² (charakteristisch), was die Nachtrag-1-Erhöhung auf 70 kN/m² erklärt.

### Baustoff ✅
- **Wert:** Spannbeton-Satteldachbinder L = 40,00 m in C 50/60, Querschnitt Doppel-I-Profil; Stb.-Fertigteile (Stützen, Brandwandplatten F90, Sockelplatten, Überladegruben); Expositionsklasse XC1; Bewehrung in Einzelpreisen enthalten
- **Quelle:** [`Angebot Nr. 28687.1.pdf`](https://drive.google.com/file/d/1Ycoy4WmhQ4Q3UHRwirStnszw6qlwIz2W/view), Pos. 1.01 und Vorbemerkungen
- **Textstelle:**
  - Pos. 1.01: „Spannbeton.-Satteldachbinder L = 40,00 m (Achsmaß) in C 50/60, Querschnitt: Doppel-I-Profil", „Expositionsklasse XC1", 56 Stück à 10.926,60 €
  - Vorbemerkungen (Bewehrung): „Die Bewehrung ist in den Einzelpreisen der Fertigteile enthalten."
  - Anschreiben: „Herstellung, Lieferung und Montage von Stb.-Fertigteilen"
  - Pos. 1.06: „Binder-, Innen- bzw. Aussenstütze mit werkseitig angeformter Fundamentplatte", 70 Stück
  - Pos. 1.11/1.14/1.17/1.20: „Brandwandplatten F90", Wandstärke 14 cm, OK +11,65 m
  - Pos. 1.28: „Sockelplatte — 16 cm Tragschale, 8 cm Dämmung (Extr. Hartschaum, WLG 035), 8 cm Vorsatzschale, 33 cm Gesamtstärke"
  - Pos. 1.31: „Überladegruben nach System Rekers — U-förmiges Fertigteil m. Bodenplatte, d = 21 cm", 36 Stück
- **Bestätigt durch:** Schlussrechnung SR 439.012 (Gewerk: „Stahlbetonfertigteile"); Rekers Plan R1 Legende zeigt Binder, Riegel, Brandwand, Stütze, Ü-Grube, Sockelelement, Stützwand als separate Fertigteilkategorien.
- **Hinweis:** Die Betongüte C 50/60 ist explizit für die Satteldachbinder angegeben. Anders als bei anderen REKERS-Projekten werden hier keine konkreten Bewehrungsstahlsorten (B500(A), ST1660/1860) in den Vorbemerkungen aufgeführt — nur der generische Hinweis „Die Bewehrung ist in den Einzelpreisen der Fertigteile enthalten." Die Bezeichnung „Spannbeton" impliziert jedoch den Einsatz von Spannstahl (ST1660/1860). Die Allgemeinen Liefer- und Leistungsbedingungen (ALB Fertigteile, Stand März 2018) sind als Anlage beigelegt, enthalten aber typischerweise die REKERS-Standardspezifikationen inkl. B500(A)/ST1660/1860.

### Dachbegrünung ❌
- **Wert:** Nicht gefunden
- **Quelle:** Alle Angebotsversionen (28687.0/28687.1/28687.11/28687.12), Bauantragsplanung (BA-1000, BA-2000), Bodengutachten, Rekers Plan R1 und 3D-Rendering durchsucht. Keine Erwähnung von Dachbegrünung, Gründach, extensiver/intensiver Begrünung oder Substratlasten.
- **Hinweis:** Dachaufbau laut BA-2000 Schnitt: „Foliendachabdichtung als harte Bedachung, Dämmung, Dampfsperre, Trapezblech" — standardmäßiges Industrieflachdach ohne Vegetationsschicht. Die Dachlastaufstellung im Angebot ist vollständig (0,45 + 0,10 + 0,20 = 0,75 kN/m² Eigenlast plus Klimalasten) und enthält keine Gründachbelastung. Konsistent mit dem Gebäudetyp Kühl- und Logistikhalle.

---

## Beobachtungen

Dieses Projekt ist eine umfangreiche Kühl- und Logistikanlage für die Schütte Gruppe in Wietmarschen-Lohne (Emsland). Mit Gesamtabmessungen von 160,30 × 112,75 m (ca. 17.715 m² Hallenfläche plus Bürotrakt) und 56 Satteldachbindern à 40 m Spannweite gehört es zu den größten Projekten im Testdatensatz. Das Angebot entwickelte sich über drei Versionen (28687.0 Richtpreis Dez 2020 → 28687.1 überarbeitetes Angebot März 2021 → Nachträge 28687.11 und 28687.12) mit einer Schlussrechnungssumme von ca. 1,845 Mio. € netto (pauschal 1.825.000 € + Nachträge).

Die Gebäudestruktur ist in 4 Brandabschnitte unterteilt (Achsen 9, 17, 25, 33) mit jeweils eigenständigen F90-Brandwandplatten (OK +11,65 m, Wandstärke 14 cm). Hallen 1.02 und 1.03 dienen als Kühlhallen mit abgehängter Kühldecke (Last < 0,15 kN/m²). 36 Überladegruben nach System Rekers (U-förmiges Fertigteil mit Bodenplatte, d = 21 cm, einschl. Isotoren) sind entlang Achse A angeordnet.

Bemerkenswert ist die Sohlbelastungserhöhung im Nachtrag 1 (28687.11): Die Bodenpressung wurde von 25 auf 70 kN/m² erhöht (Aussage Architekt Hempen), was ca. 1,50 m² größere Fundamente pro Stütze erforderte (Aufpreis 12.500,60 € für 70 Stützen). Dies deutet auf einen späten Planungswechsel zur höheren Lagerbelastung hin.

Anders als bei den meisten anderen REKERS-Projekten fehlen in diesem Angebot explizite Angaben zu Windlast-/Schneelastzonen (nur „gemäß DIN") und konkreten Bewehrungsstahlsorten (nur „in Einzelpreisen enthalten"). Die Betongüte C 50/60 und die Expositionsklasse XC1 sind jedoch explizit angegeben — C 50/60 ist die höchste in den bisher untersuchten Projekten vorkommende Betongüte für Spannbeton-Satteldachbinder.

Kran und Dachbegrünung sind definitiv nicht vorhanden — konsistent mit einer reinen Kühl-/Logistikhalle mit Gabelstaplerbetrieb (max. 20 kN/m² Sohlbelastung) und standardmäßigem Industrieflachdach.

---

## Quelle

- **Datenverzeichnis:** `~/Documents/projects/billable/REKERS__poc/data/2026-01-28_testdaten/37369/`
- **Design-Doc:** [Feasibility Report, Teil 2](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/design-doc-feasibility-report)
- **Session:** 86b63781-9830-4236-b9e0-ee8f6816ce23

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

