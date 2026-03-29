---
publish: true
---

# Kriteriennachweis — Projekt 36185: Lagergebäude GGM Gastro
[[client-rekers]]

Nachweis für Design-Doc Teil 2. Datenverfügbarkeit pro Kriterium mit Quelldateien und extrahierten Werten.

Projekt: 36185 | Datum: 2019-12-05 | Wert: €3.869K

---

## Untersuchte Daten

| Ebene | Anzahl | Details |
|-------|--------|---------|
| L0: Ordnerdateien | 67 | 62 EML, 3 PDF, 2 ZIP |
| L2: EML-Anhänge | 217 | 144 PDF, 14 JPG, 38 PNG, 6 XLSX, 15 sonstige |
| _extracted (ZIP) | 11 | 11 PDF |

Multiplikator: 4,4x (L0 vs. L0+L2+ZIP)

---

## Übersicht

| Kriterium | Status | Wert | Quellebene |
|-----------|--------|------|------------|
| Bauort | ✅ CSV | Ochtrup, 48607, D | L1 |
| Kundenreferenz | ✅ CSV | 529 | L1 |
| Kalkulatorenwissen | ✅ CSV | 917 | L1 |
| Gebäudetyp | ✅ | Lagerhallen Q-S (Logistik-/Lagergebäude) mit Überladebrücken | L2 |
| Höhe | ✅ | H = 10,00 m (Flachdach); Bezugshöhe ze = 10,50 m | L2 |
| Kran | ❌ | Nicht gefunden | — |
| Dachlasten | ✅ | Dachlast 0,15; Installation 0,15; PV 0,25 kN/m²; WZ2; SLZ1 | L2 |
| Baustoff | ✅ | Spannbeton (Satteldachbinder C50/60-F90); Stb.-Fertigteile | L2 |
| Dachbegrünung | ❌ | Nicht gefunden | — |

**7/9 nachgewiesen** (3 strukturiert + 4 extrahiert). 2 nicht vorhanden.

---

## Strukturierte Kriterien (Anfragen.csv)

### Bauort
- **Wert:** Ochtrup, 48607, D
- **Quelle:** Anfragen.csv, Zeile 2 → `BAUVORHABEN_ORT`, `PLZ`, `NATION`

### Kundenreferenz
- **Wert:** 529
- **Quelle:** Anfragen.csv, Zeile 2 → `KUNDE`

### Kalkulatorenwissen
- **Wert:** 917
- **Quelle:** Anfragen.csv, Zeile 2 → `KALKULATOR`

---

## Extrahierte Kriterien (Projektdateien)

### Gebäudetyp ✅
- **Wert:** Lagerhallen Q-S (Logistik-/Lagergebäude) mit 29 Überladebrücken und Blocklager-Bereichen, je Halle ca. 10.000 m²
- **Quelle:** [`04_03_A   06.12.2021_GrundrissEG_M_200.pdf`](https://drive.google.com/file/d/10iWuIgrHoRbKFkyIWmu0j6n_MkmbYU3-/view) (Bauantragsplanung M. 1:200)
- **Textstelle:** Grundriss zeigt drei Lagerhallen Q, R und S mit jeweils ca. 10.000 m² Grundfläche. Die Hallen enthalten Blocklager-Bereiche und werden über Sektionaltore an der Südseite erschlossen. Plankopf: „Neubau der Lagerhallen Q - V für ggmgastro.com, Weinerpark 16, 48607 Ochtrup".
- **Bestätigt durch:**
  - [`04_05   06.12.2021_Ansichten_M_200.pdf`](https://drive.google.com/file/d/11xbGmEWAAA1r0DZE9-HLGey7zQOe4Jl0/view): Vier Ansichten (Norden, Süden, Osten, Westen) zeigen eingeschossige Hallenkonstruktionen mit Satteldach, vorgelagertem Rampenbereich und ggmgastro.com-Branding. Sektionaltore auf Südseite durchgehend sichtbar.
  - [`Angebot 28388.11 d.pdf`](https://drive.google.com/file/d/1JHU_vPWaII-fT4Dae4sjfgmo2R_zqnDy/view) Anschreiben: „Neubau der Lagerhallen Q-S für GGM-Gastro, Weinerpark 16 in 48607 Ochtrup". Pos. 1.160 beschreibt 29 Stück Stb.-Überladebrücken in der Achse A/5-27.
- **Hinweis:** Die Lagerhallen dienen der Logistik für GGM Gastro International GmbH (Gastronomiebedarf-Großhändler). Gabelstaplerverkehr ist das primäre Transportmittel (Anpralllast 4,8 to lt. Vorstatik Lastannahmen). Es handelt sich um Erweiterungsbauten zu bestehenden Hallen auf dem Firmengelände.

### Höhe ✅
- **Wert:** Gebäudehöhe H = 10,00 m (Flachdach); Bezugshöhe ze = 10,50 m (inkl. Attika hp = 0,50 m); Brandwand-Oberkante max. +15,25 m
- **Quelle:** [`2019.12.17 Vorstatik.pdf`](https://drive.google.com/file/d/1eCWnJthTOyUD9zQGkAcheCWOFAEn1I97/view), Seite 5 (Pos. SW — Schnee- und Windlasten)
- **Textstelle:** Pos. SW listet: „Gebäudehöhe (Höhe Flachdach) H = 10.00 m", „Traufbereich mit Attika, Attikaüberstand hp = 0.50 m", Bezugshöhe ze = 10.50 m. Die Berechnung bezieht sich auf „ein exemplarisches Standardmodul am Beispiel Halle A" mit B = 50,00 m und L = 90,50 m.
- **Bestätigt durch:**
  - [`04_04   06.12.2021_Schnitte_M_200.pdf`](https://drive.google.com/file/d/15I5AKhaNr_aICvDw3MVbCoZnEPbUwcj8/view): Drei Schnitte (A-A, B-B, C-C) durch Lagerhallen Q, R, S zeigen konsistente Hallenhöhen. Rampenbereich auf der Südseite sichtbar.
  - Angebot 28388.11d Pos. 1.200 (Brandwände): Unterkante -0,47 m, Oberkante max. +15,25 m (Brandwand reicht über First hinaus). Pos. 1.340 (Innenstützen): Stütze b/d = 60/60 cm, Länge = 14,55 m inkl. angeformtem Fundament, C50/60 F90.
  - Angebot 28388.11d Pos. 1.170 (Sandwichwände): Oberkante Tragschale max. +5,65 m (niedrigere Wandbereiche im Dockbereich).
- **Hinweis:** Die Vorstatik (17.12.2019) bezieht sich auf das Standardmodul „Halle A". Die Lagerhallen Q-S (Angebot 28388.11d, Mai 2023) verwenden das gleiche Satteldachbinder-System (L = 24,00 m, Achsmaß 7,00 m), daher ist die Gebäudehöhe vergleichbar. Stützenlängen im Rampenbereich (15,75 m) sind höher aufgrund der tieferliegenden Rampe (Überladebrücken-Oberkante -0,20 m, Unterkante -1,70 m).

### Dachlasten ✅
- **Wert:**
  - Dachlast (Dämmung und Abdichtung) = 0,15 kN/m²
  - Installation = 0,15 kN/m²
  - PV (optionale PV-Anlage) = 0,25 kN/m²
  - Wind: Windzone WZ 2 (vb,0 = 25,00 m/s; qb,0 = 0,39 kN/m²)
  - Schnee: Schneelastzone SLZ 1, Binnenland (sk = 0,65 kN/m²)
  - Geschwindigkeitsdruck qp = 0,68 kN/m²
- **Quelle:** [`2019.12.17 Vorstatik.pdf`](https://drive.google.com/file/d/1eCWnJthTOyUD9zQGkAcheCWOFAEn1I97/view), Seite 4 (Pos. Z — Wind- und Schneelastzonen) und Seite 35 (Pos. L — Lastannahmen)
- **Textstelle:**
  - Pos. Z: „Windzone WZ = 2", „Schneelastzone SLZ = 1", „char. Schneelast sk = 0.65 kN/m²", „Geländehöhe ü. NN Hs = 64.00 m"
  - Pos. L (Lastannahmen): „Flächenlasten Dachdecke: gk_DA Dämmung und Abdichtung = 0,15 kN/m²; qk_DA1 Install Installationen = 0,15 kN/m²; qk_DA2 PV optionale PV-Anlage = 0,25 kN/m²"
  - Pos. SW: Windlastermittlung nach DIN EN 1991-1-4:2010-12, Bezugshöhe ze = 10,50 m
- **Bestätigt durch:**
  - Vorstatik Pos. TB1 (Stahl-Trapezprofil): Belastungsgrafiken zeigen Gk = 0,15 kN/m² (Eigenlast), Qk.N = 0,40 kN/m² (Nutzlast), Qk.S = 0,52 kN/m² (Schneelast) für HOESCH T205.1-0.88 Dachtrapezbleche im 3-Feld-System mit L = 7,50 m Stützweite.
  - Angebot 28388.11d Pos. 1.10: „Spannbeton-Satteldachbinder [...] im Abstand von 7,00m [...] incl. HTU-Schiene für die Dachtrapezbleche"
- **Hinweis:** Die Lastannahmen gelten standortübergreifend für alle Hallen am Standort Ochtrup (WZ2/SLZ1). Im Vergleich zum Projekt 35764 (WZ2/SLZ2 bei Gescher) liegt Ochtrup in der niedrigeren Schneelastzone 1 (sk = 0,65 vs. 0,85 kN/m²). PV-Last mit 0,25 kN/m² leicht höher als bei 35764 (0,20 kN/m²). Nutzlast Kategorie E (Lagerräume) mit Gabelstapler-Anpralllast 4,8 to.

### Baustoff ✅
- **Wert:** Spannbeton-Satteldachbinder (C50/60 - F90, l = 24,00 m); Spannbeton-Parallelgurtbinder (C50/60 - F90, l = 12,00 m); Stahlbeton-Fertigteile (Stb.-Fertigteile); Stb.-Stützen C50/60 F90; Stb.-Sandwichwände und Brandwände C35/45 - F90
- **Quelle:** [`Angebot 28388.11 d.pdf`](https://drive.google.com/file/d/1JHU_vPWaII-fT4Dae4sjfgmo2R_zqnDy/view), Vorbemerkungen und Positionen (18 Seiten)
- **Textstelle:**
  - Anschreiben: „Herstellung, Lieferung und Montage von Stb.-Fertigteilen für das Bauvorhaben: Neubau der Lagerhallen Q-S"
  - Pos. 1.10: „Spannbeton-Satteldachbinder Stat. Pos. BI1 [...] L=24,00m Achsmass im Abstand von 7,00m, Höhe = ca. 88-124-88 cm, C50/60 - F90, incl. HTU-Schiene für die Dachtrapezbleche"
  - Pos. 1.11: „Spannbeton-Parallelgurtbinder Stat. Pos. BI2 [...] L=12,00m [...] Höhe = ca. 88 cm, C50/60 - F90"
  - Pos. 1.20/1.21: „Stb.-Abfangbinder [...] B/H = 35/135cm, C50/60 - F90"
  - Pos. 1.170: „Stb.-Sandwichwände [...] d=16+12+8=36cm mit 12 cm EPS 035, C35/45 - F90"
  - Pos. 1.200: „Stb.-Brandwände [...] d=16cm, C35/45, Feuerwiderstandsklasse F90"
  - Pos. 1.340: „Stützen mit angeformtem Fundament, Innenstützen [...] Stütze b/d = 60/60cm [...] C50/60 F90"
- **Bestätigt durch:** Anlagen des Angebots enthalten „Merkblatt Nr. 1 der Fachvereinigung Deutscher Betonfertigteilbau e.V. über Sichtbetonflächen von Fertigteilen aus Beton und Stahlbeton". Vorstatik (708 Seiten) enthält vollständige statische Berechnung aller Positionen.
- **Hinweis:** Im Unterschied zu Projekt 35764 enthält dieses Angebot keine expliziten Abschnitte zu „Beton" und „Bewehrung" in den Vorbemerkungen — die Materialspezifikationen sind ausschließlich in den Einzelpositionen dokumentiert. C50/60 für tragende Elemente (Binder, Stützen, Abfangbinder), C35/45 für Wandelemente (Sandwich, Brandwand, Giebelbalken). Alle tragenden Elemente durchgehend F90 (Feuerwiderstandsklasse). Die Stb.-Sandwichwände mit EPS 035 Dämmkern sind als dreischichtige Elemente (16+12+8 cm) konzipiert.

### Kran ❌
- **Wert:** Nicht gefunden
- **Quelle:** Alle Angebotsversionen (28388.1 bis 28388.16, 28388.50, 28388.61), Vorstatik, Schnitte und Grundrisse durchsucht. Keine Erwähnung von Kranbahn, Laufkran oder Kranlasten in den Positionen oder Lastannahmen.
- **Hinweis:** Das Gebäude ist als Logistik-Lagerhalle für Gabelstaplerbetrieb konzipiert (Anpralllast 4,8 to lt. Vorstatik Lastannahmen, Pos. L). Die Vorbemerkungen (Abschnitt 7.1) erwähnen „unser Kran" — dies bezieht sich auf REKERS' Montagekran für die Fertigteilmontage, nicht auf einen gebäudeintegrierten Kran. Im Grundriss sind ausschließlich Blocklager-Bereiche und Gabelstapler-Fahrstraßen dargestellt, keine Kranbahnen.

### Dachbegrünung ❌
- **Wert:** Nicht gefunden
- **Quelle:** Alle Angebotsversionen, Vorstatik (Lastannahmen), Schnitte und Ansichten durchsucht. Keine Erwähnung von Dachbegrünung, Gründach oder zusätzlichen vegetationsbezogenen Dachlasten.
- **Hinweis:** Die Dachkonstruktion besteht aus Stahl-Trapezprofilen (HOESCH T205.1-0.88) auf Spannbeton-Satteldachbindern. Die Dachlastenaufstellung in der Vorstatik ist vollständig: Dämmung/Abdichtung (0,15) + Installation (0,15) + PV (0,25) = 0,55 kN/m² Eigenlast plus Klimalasten — keine Gründachbelastung enthalten. Konsistent mit dem Gebäudetyp Lagerhalle.

---

## Beobachtungen

Dieses Projekt ist mit einer Angebotssumme von 3.250.000 € netto (nach 6% Nachlass und 3% Skonto) eines der größten im Testdatensatz. Das Angebot 28388.11d ist die vierte Hauptversion (basierend auf 28388.11"b"/überarbeitet) und deckt die Lagerhallen Q-S mit insgesamt ca. 30.000 m² Grundfläche ab.

Die Angebotshistorie ist außergewöhnlich umfangreich: 16+ Angebotsversionen (28388.1 bis 28388.16) plus Ergänzungsangebote (28388.13, 28388.14, 28388.14a), Nachträge (28388.15) und separate Angebote für spätere Bauphasen (28388.50 in Unterordner 15846, 28388.61 in Root-Extracted). Dies deutet auf ein langjähriges Projekt mit mehreren Bauphasen hin (2019-2023+).

Der Bauherr ist Bakir & Inan Verpachtung und Vermietung GbR (lt. Vorstatik und Planköpfen), der Nutzer ist GGM Gastro International GmbH. Die Tragwerksplanung und Architektur stammen vom Ingenieurbüro Johann Hoff, Gronau.

Die Vorstatik (708 Seiten, Dezember 2019) bezieht sich auf die ursprüngliche „Halle A" als Standardmodul. Die Lagerhallen Q-S (Mai 2023) sind spätere Erweiterungen mit demselben Konstruktionssystem (Satteldachbinder l = 24 m, 7 m Raster). Die standortspezifischen Parameter (WZ2, SLZ1) gelten für alle Bauphasen.

Kran ist definitiv nicht vorhanden — das Gebäude dient ausschließlich der Lagerlogistik mit Gabelstaplerbetrieb. Dachbegrünung ist ebenfalls nicht vorhanden — standardmäßiges Industrieflachdach mit Trapezblech und Folienabdichtung.

Niederländische Korrespondenz in Unterordner 15846 (WeTransfer-Archive, E-Mails auf Niederländisch an oldehenskenbv.nl und inprebo.nl) deutet auf eine Zusammenarbeit mit niederländischen Subunternehmern hin, möglicherweise für Hohlkörperdecken oder Fertigteilarbeiten.

---

## Quelle

- **Datenverzeichnis:** `~/Documents/projects/billable/REKERS__poc/data/2026-01-28_testdaten/36185/`
- **Design-Doc:** [Feasibility Report, Teil 2](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/design-doc-feasibility-report)
- **Session:** 5442204e-3eb4-4721-a333-cd6fbf28b1ab

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

