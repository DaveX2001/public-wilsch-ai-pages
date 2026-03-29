---
publish: true
---

# Kriteriennachweis — Projekt 39381: VGP Park Magdeburg - Halle D
[[client-rekers]]

Nachweis für Design-Doc Teil 2. Datenverfügbarkeit pro Kriterium mit Quelldateien und extrahierten Werten.

Projekt: 39381 | Datum: 2022-09-14 | Wert: €11.829K

---

## Untersuchte Daten

| Ebene | Anzahl | Details |
|-------|--------|---------|
| L0: Ordnerdateien | 50 | 49 EML, 1 PDF |
| L2: EML-Anhänge | 96 | 60 PDF, 8 XLSX, 2 ZIP, 2 EML, 24 sonstige (PNG/GIF/JPG) |
| _extracted (ZIP) | 20 | 10 DOC/DOCX, 6 XLSX, 3 PDF, 1 sonstige |

Multiplikator: 3,3x (L0 vs. L0+L2+ZIP)

---

## Übersicht

| Kriterium | Status | Wert | Quellebene |
|-----------|--------|------|------------|
| Bauort | ✅ CSV | Sülzetal, 39171, D | L1 |
| Kundenreferenz | ✅ CSV | 103166 | L1 |
| Kalkulatorenwissen | ✅ CSV | 1014 | L1 |
| Gebäudetyp | ✅ | Gewerbepark — Logistik-/Lagerhalle mit Büro- und Sozialflächen | L2 |
| Höhe | ✅ | 14 m (Standard-Firsthöhe); freie Hallenhöhe 11,80 m; Teilbereich 20 m | L2 |
| Kran | ✅ | Ja — 20 t Kran im Teilbereich 20 m Höhe | L2 |
| Dachlasten | ✅ | PV 0,20; Trapezblech+PIR+Abdichtung 0,35; Installation 0,15 kN/m² | L2 |
| Baustoff | ✅ | Stahlbeton-Fertigteile; FT-Paralleldachbinder; Expositionsklasse XC3 | L2 |
| Dachbegrünung | ❌ | Nicht gefunden | — |

**8/9 nachgewiesen** (3 strukturiert + 5 extrahiert). 1 nicht vorhanden.

---

## Strukturierte Kriterien (Anfragen.csv)

### Bauort
- **Wert:** Sülzetal, 39171, D
- **Quelle:** Anfragen.csv, Zeile 2 → `BAUVORHABEN_ORT`, `PLZ`, `NATION`
- **Bestätigt durch:** Brandschutzkonzept Stöber (29.07.2022): „Industriegebiet Osterweddingen, 39171 Sülzetal"

### Kundenreferenz
- **Wert:** 103166
- **Quelle:** Anfragen.csv, Zeile 2 → `KUNDE`

### Kalkulatorenwissen
- **Wert:** 1014
- **Quelle:** Anfragen.csv, Zeile 2 → `KALKULATOR`

---

## Extrahierte Kriterien (Projektdateien)

### Gebäudetyp ✅
- **Wert:** Gewerbepark — Logistik-/Lagerhalle (6 Hallenabschnitte) mit Büroanbau, Mezzanine und Sozialflächen
- **Quelle:** [`220729_Stöber_BSK_GERMAG-D.pdf`](https://drive.google.com/file/d/1fbTzd7ctafho_3_drA4RZTpwBI53Nuy7/view) (Brandschutzkonzept, Seite 5)
- **Textstelle:** „Neubau eines Gewerbeparks mit Büro- und Sozialflächen". Sonderbau nach § 50 BauO LSA: Nr. 3 (Gebäude >1.600 m² Grundfläche) und Nr. 18 (Regallager mit OK Lagergut >7,50 m).
- **Bestätigt durch:**
  - [`221128_Erweiterte Beschreibung II (Ergänzung zu Gießen) für VGP_Rhenus_MD kommentiert VGP.pdf`](https://github.com/MariusWilsch/REKERS__poc/blob/main/data/2026-01-28_testdaten/39381/15214/_extracted/221128_Erweiterte%20Beschreibung%20II%20%20(Erg%C3%A4nzung%20zu%20Gie%C3%9Fen)%20f%C3%BCr%20VGP_Rhenus_MD%20kommentiert%20VGP.pdf): 6 Hallenabschnitte (Halle 1–6), Büroanbau Verwaltung an Halle 2, Mezzanine 16 m in Hallen 3+4 (Ausbau) und Hallen 1+2 (Lagermezzanine), Hallen 5+6 ohne Mezzanine. WGK 3 Löschwasserrückhaltung in Hallen 1–4.
  - [`k5_VGM_A_HD_GR_100_00_A.pdf`](https://drive.google.com/file/d/1kTcTALxJ8KxP2NBq1GIzdhrJ2YBCCkpN/view) (Bauantrag-Grundriss EG/OG): 3 Units mit Erdgeschoss und Obergeschoss dargestellt, Büroräume, Sozialräume, Schulungsräume, WC-Anlagen.
- **Hinweis:** Die Bezeichnung „Gewerbepark" stammt aus dem Brandschutzkonzept, „Halle D" aus der Projektbezeichnung im VHP. Rhenus ist der Mieter (Logistikdienstleister). Hauptnutzung: Logistik/Lagerung mit integrierter Verwaltung und Sozialinfrastruktur.

### Höhe ✅
- **Wert:** Standard-Firsthöhe ca. 14 m; freie Hallenhöhe 11,80 m (unter Installationen/Konstruktion); in Tiefpunktachsen 11,63 m; Teilbereich mit 20 m Höhe für 5.000 m²
- **Quelle:** EML [`GERMAG_D_Anfrage Teilbereich Gebäudehöhe 20 m.eml`](https://drive.google.com/file/d/1f7tyBf2JMcFDSHOqS2uGSsjbopQko8wJ/view) (21.10.2022, Dirk Dingeldey, VGP Industriebau)
- **Textstelle:** „ich benötige eine grobe Mehrkostenindikation für die Halle D in Magdeburg für einen Teilbereich in 20 m Höhe anstatt 14m. Der Bereich soll 5.000 m² abbilden."
- **Bestätigt durch:**
  - [`221128_Erweiterte Beschreibung II...pdf`](https://github.com/MariusWilsch/REKERS__poc/blob/main/data/2026-01-28_testdaten/39381/15214/_extracted/221128_Erweiterte%20Beschreibung%20II%20%20(Erg%C3%A4nzung%20zu%20Gie%C3%9Fen)%20f%C3%BCr%20VGP_Rhenus_MD%20kommentiert%20VGP.pdf): „Die freie Hallenhöhe beträgt 11,80 m unter Installationen (z. B. Lichtband etc.) und der Konstruktion. Im Bereich der Tiefpunktachsen beträgt die freie Höhe UK Konstruktion 11,63 m in Längsrichtung der Regale." Querschnittszeichnung zeigt Lichtbänder UK 11,80 m, OK Dachband ca. +12,80 m.
  - [`k5_VGM_A_HD_SC_250_00.pdf`](https://drive.google.com/file/d/1BJPAnFb5_-iSp8-N6zimmVwrfy1BQmyj/view) (Bauantrag-Schnitte 1:250): Schnitt A-A und Schnitt B-B mit OKFF = ±0,00 m = 84,35 m üNN. Hallenstützenhöhen und Binderhöhen ablesbar.
  - [`k5_VGM_A_HD_AN_250_00.pdf`](https://drive.google.com/file/d/1Wf9GENV4GwvAhXoiLn4zyzvk19a5pDyo/view) (Bauantrag-Ansichten 1:250): Alle vier Ansichten (West Achse 1, Ost Achse 29, Nord Achse A, Süd Achse M) zeigen konsistente Dachlinie und Fassadengestaltung.
- **Hinweis:** Die Standard-Hallenhöhe von 14 m (Firsthöhe) wird aus der EML-Anfrage abgeleitet, die 20 m als „anstatt 14m" beschreibt. Die freie Hallenhöhe (11,80 m) stammt aus der Erweiterten Beschreibung und ist die nutzbare Höhe unter Installationen. Der Teilbereich mit 20 m Höhe für 5.000 m² mit Kranausstattung stellt eine Sonderanforderung dar.

### Kran ✅
- **Wert:** Ja — 20 t Kran im Teilbereich 20 m Höhe (5.000 m² Fläche)
- **Quelle:** EML [`GERMAG_D_Anfrage Teilbereich Gebäudehöhe 20 m.eml`](https://drive.google.com/file/d/1f7tyBf2JMcFDSHOqS2uGSsjbopQko8wJ/view) (21.10.2022)
- **Textstelle:** „Dieser Bereich sollte so ausgelegt werden, dass dort ein Kran mit 20 to. fahren kann."
- **Bestätigt durch:** Separate EML [`VGP Park Magdeburg_Krananlage.eml`](https://drive.google.com/file/d/1bDTbGwITxwAhEAiDzEMpBE1v4SaZxJr5/view) (27.10.2022, Phillip Roßbiegalle an Werner Adick) — spezifisches Krananlage-Thema, Inhalt nicht vollständig extrahiert (große Datei mit Anhängen).
- **Hinweis:** Die Krananfrage bezieht sich auf einen Teilbereich der Halle D. Im Standardbereich (11,80 m freie Höhe) sind keine Kranbahnen dokumentiert — die Hallen sind als Gabelstaplerbetrieb konzipiert (Hallensohle Stiellasten min. 12,6 T, Lastverteilplatte 196 cm²). Der 20-t-Kran ist eine projektspezifische Sonderanforderung.

### Dachlasten ✅
- **Wert:**
  - Photovoltaikanlage = 0,20 kN/m² (Aufbauhöhe h < 0,35 m)
  - Trapezblech, 100 mm PIR-Dämmung, Abdichtung = 0,35 kN/m²
  - Installation = 0,15 kN/m²
- **Quelle:** [`Sammelnachtrag 1- Betonfertigteile- VGP Park Magdeburg Halle D- Rekers-Endfassung-Endfassung.pdf`](https://drive.google.com/file/d/1tbYID34bQSg1o8ZM-_H9u_3PvLMnWIWI/view), Vorbemerkungen Angebot Nr. 39381.12 (Vordachkonstruktion)
- **Textstelle:** „Dachbelastung: - Photovoltaikanlage = 0,20 kN/m² (Aufbauhöhe h < 0,35 m) - Trapezblech, 100 mm PIR-Dämmung, Abdichtung = 0,35 kN/m² - Installation = 0,15 kN/m²"
- **Bestätigt durch:** Gleiche Vorbemerkungen referenzieren „Hauptangebotes Nr. 39381.4 vom 02.11.2022", dessen Bedingungen gelten. Nutzlast Ortbetonsohle unter Vordach = 20 kN/m². Dachneigung = 2%.
- **Hinweis:** Die Dachlastwerte stammen aus dem Ergänzungsangebot für die Vordachkonstruktion (39381.12), die explizit auf die gleichen Vorbemerkungen wie das Hauptangebot 39381.4 verweist. Windlastzone und Schneelastzone sind in den verfügbaren Dokumenten nicht explizit als WLZ/SLZ-Kennzeichnung angegeben. PV-Last 0,20 kN/m² mit Aufbauhöhenbegrenzung <0,35 m spezifiziert.

### Baustoff ✅
- **Wert:** Stahlbeton-Fertigteile (Stb.-FT); FT-Paralleldachbinder (Systemachslänge l ca. 12,00 m, Höhe ca. 1,10 m, Gefälle 2%); FT-Dachrandträger (L = 6,00 m); Expositionsklasse XC3; Stb.-FT-Sandwichaussenwand (Normalbeton, Dämmung EPS WLG 035)
- **Quelle:** [`Sammelnachtrag 1- Betonfertigteile- VGP Park Magdeburg Halle D- Rekers-Endfassung-Endfassung.pdf`](https://drive.google.com/file/d/1tbYID34bQSg1o8ZM-_H9u_3PvLMnWIWI/view), Angebot Nr. 39381.12 Positionen + Angebot Nr. 39381.13 Positionen
- **Textstelle:**
  - Pos. 1.01 (39381.12): „FT-Paralleldachbinder, Systemachslänge l ca. = 12,00 m, Querschnitt gemäß Statik, Höhe ca. 1,10 m, im Gefälle mit 2 % verlegt, Expositionsklasse XC3"
  - Pos. 1.03 (39381.12): „FT-Dachrandträger, Querschnitt gemäß Statik, Höhe ca. 0,40 m, Systemachslänge L = 6,00 m, Parallelgurtig im Gefälle verlegt"
  - Pos. 1.1 (39381.13): „Stb.-FT-Sandwichaussenwand, Tragschale gemäß Statik, Oberfläche abgerieben und geglättet. Dämmung d = 8 cm, EPS WLG 035. Vorsatzschale d = 8 cm, Oberfläche schalungsglatt, Normalbeton in grau."
- **Bestätigt durch:**
  - [`CA_GERMAG-D_Rekers_20221130_2.pdf`](https://drive.google.com/file/d/12gi_swEMpksvyHAMj-YSFEqzTHObtOWo/view) (Auftrag): „Gewerk: Betonfertigteile für unsere Halle D im VGP Park Magdeburg"
  - [`VHP BFT- VGP Park Magdeburg Halle D- Rekers-END-END.pdf`](https://drive.google.com/file/d/1t13o4DJs4xKN3DiNsQ2uJ5DP4s0tfaTv/view), § 3.2: Preisgleitung für „Betonstahl, Spannstahl & Kraftstoffpreise" vereinbart — Spannstahl wird als relevante Materialgruppe erwähnt.
- **Hinweis:** Keine explizite Betongüte (z.B. C50/60) in den verfügbaren Dokumenten angegeben; die Expositionsklasse XC3 und „gemäß Statik" deuten auf projektspezifische Bemessung hin. Spannstahl wird im VHP als Preisgleitungs-Materialgruppe genannt, was auf Spannbeton-Elemente im Hauptangebot hindeutet, auch wenn die Ergänzungsangebote nur Stahlbeton-Fertigteile explizit beschreiben.

### Dachbegrünung ❌
- **Wert:** Nicht gefunden
- **Quelle:** Alle verfügbaren Angebote (39381.12, 39381.13, 39381.15), VHP (inkl. DGNB-Abschnitt §23), Erweiterte Beschreibung, Brandschutzkonzept und Bauantragsplanung durchsucht. Keine Erwähnung von Dachbegrünung, Gründach, extensiver/intensiver Begrünung oder entsprechenden Substratlasten.
- **Hinweis:** DGNB Gold ist als Zertifizierungsziel vertraglich vereinbart (VHP §23, Marktversion 2018), was theoretisch Dachbegrünung einschließen könnte. Jedoch enthält weder die Erweiterte Beschreibung noch die Dachlastenaufstellung (0,35 + 0,15 + 0,20 = 0,70 kN/m² Eigenlast plus Klimalasten) eine Vegetationsschicht. Die Dachkonstruktion basiert auf Trapezblech mit PIR-Dämmung und Abdichtung — ein Standard-Industriedachaufbau ohne Gründachkomponente.

---

## Beobachtungen

Dieses Projekt ist ein Großprojekt mit einem Pauschalvertragswert von 5.720.000 EUR netto (nach 8,12% Nachlass) für die Betonfertigteile. Die Vertragskonstellation ist bemerkenswert: Auftraggeber ist VGP Park Magdeburg S.à.r.l. (Luxemburg), VGP Industriebau GmbH (Düsseldorf) agiert als Projektleitung, Rhenus ist der Endmieter des Logistikstandorts.

Die Erweiterte Beschreibung basiert auf dem Projekt „Gießen" als Referenzbauvorhaben, was auf eine VGP-Standardisierung über Projekte hinweg hindeutet. Für das Angebotssystem relevant: Ähnliche VGP-Projekte dürften vergleichbare technische Spezifikationen aufweisen.

Eine Besonderheit ist die Anfrage für einen Teilbereich mit 20 m Gebäudehöhe (statt Standard 14 m) für 5.000 m² mit 20-t-Kranausstattung. Diese Sonderanforderung zeigt die Variabilität innerhalb eines Projekts — der Standardbereich hat keine Kranbahnen (Gabelstaplerbetrieb), während der erhöhte Bereich eine Krananlage erfordert.

Das Hauptangebot Nr. 39381.4 vom 02.11.2022 ist nicht als separate Datei im Datenbestand vorhanden — die technischen Spezifikationen wurden aus den Ergänzungsangeboten (39381.12, 39381.13, 39381.15) rekonstruiert, die explizit auf die Vorbemerkungen des Hauptangebots verweisen. Wind- und Schneelastzonen (WLZ/SLZ) sind in den verfügbaren Dokumenten nicht explizit als Kennzeichnung angegeben.

---

## Quelle

- **Datenverzeichnis:** `~/Documents/projects/billable/REKERS__poc/data/2026-01-28_testdaten/39381/`
- **Design-Doc:** [Feasibility Report, Teil 2](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/design-doc-feasibility-report)
- **Session:** b11ae83f-ff41-45ec-8d16-58a82f2b45a8

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

