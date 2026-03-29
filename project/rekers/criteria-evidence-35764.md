---
publish: true
---

# Kriteriennachweis — Projekt 35764: Neubau Werkhalle van Eckendonk
[[client-rekers]]

Nachweis für Design-Doc Teil 2. Datenverfügbarkeit pro Kriterium mit Quelldateien und extrahierten Werten.

Projekt: 35764 | Datum: 2019-07-19 | Wert: €491K

---

## Untersuchte Daten

| Ebene | Anzahl | Details |
|-------|--------|---------|
| L0: Ordnerdateien | 25 | 22 EML, 2 PDF, 1 ZIP |
| L2: EML-Anhänge | 65 | 36 PDF, 14 JPG, 14 PNG, 1 sonstige |
| _extracted (ZIP) | 13 | 10 PDF, 3 DWG |

Multiplikator: 4,1x (L0 vs. L0+L2+ZIP)

---

## Übersicht

| Kriterium | Status | Wert | Quellebene |
|-----------|--------|------|------------|
| Bauort | ✅ CSV | Gescher, 48712, D | L1 |
| Kundenreferenz | ✅ CSV | 108636 | L1 |
| Kalkulatorenwissen | ✅ CSV | 1051 | L1 |
| Gebäudetyp | ✅ | Werkhalle (Hallengebäude) mit 2-gesch. Bürotrakt | L2 |
| Höhe | ✅ | 14,35 m (First); OK-Stütze +13,35 m; Hakenhöhe 8,70 m | L2 |
| Kran | ✅ | Ja — 2x ABUS ZLK 40t/10t + 2x optional 20t | L2 |
| Dachlasten | ✅ | Dachlast 0,45; Installation 0,15; PV 0,20 kN/m²; WLZ2; SLZ2 | L2 |
| Baustoff | ✅ | Spannbeton (Satteldachbinder); Stb.-Fertigteile; B500(A); ST1660/1860 | L2 |
| Dachbegrünung | ❌ | Nicht gefunden | — |

**8/9 nachgewiesen** (3 strukturiert + 5 extrahiert). 1 nicht vorhanden.

---

## Strukturierte Kriterien (Anfragen.csv)

### Bauort
- **Wert:** Gescher, 48712, D
- **Quelle:** Anfragen.csv, Zeile 2 → `BAUVORHABEN_ORT`, `PLZ`, `NATION`

### Kundenreferenz
- **Wert:** 108636
- **Quelle:** Anfragen.csv, Zeile 2 → `KUNDE`

### Kalkulatorenwissen
- **Wert:** 1051
- **Quelle:** Anfragen.csv, Zeile 2 → `KALKULATOR`

---

## Extrahierte Kriterien (Projektdateien)

### Gebäudetyp ✅
- **Wert:** Werkhalle (Hallengebäude) mit angebundenem 2-geschossigem Bürotrakt
- **Quelle:** [`19-02_2019-04-24_E-1000_Grundriss, Schnitt, Ansichten.pdf`](https://drive.google.com/file/d/1UheOsMbK74MyCW5U4YukeAGGGA83jWdU/view)
- **Textstelle:** Konzeptstudie von bb architekten zeigt „Werkhalle ~1850 m²" mit angeschlossenen „Sozialräume, Sekretariat, Büros ~150 m²". Plankopf: „Neubau einer Werkhalle". Haupthalle mit Spannweite 30,00 m × 67,50 m, 50t-Kranbahn und Sektionaltore.
- **Bestätigt durch:** [`19-02_BA-1000d_Grundrisse+Schnitt.pdf`](https://drive.google.com/file/d/16FGeRO1U9p1ayjKIWL77jwedUogXCKMG/view) (Bauantragsplanung M. 1:100) zeigt „Werkhalle A= 1984,66 m²" mit Räumen für Büro, Besprechung, Umkleide, Aufenthalt, Kompressor, HA/Technik. Abschnitt 1.1.2 des Angebots 28273.30 ist explizit als „Bürotrakt: 2-geschossig" betitelt.
- **Hinweis:** Gebäudetyp ist eindeutig eine industrielle Werkhalle (Hallengebäude) mit integriertem 2-geschossigem Bürotrakt. Das Angebot trennt die Preise in „Hallenkonstruktion" und „Bürotrakt: 2-geschossig".

### Höhe ✅
- **Wert:** Firsthöhe +14,35 m (Bauantragsplanung); OK-Stütze ca. +13,35 m; Unterkante Binder ca. +12,35 m; Hakenhöhe 8,70 m; Bürotrakt +7,25 m
- **Quelle:** [`19-02_BA-3000d_Ansichten.pdf`](https://drive.google.com/file/d/1V21LC2EeEWtVWsrTWPTFDdFlE2ziuf54/view)
- **Textstelle:** Alle vier Ansichten (Westen, Norden, Osten, Süden) zeigen konsistente Höhenangaben: Geländeoberkante ±0,00, Hallenfirst bei +14,35, Bürobereich bei +7,25. Der Schnitt in BA-1000d bestätigt: „14,35" Gesamthöhe, „+12,34" Unterkante Binder, „+0,30" Sockelelement OK.
- **Bestätigt durch:** Konzeptstudie E-1000 (Vorgängerversion) zeigt +14,30 m; Angebot 28273.30 Pos. 1.1.1.1.01 nennt „Unterkante Binder: ca. +12,35 m" und „OK-Stütze = ca. +13,35 m"; ABUS-Krandatenblatt zeigt HG = 12340 mm und HS = 10390 mm.
- **Hinweis:** Die frühe Konzeptstudie (E-1000, April 2019) zeigt 14,30 m; die Bauantragsplanung (BA-1000d/BA-3000d, Feb 2020) präzisiert auf 14,35 m. Die Angebotswerte bestätigen OK-Stütze bei +13,35 m. Hakenhöhe 8,70 m aus der Schnittdarstellung der Konzeptstudie.

### Kran ✅
- **Wert:** Ja. 2x ABUS ZLK 40t/10t (Zweiträger-Laufkran, Tandembetrieb, max. Gesamttraglast 50t je Kranbahn); plus 2x optionale 20t-Kranbahnen
- **Quelle:** [`19-02_2020-03-02_Krandatenblätter.pdf`](https://drive.google.com/file/d/1SDlnk2C1sQrx0bDpCnxNHemMDhcV7Aqk/view) (ABUS Kransysteme GmbH)
- **Textstelle:** 6-seitiges ABUS-Dokument, Auftrag 16949810, vom 02.03.2020: „Kran: ZLK 40 t / 10 t x 28250 mm", „Berechnungsgrundlage: DIN 15018, H2/B3", „Gesamtgewicht: 29960 kg", „Kranfahren: 10/40 m/min", „Betriebsort: Hallenbetrieb", Kranklassifizierung S2. Lageplan zeigt zwei Kranbahnen (Pos. 1 und Pos. 2) jeweils mit ZLK 40t/10t im Tandembetrieb (Master-Master). Hallenschnitt A-A: Spannweite 28250 mm, HG=12340, HS=10390.
- **Bestätigt durch:**
  - [`19-02_2019-11-29_Anlage 1_optional 20t Kräne.pdf`](https://drive.google.com/file/d/1fzQKc5KHR4gOwvvONq1KvQziI1APBise/view): Grundriss mit handschriftlichen roten Anmerkungen „optional 20t Kran" für zwei zusätzliche 20t-Kranbahnen, datiert 29.11.19.
  - [`Angebot Nr. 28273.20.pdf`](https://drive.google.com/file/d/1xOrxWOP3l7bGOPqn_c_VkiAhz_IhHJEB/view) (Kranzuschlag): „Kosten für den Einsatz der Spezialkrane", „max. 2 Kranbahnen mit einer max. gesamten Traglast von je 50 to (40 to + 10to)", „Zusätzlich sind die Lasten aus 2 optionalen Kranbahnen mit einer max. Traglast von je 20 to" — Kranzuschlag gesamt €95.715,80 netto.
  - [`Angebot Nr. 28273.11.pdf`](https://drive.google.com/file/d/1tRHIiPoYpxH_R9JMNAGA_-afIW2AjA1U/view) Vorbemerkungen: „wir gehen von max. 2 Kranbahnen mit einer max. gesamten Traglast von je 50 to (40 to + 10to) aus."
- **Hinweis:** Krandaten außergewöhnlich umfassend dokumentiert mit vollständigen ABUS-Spezifikationen inkl. Radlasten, Seitenlasten und dynamischen Beiwerten nach DIN EN 1991-3. Kranbahnstützen mit Erweiterungsoption in Achsen 4-10/B (Angebot 28273.11) bzw. 5-10/B (Angebot 28273.30).

### Dachlasten ✅
- **Wert:**
  - Dachlast = 0,45 kN/m² (Trapezblech, Dämmung, Abdichtung)
  - Installation = 0,15 kN/m²
  - PV = 0,20 kN/m²
  - Wind: Windlastzone WZ2 (WLZ2)
  - Schnee: Schneelastzone 2 (SLZ2)
  - Dachtrapezbleche als 3- bzw. 4-Feldträger, Durchlauffaktor = 1,10
- **Quelle:** [`Angebot Nr. 28273.30.pdf`](https://drive.google.com/file/d/1RLlrQUZskZeOg9uXbguj8IUN_chiQYzV/view), Seite 4 (Pos. 1.1.1.1.01 Spannbeton-Satteldachbinder)
- **Textstelle:** Unter „Lasten:" listet das Angebot explizit: „Dachlast = 0,45 kN/m² (Trapezbl., Dä., Abkl.)", „Installation = 0,15 kN/m²", „PV = 0,20 kN/m²", „Wind: WLZ2", „Schnee: SLZ2"
- **Bestätigt durch:**
  - Angebot 28273.30 Vorbemerkungen (Seite 1): „Wind- u. Schneelasten gemäß DIN EN 1991: Windlastzone WZ2, Schneelastzone 2"
  - [`Angebot Nr. 28273.0.pdf`](https://drive.google.com/file/d/1ilaXJHgAi8NRNmqmLSD2ioWL0OtaMxii/view) (Richtpreis-Angebot, Aug 2019), Pos. 1.01: identische Lastwerte (Dachlast 0,45; Installation 0,15; PV 0,20; WLZ2; SLZ2)
  - Angebot 28273.11 Vorbemerkungen: gleiche Wind-/Schneezonen bestätigt
  - Bauantragsplanung BA-1000d Schnittdarstellung: „Dachaufbau Werkhalle: Foliendachabdichtung, Dämmung, Dampfsperre kalt selbstklebend, Trapezblech"
- **Hinweis:** Alle drei Angebotsversionen (28273.0, 28273.11, 28273.30) nennen identische Dachlastwerte. PV-Last 0,20 kN/m² explizit enthalten. Erdbebenzone: „Der Bauort befindet sich nicht in einer Erdbebenzone." Sohlbelastung Lagerflächen = 50,0 kN/m² (Boden, nicht Dach).

### Baustoff ✅
- **Wert:** Spannbeton-Satteldachbinder (l=30,00 m); Stahlbeton-Fertigteile; Betonstahl B500(A); Spannstahl ST1660/1860 nach DIN 488; Beton nach DIN EN 206-1/DIN 1045-2 mit Portlandzement CEM I; Druckfestigkeitsklasse/Betongüte gemäß statischer Erfordernis
- **Quelle:** [`Angebot Nr. 28273.30.pdf`](https://drive.google.com/file/d/1RLlrQUZskZeOg9uXbguj8IUN_chiQYzV/view), Vorbemerkungen Seite 2 (Abschnitte Beton, Bewehrung)
- **Textstelle:**
  - Beton: „Die Ausführung der Betonarbeiten erfolgt nach DIN EN 206-1/ DIN 1045-2 unter Verwendung von Portlandzement CEM I. Druckfestigkeitsklasse/Betongüte gemäß statischer Erfordernis."
  - Bewehrung: „Betonstahl B500(A), Spannstahl ST1660/1860 nach DIN 488."
  - Pos. 1.1.1.1.01: „Spannbeton-Satteldachbinder, l = 30,00 m" mit „Binderhöhe: 1,45 m im First"
  - Pos. 1.1.2.09: „Spannbeton-Hohlkörperdecken EG und OG, d = 20 cm"
  - Feuerwiderstandsklassen: Binder/Riegel/Abfangträger F90; Stützen/Unterzüge/Deckenplatten F90; Brandwandplatten F90
- **Bestätigt durch:** Angebote 28273.0 und 28273.11 enthalten identische Beton- und Bewehrungsspezifikationen. Das Anschreiben aller Versionen beschreibt „Herstellung, Lieferung und Montage von Stb.-Fertigteilen".
- **Hinweis:** Betongüte als „gemäß statischer Erfordernis" angegeben, nicht als konkrete Klasse wie C50/60. Der Spannstahl ST1660/1860 bestätigt, dass die Dachbinder aus Spannbeton gefertigt sind. Briefkopf identifiziert REKERS Betonwerk GmbH & Co. KG als Fertigteilhersteller.

### Dachbegrünung ❌
- **Wert:** Nicht gefunden
- **Quelle:** Alle Angebotsversionen (28273.0, 28273.11, 28273.20, 28273.30), Bauantragsplanung (E-1000, BA-1000d, BA-3000d) und Anlage 1 durchsucht. Keine Erwähnung von Dachbegrünung, Gründach, extensiver/intensiver Begrünung oder zusätzlichen Dachlasten über Dachlast/Installation/PV/Wind/Schnee hinaus.
- **Hinweis:** Dachaufbau laut BA-1000d: „Foliendachabdichtung, Dämmung, Dampfsperre kalt selbstklebend, Trapezblech" — standardmäßiges Industrieflachdach ohne Vegetationsschicht. Die Dachlastenaufstellung im Angebot ist vollständig (0,45 + 0,15 + 0,20 = 0,80 kN/m² Eigenlast plus Klimalasten) und enthält keine Gründachbelastung. Konsistent mit dem industriellen Gebäudetyp Werkhalle.

---

## Beobachtungen

Dieses Projekt verfügt über außergewöhnlich umfangreiche technische Dokumentation über alle Dateiebenen. Das Angebot entwickelte sich über vier Versionen (28273.0 Richtpreis Aug 2019 → 28273.11 Feb 2020 → 28273.12 Nachtrag Mai 2020 → 28273.30 Konsolidierung Jun 2020), jeweils mit konsistenten Strukturparametern. Die ABUS-Krandatenblätter sind ungewöhnlich detailliert mit vollständigen Lastspezifikationen nach DIN EN 1991-3 inkl. dynamischer Beiwerte — untypisch für normale Projektordner und von hohem ingenieurtechnischem Wert.

Der Bauort änderte sich zwischen den Angebotsversionen: das Richtpreisangebot (28273.0) referenziert „46342 Velen", alle späteren Versionen referenzieren „48712 Gescher" (Marie-Curie-Straße 20). Die Anfragen.csv erfasst den endgültigen Standort Gescher.

Die Kranbahnanlage ist ein wesentlicher Kostenposten (€95.715,80 netto Zuschlag im Angebot 28273.20) zusätzlich zur Hallenkonstruktion (€228.544,60 im Angebot 28273.30) — ca. 42% der Hallenstrukturkosten. Die optionalen 20t-Kranbahnen erfordern zusätzliche Stützenauslegung mit Erweiterungsvorbereitungen in den Kranbahnstützen.

Dachbegrünung ist definitiv nicht vorhanden — die standardmäßige Industriedachspezifikation mit Trapezblech und Folienabdichtung schließt eine Vegetationsschicht aus, bestätigt durch vollständige Abwesenheit entsprechender Lastwerte in allen Dokumenten.

---

## Quelle

- **Datenverzeichnis:** `~/Documents/projects/billable/REKERS__poc/data/2026-01-28_testdaten/35764/`
- **Design-Doc:** [Feasibility Report, Teil 2](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/design-doc-feasibility-report)
- **Session:** b64012b8-aa19-47bc-87a3-866af679580a

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

