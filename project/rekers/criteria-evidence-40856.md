---
publish: true
---

# Kriteriennachweis — Projekt 40856: Neubau Logistikhalle

[[client-rekers]]

Nachweis für Design-Doc Teil 2. Datenverfügbarkeit pro Kriterium mit Quelldateien und extrahierten Werten.

Projekt: 40856 | Datum: 2024-02-19 | Wert: €1.816K

---

## Untersuchte Daten

| Ebene | Anzahl | Details |
|-------|--------|---------|
| L0: Ordnerdateien | 19 | 16 EML, 2 PDF, 1 ZIP |
| L2: EML-Anhänge | 63 | 42 PDF, 19 IMG, 2 DWG |
| _extracted (ZIP) | 3 | 3 PDF |

Multiplikator: 4,5x (L0 vs. L0+L2+ZIP)

---

## Übersicht

| Kriterium | Status | Wert | Quellebene |
|-----------|--------|------|------------|
| Bauort | ✅ CSV | Linthe, 14822, D | L1 |
| Kundenreferenz | ✅ CSV | 14244 | L1 |
| Kalkulatorenwissen | ✅ CSV | 1014 | L1 |
| Gebäudetyp | ✅ | Logistikhalle mit Überladebrücken und Technikeinbau | L2 |
| Höhe | ✅ | OK Attika +12,50 m; UK Paralleldachbinder Tiefpunkt +10,50 m; OK Binder Hochpunkt +12,00 m | L2 |
| Kran | ❌ | Nicht gefunden | — |
| Dachlasten | ✅ | PV 0,20; Installation 0,15; Trapezblech+Dä.+Abdichtung 0,23 kN/m²; WLZ2; SLZ2 | L2 |
| Baustoff | ✅ | Spannbeton-Parallelgurtbinder; Stb.-FT; B500(A); ST1660/1860 | L2 |
| Dachbegrünung | ❌ | Explizit ausgeschlossen (0,00 kN/m²) | — |

**7/9 nachgewiesen** (3 strukturiert + 4 extrahiert). 2 nicht vorhanden.

---

## Strukturierte Kriterien (Anfragen.csv)

### Bauort
- **Wert:** Linthe, 14822, D
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
- **Wert:** Logistikhalle (Hallengebäude) mit 16 Überladebrücken und Technikeinbau
- **Quelle:** [`Angebot zum VHP 20240426.pdf`](https://drive.google.com/file/d/1t9fI_zMAg6gtdlttlfg1lAcX1C2J8LAm/view) (Angebot Nr. 40856.1, Vorbemerkungen Seite 1)
- **Textstelle:** Bauvorhaben: „Neubau einer Logistikhalle für CTP in Linthe, Kampaweg 1, 14822 Linthe". Vorbemerkungen: „Geplant sind 16 Stk. Überladebrücken (Abmess.: 2,00/2,50 m), angeordnet werden diese in Gruppen zu je 8 Stück auf einer Länge von 4 x 8,00 m = 32,00 m." Option „Mezzanine" ist entfallen, kommt nicht zur Ausführung. lichte Höhe bis UK Decke Technikeinbau: +3,00 m.
- **Bestätigt durch:** [`240219 CTPark Linthe_GR-EG 1-200_Köster.pdf`](https://drive.google.com/file/d/16HXPw5TSULRG9Id8jgxA1LCzQiHaLpo-/view) (Entwurfsplanung LP3, Köster GmbH): Grundriss „Halle (Teil A+B) EG" zeigt Gesamtabmessungen ca. 216,00 m × 72,87 m mit Rangierzone, Ladezone, Personalräumen und Nebenräumen. 16 Überladebrücken sichtbar an der Ladezone. Flächenangaben: ~288,49 m² je Büro-/Nebenraumbereich × 2.
- **Hinweis:** Gebäudetyp ist eindeutig eine industrielle Logistikhalle für den Bauherrn CTP (Logistikpark-Entwickler). Köster GmbH ist Generalunternehmer, REKERS liefert Stb.-Fertigteile. Die Option „geänderte Hallenhöhe" mit UK Binder +12,10 m (Variante 2) ist ebenfalls entfallen — nur Variante 1 (Hauptangebot) kommt zur Ausführung.

### Höhe ✅
- **Wert:** OK Attika ca. +12,50 m; OK Paralleldachbinder Hochpunkt ca. +12,00 m; UK Paralleldachbinder Tiefpunkt ca. +10,50 m; UK Abfangträger Tiefpunkt ca. +10,39 m; OK Hallensohle ±0,00 m; Attikahöhe 14,75 m (Lastzusammenstellung)
- **Quelle:** [`Angebot zum VHP 20240426.pdf`](https://drive.google.com/file/d/1t9fI_zMAg6gtdlttlfg1lAcX1C2J8LAm/view) (Angebot Nr. 40856.1, Vorbemerkungen Seite 3, Abschnitt „Konstruktionshöhen")
- **Textstelle:** „OK Attika: ca. +12,50 m", „OK Paralleldachbinder im Hochpunkt: ca. +12,00 m", „UK Paralleldachbinder im Tiefpunkt: ca. +10,50 m", „UK Abfangträger im Tiefpunkt: ca. +10,39 m", „OK Hallensohle: +-0,00 m", „OK Aussengelände umlaufend: +-0,00 m, im Bereich der Überladegruben: -1,20 m"
- **Bestätigt durch:**
  - [`Systemschnitt Parallelgurtbinder 10,50m.pdf`](https://drive.google.com/file/d/1hIe9GYYXeYFAMsXTUVypBgokcU9atTYe/view) (REKERS Detailschnitt): Zeigt OK Attika +12,48, OK Hochsicke +12,20, OK Stütze +12,00, OK Brandwand +12,80, UK Abfangbinder +10,39. Spannweite 24,00 m, Achsraster 8,00 m, Gefälle 1,5%.
  - [`240219 CTPark Linthe_SC - Köster .pdf`](https://drive.google.com/file/d/1mY2AiZxX-u6SEFLIS-FUlZpIIwwF57AJ/view) (Genehmigungsplanung LP4, Schnitte): Variante 1 (Hauptangebot) zeigt UK Binder +10,50 m; Variante 2 (Option, entfallen) zeigte UK Binder +12,20 m.
  - [`240219 CTP Linthe - Lastzusammenstellung.pdf`](https://drive.google.com/file/d/1s7xbUpLfgU62nl6H_8m62TKGTr1_90sF/view): Attikahöhe h = 14,75 m, Achsraster 8,00 × 24,00 m.
- **Hinweis:** Die Attikahöhe in der Lastzusammenstellung (14,75 m) weicht von der REKERS-Konstruktionshöhe (OK Attika +12,50 m) ab. Die Lastzusammenstellung stammt von Köster (19.02.2024) und basiert auf der Entwurfsplanung; das REKERS-Angebot 40856.1 (18.04.2024) basiert auf der überarbeiteten Genehmigungsplanung nach Vergabegespräch. Die niedrigere REKERS-Höhe (+12,50 m) ist maßgeblich, da die Option „geänderte Hallenhöhe" entfallen ist. Die Pos. 1.1.1 im Angebot bestätigt: Spannbeton-Parallelgurtbinder l = 24,00 m, Trägerhöhe h = 1,10 m, 1,50% Gefälle.

### Kran ❌
- **Wert:** Nicht gefunden
- **Quelle:** Angebot 40856.1 Vorbemerkungen, Lastzusammenstellung, Genehmigungsplanung LP4 (Schnitte), Grundriss LP3 und alle Angebotspositionen durchsucht. Kein Kran, keine Kranbahn, keine Kranlasten erwähnt.
- **Hinweis:** Logistikhalle für CTP mit Gabelstaplerbetrieb (FL3, Eigengewicht 44 kN, Hublast 25 kN laut Lastzusammenstellung). Die Anpralllasten im Angebot beziehen sich auf „Gabelstapler mit einem zul. Gesamtgewicht von 6,9 to" — konsistent mit einem Logistikbetrieb ohne Deckenkrane. Die Hallenaussteifung erfolgt über eingespannte Stützen (Quer-/Längsrichtung über FT-Binder/Träger gekoppelt), nicht über Kranbahnstützen.

### Dachlasten ✅
- **Wert:**
  - Photovoltaikanlage = 0,20 kN/m² (Aufbauhöhe h ≤ 0,34 m)
  - Abhanglast (Installation) = 0,15 kN/m²
  - Trapezblech, Dämmung, Abdichtung = 0,23 kN/m²
  - Dachbegrünung = 0,00 kN/m² (kommt nicht zur Ausführung)
  - Wind: Windlastzone WZ 2, Binnenland
  - Schnee: Schneelastzone 2, Norddeutsches Tiefland, Geländehöhe ca. 47,00 m ü. NHN; sk = 0,900 kN/m²
  - Dachtrapezbleche als 3- oder 4-Feldsystem, Koppellast 1,5 kN/m, Durchlauffaktor 1,10
- **Quelle:** [`Angebot zum VHP 20240426.pdf`](https://drive.google.com/file/d/1t9fI_zMAg6gtdlttlfg1lAcX1C2J8LAm/view) (Angebot Nr. 40856.1, Vorbemerkungen Seite 2, Abschnitt „Dachbelastung Hallendach")
- **Textstelle:** „Photovoltaikanlage = 0,20 kN/m² (Aufbauhöhe h <= 0,34 m)", „Dachbegrünung = 0,00 kN/m² (kommt nicht zur Ausführung)", „Trapezblech, Dämmung, Abdichtung = 0,23 kN/m²", „Abhanglast = 0,15 kN/m²". Wind- und Schneelasten: „Windlastzone WZ 2, Binnenland", „Schneelastzone 2, Norddeutsches Tiefland, Geländehöhe ca. 47,00 m ü. NHN".
- **Bestätigt durch:**
  - [`240219 CTP Linthe - Lastzusammenstellung.pdf`](https://drive.google.com/file/d/1s7xbUpLfgU62nl6H_8m62TKGTr1_90sF/view) (Köster, 19.02.2024): Identische Werte — Dachlasten Halle: Installation 0,150 kN/m² (Schwer), PV-Anlage 0,200 kN/m² (Mittel), Gründach 0,000 kN/m² (keins); Eigengewicht Dach: Trapezblech 0,150, Dämmung PIR/PUR d=12cm 0,060, Dichtung PVC-Folie 0,020 = 0,230 kN/m²; SLZ 2, WLZ 2; Σ Dachlast Halle = 1,494 kN/m².
  - [`Systemschnitt Parallelgurtbinder 10,50m.pdf`](https://drive.google.com/file/d/1hIe9GYYXeYFAMsXTUVypBgokcU9atTYe/view): Dachaufbau bestätigt: Dachabdichtung (PVC) d=1,5 mm, Dämmung (PIR/PUR) d=10 cm, Dampfbremse, Trapezblech d=20 cm.
- **Hinweis:** Erdbeben: „Ausserhalb einer Erdbebenzone" (DIN 4149-2005), Sap,R = 0,0599 m/s² (DIN EN 1998-1). Hallensohlenbelastung = 60 kN/m² (Sohle, nicht Dach). Mezzanin-Ausbaulast 7,500 kN/m² in der Lastzusammenstellung, aber Mezzanine kommt laut Angebot 40856.1 nicht zur Ausführung.

### Baustoff ✅
- **Wert:** Spannbeton-Parallelgurtbinder (l = 24,00 m, h = 1,10 m); Spannbeton-Abfangträger (l = 18,00 m, h ≈ 1,25 m); Stahlbeton-Fertigteile; Betonstahl B500(A); Spannstahl ST1660/1860 nach DIN 488; Beton nach DIN EN 206-1/DIN 1045-2 mit Portlandzement; Druckfestigkeitsklasse gemäß statischer Erfordernis
- **Quelle:** [`Angebot zum VHP 20240426.pdf`](https://drive.google.com/file/d/1t9fI_zMAg6gtdlttlfg1lAcX1C2J8LAm/view) (Angebot Nr. 40856.1, Vorbemerkungen Seite 3, Abschnitt 4 „Beton" und Seite 4, Abschnitt 5 „Bewehrung")
- **Textstelle:**
  - Beton: „Die Ausführung der Betonarbeiten erfolgt nach DIN EN 206-1/DIN 1045-2 unter Verwendung von Portlandzement. Druckfestigkeitsklasse/Betongüte gemäß statischer Erfordernis."
  - Bewehrung: „Betonstahl B500(A), Spannstahl ST1660/1860 nach DIN 488."
  - Pos. 1.1.1: „Spannbeton-Parallelgurtbinder, Systemachslänge l = 24,00 m, Trägerhöhe h = 1,10 m, im Gefälle mit 1,50 % verlegt" (56 Stück, €289.055,20)
  - Pos. 1.1.3: „Spannbeton-Abfangträger für vorgen. Binder, Systemachslänge l = 18,00 m, Trägerhöhe ca. 1,25 m" (32 Stück, €199.238,40)
  - Expositionsklassen: Innenbauteile XC1/W0; Gründung XC2/XF1/WF; Außen XC4/XF1/WF
  - Feuerwiderstandsklassen: Binder/Abfangträger/Riegel F90; Stützen F90; Brandwände F90
- **Bestätigt durch:** Angebot 40856.2 (Ergänzungsangebot für Trafogebäude) bestätigt Stb.-Fertigteile als Bauweise. Systemschnitt zeigt Parallelgurtbinder-Querschnitt mit 1,10 m Trägerhöhe und 1,5% Gefälle.
- **Hinweis:** Betongüte als „gemäß statischer Erfordernis" angegeben, nicht als konkrete Klasse. Dachform: Paralleldachbinder im Gefälle mit 1,5% verlegt — kein Satteldachbinder wie bei Projekt 35764, sondern Parallelgurtbinder (flaches Dach mit Gefälle). Sandwichwände: EPS WLG 035 (8 cm), Sockel: XPS WLG 035 (8 cm).

### Dachbegrünung ❌
- **Wert:** Explizit ausgeschlossen
- **Quelle:** Angebot 40856.1 Vorbemerkungen: „Dachbegrünung = 0,00 kN/m² (kommt nicht zur Ausführung)". Lastzusammenstellung: Gründach = 0,000 kN/m² (Auswahl: „keins").
- **Hinweis:** Dachbegrünung wurde in beiden Dokumenten (Köster-Lastzusammenstellung und REKERS-Angebot) explizit als nicht vorgesehen erfasst. Standardmäßiger Industriedachaufbau: PVC-Dachabdichtung, PIR/PUR-Dämmung, Dampfbremse, Trapezblech. PV-Anlage ist vorgesehen (0,20 kN/m²), aber keine Vegetationsschicht.

---

## Beobachtungen

Dieses Projekt zeigt eine klare Angebotsevolution: das REKERS-Angebot 40856.0 (Richtpreis, 05.03.2024) wurde nach Vergabegespräch (11.04.2024) zum überarbeiteten Angebot 40856.1 (18.04.2024) konsolidiert, gefolgt vom Ergänzungsangebot 40856.2 (27.05.2024) für ein separates Trafogebäude. Die Gesamtangebotssumme (€1.816K in Anfragen.csv) setzt sich zusammen aus 40856.1 (€1.778.723,20 brutto vor Nachlass) + 40856.2 (€37.403,10).

Mehrere Optionen des ursprünglichen Angebots wurden nach der Vergabebesprechung gestrichen: Mezzanine, geänderte Hallenhöhe (UK Binder +12,10 m), und Jumbo-Überladebrücken. Dies erklärt die Diskrepanz zwischen der Lastzusammenstellung (Attikahöhe 14,75 m, mit Mezzanine 7,5 kN/m²) und den endgültigen REKERS-Konstruktionshöhen (OK Attika +12,50 m, ohne Mezzanine).

Der Binderachsabstand wurde von 8,00 m auf 9,00 m geändert. Die Traufstützen und Brandwandstützen werden im Achsabstand von 9,00 m angeordnet, Innenstützen im Abstand von 2 × 9,00 = 18,00 m. Dies ist ungewöhnlich und resultiert aus der Optimierung der Überladebrücken-Anordnung.

Die Pauschalpreis-Vereinbarung (€1.510.000,00 netto nach 15% Nachlass und Pauschal-Nachlass) basiert auf dem VHP (Vergabehandlungsprotokoll) — ein seltenes Vertragskonstrukt in den untersuchten Projekten, das auf fortgeschrittene Verhandlungen mit Köster GmbH hindeutet.

Kran ist definitiv nicht vorhanden — typisch für Logistikhallen mit Gabelstaplerbetrieb. Dachbegrünung ist explizit in beiden Quellen (Bauherr und REKERS) als nicht vorgesehen dokumentiert.

---

## Quelle

- **Datenverzeichnis:** `~/Documents/projects/billable/REKERS__poc/data/2026-01-28_testdaten/40856/`
- **Design-Doc:** [Feasibility Report, Teil 2](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/design-doc-feasibility-report)
- **Session:** 228d8e70-dc40-4996-b1c4-43a5cd3ff802

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

