---
publish: true
---

# Kriteriennachweis — Projekt 40138: Hillwood Mönchengladbach
[[client-rekers]]

Nachweis für Design-Doc Teil 2. Datenverfügbarkeit pro Kriterium mit Quelldateien und extrahierten Werten.

Projekt: 40138 | Datum: 2023-06-16 | Wert: €1.668K

---

## Untersuchte Daten

| Ebene | Anzahl | Details |
|-------|--------|---------|
| L0: Ordnerdateien | 6 | 4 EML, 2 ZIP (Architektur, Brandschutz) |
| L2: EML-Anhänge | 80 | 42 PDF, 4 XLSX, 18 JPG/PNG, 3 ZIP, 13 sonstige |
| _extracted (ZIP) | 31 | 23 PDF (Tragwerksplanung, Brandschutz, Architektur), 8 sonstige |

Multiplikator: 18,5x (L0 vs. L0+L2+ZIP)

---

## Übersicht

| Kriterium | Status | Wert | Quellebene |
|-----------|--------|------|------------|
| Bauort | ✅ CSV | Mönchengladbach, 41199, D | L1 |
| Kundenreferenz | ✅ CSV | 335 | L1 |
| Kalkulatorenwissen | ✅ CSV | 917 | L1 |
| Gebäudetyp | ✅ | Logistikhalle mit 2 Units (U1: 7.440 m², U2: 7.495 m²), Mezzanin + Büro | L2 |
| Höhe | ✅ | OK Attika 12,50 m; UK Träger 10,50 m; First +12,88 m | L2 |
| Kran | ❌ | Nicht gefunden — Logistikhalle (Gabelstaplerbetrieb) | — |
| Dachlasten | ❌ | Nicht gefunden — Dachaufbau bekannt, keine kN/m²-Werte oder WLZ/SLZ | — |
| Baustoff | ✅ | Stb.-Fertigteile C45/55; BSt 500; Holz-Fischbauchbinder (Dach) | L2 |
| Dachbegrünung | ❌ | Nicht gefunden — Foliendach auf Trapezblech, DGNB Gold angestrebt | — |

**6/9 nachgewiesen** (3 strukturiert + 3 extrahiert). 3 nicht vorhanden.

---

## Strukturierte Kriterien (Anfragen.csv)

### Bauort
- **Wert:** Mönchengladbach, 41199, D
- **Quelle:** Anfragen.csv, Zeile → `BAUVORHABEN_ORT`, `PLZ`, `NATION`

### Kundenreferenz
- **Wert:** 335
- **Quelle:** Anfragen.csv, Zeile → `KUNDE`

### Kalkulatorenwissen
- **Wert:** 917
- **Quelle:** Anfragen.csv, Zeile → `KALKULATOR`

---

## Extrahierte Kriterien (Projektdateien)

### Gebäudetyp ✅
- **Wert:** Logistikhalle mit 2 Units (Unit 1: ca. 7.440 m², Unit 2: ca. 7.495 m², gesamt ca. 14.935 m²), je eine Mezzanin-Ebene ca. 1.152 m² mit ausgebauten Büroeinheiten (je ca. 288 m²)
- **Quelle:** [`230530_23-0213-1-52_lko_54221-LV Konstruktive-Fertigteile.pdf`](https://drive.google.com/file/d/1p6Okaxe5UK9nyshP29lZ56Os2HeoF467/view), Seite 4–5 (Baubeschreibung)
- **Textstelle:** „Logistikhalle Hillwood Mönchengladbach befindet sich in Mönchengladbach an der Oppelner Straße. Halle 1 ist in Unit 1 (ca. 7.440 m²) und in Unit 2 (ca. 7.495 m²) unterteilt. In jeder Unit befindet sich eine Mezzanin ca. 1.152 m² davon mit ausgebauten Büroeinheiten (ca. 2 x 288 m2)."
- **Bestätigt durch:** [`4_1_AR_GR_0_X_0101_00_-_Grundriss Halle.pdf`](https://drive.google.com/file/d/1YTiQFkANuw5I_WsDNk7H3dnB8vgpc5x7/view) (Bauantrag M 1:200): Grundriss zeigt Unit 1 (7.440,86 m²) und Unit 2 (7.493,53 m²) mit Gebäudeabmessungen ca. 157 m × 97 m, 27 Längsachsen, 2% Dachgefälle, Ladebrücken an Achse A mit 16 Toröffnungen. Bauherr: HE4 Lux Enterprises Rho S.à r.l. (Luxemburg).
- **Hinweis:** Hillwood ist ein internationaler Logistikimmobilien-Entwickler. Das Projekt ist eine spekulative Logistikhalle (built-to-suit möglich) mit General-Unternehmer Köster GmbH. Die Brandwand (F90) in Achse 14 teilt die Halle in 2 Units. DGNB Gold-Zertifizierung angestrebt (lt. LV Ausschreibung).

### Höhe ✅
- **Wert:** OK Attika 12,50 m; UK Träger 10,50 m; First +12,88 m (Schnitt A); OK Stütze +12,05 bis 12,29 m; Mezzanin +6,06 m
- **Quelle:** [`4_2_AR_SN_X_X_0201_00_-_Schnitte.pdf`](https://drive.google.com/file/d/132FZdbyBK70kNl2TiLGvz2rT4FvmEmX0/view) (Bauantrag, Schnitte M 1:200)
- **Textstelle:** Schnitt A zeigt First bei +12,88 m, Traufe bei +12,25 m und +10,85/+10,60 m, UK Lichtband +7,22 m, OK Lichtband +9,02 m. Mezzanin-Ebene auf +6,06 m. Dachaufbau: Dachabdichtung 1,5 mm, Dämmung PIR 100 mm, Dampfsperre 4 mm, Trapezblech 200 mm. Schnitt B zeigt OK BFT +2,50 m (Sockelbereich) und +12,20 m im Dachbereich.
- **Bestätigt durch:**
  - [`4_1_AR_AN_0_X_0300_00_-_Ansichten.pdf`](https://drive.google.com/file/d/1eYAmUlnuJ5rjlscXU1WZAWq25nryTlxJ/view) (Bauantrag, Ansichten M 1:200): Gebäudehöhe +12,50 m, Isowand-Fassade bis +6,00 m sichtbar.
  - [`230530_23-0213-1-52_lko_54221-LV Konstruktive-Fertigteile.pdf`](https://drive.google.com/file/d/1p6Okaxe5UK9nyshP29lZ56Os2HeoF467/view), Seite 4: „UK Träger liegt bei 10,50m. OK Attika 12,50 m."
  - [`Freibleiendes Angebot 40138.0.pdf`](https://drive.google.com/file/d/1nngfVtIQNvCXbwATtHsq9fmT_YUU35pt/view), Pos. 01.01.01: Giebelstütze H=12,67 m, OK Stütze =+12,05 bis 12,29 m; Traufstütze H=12,55 m; Innenstütze H=12,55 m.
- **Hinweis:** Satteldach-Fischbauchbinder als Dachtragwerk (Holz oder Spannbeton) ergibt die Differenz zwischen Traufhöhe (+10,50/+10,85 m) und Firsthöhe (+12,88 m). Die Stützenhöhen im REKERS-Angebot beziehen sich auf OK-Stütze bis OK-Fundament, bestätigen die Gesamthöhe konsistent.

### Kran ❌
- **Wert:** Nicht gefunden
- **Quelle:** Alle Angebotsversionen (40138.0 freibleibend, 40138.1 pauschal), LV Ausschreibung, Architekturpläne (Grundriss, Schnitte, Ansichten) und Tragwerksplanungspläne durchsucht. Keine Erwähnung von Laufkran, Kranbahn, Krandatenblätter oder Kranlasten.
- **Hinweis:** Logistikhallen sind typischerweise Gabelstaplerbetrieb ohne eingebaute Krananlage. Der im Auslieferungsterminplan (83809-UT01a) erwähnte „160 to Gitter-Raupe HM 24,3 / Nadel 24,3" ist der REKERS-Montagekran für die Fertigteilmontage, kein Gebäudekran. Die Stützen haben „Gabellagerung für die Aufnahme von Holzbinder" — keine Kranbahnkonsolen.

### Dachlasten ❌
- **Wert:** Nicht gefunden (Dachaufbau bekannt, keine quantitativen Lastwerte)
- **Quelle:** Alle Angebotsversionen (40138.0, 40138.1), LV Ausschreibung und Architekturpläne durchsucht. Kein Wind-/Schneelastabschnitt in den REKERS-Vorbemerkungen (anders als bei Projekten mit REKERS-Spannbetonbindern).
- **Hinweis:** Der Dachaufbau ist aus den Architektur-Schnitten bekannt: Dachabdichtung 1,5 mm + Dämmung PIR 100 mm + Dampfsperre 4 mm + Trapezblech 200 mm (Foliendach). Das Dachtragwerk wird in Holz-Fischbauchbinder realisiert und ist nicht Teil des REKERS-Leistungsumfangs. Daher enthält das REKERS-Angebot keine expliziten Dachlasten (kN/m²), Wind- oder Schneelastzonen — diese wären in der separaten Holzbinder-Ausschreibung bzw. Tragwerksplanung zu finden, die nicht im Projektordner vorliegt.

### Baustoff ✅
- **Wert:** Stahlbeton-Fertigteile C45/55 (Zulage von C35/45 XC1); Betonstahl BSt 500 S und BSt 500 M; Dachtragwerk: Holz-Fischbauchbinder bzw. Spannbetonbinder
- **Quelle:** [`Freibleiendes Angebot 40138.0.pdf`](https://drive.google.com/file/d/1nngfVtIQNvCXbwATtHsq9fmT_YUU35pt/view), Seite 3–7 (Positionen Stützen und Bewehrung)
- **Textstelle:**
  - Pos. 01.01.01.20: „Zulage Stütze in C45/55 — anstatt C 35/45XC1 — kein Mehr- bzw. Minderpreis" (alle Stützentypen)
  - Pos. 01.01.02.10: „BST 500 S — Betonstabstahl BSt 500 für Köcherfundamente" (146,16 to)
  - Pos. 01.01.02.20: „BST 500 M — Betonstahlmatten BSt 500 für Köcherfundamente" (2,51 to)
  - Alle Stützen mit „Gabellagerung für die Aufnahme von Holzbinder"
- **Bestätigt durch:**
  - [`230530_23-0213-1-52_lko_54221-LV Konstruktive-Fertigteile.pdf`](https://drive.google.com/file/d/1p6Okaxe5UK9nyshP29lZ56Os2HeoF467/view), Seite 5: „Das Tragwerk der Gebäude besteht aus Stahlbetonstützen mit angeformten Fundamenten. Das Dachtragwerk wird in Holz-Fischbauchbinder bzw. Spannbetonbinder realisiert."
  - LV Seite 6: „Sofern nicht anders erwähnt sind alle Fertigteile in der Mindestbetongüte C 35/45 XC 1 herzustellen und Mindestanforderung an Sichtbeton SB2."
- **Hinweis:** Anders als bei reinen REKERS-Hallenprojekten (z.B. 35764) mit Spannbeton-Satteldachbinder liefert REKERS hier ausschließlich die Stb.-Fertigteile (Stützen, Wandplatten, Sockel, TT-Decken). Das Dachtragwerk wird separat als Holz-Fischbauchbinder ausgeführt. Die C45/55-Zulage ohne Mehr-/Minderpreis deutet darauf hin, dass REKERS standardmäßig in C45/55 fertigt und die formale Zulage nur vertragstechnisch dokumentiert wird.

### Dachbegrünung ❌
- **Wert:** Nicht gefunden
- **Quelle:** Alle Angebotsversionen, LV Ausschreibung, Architekturpläne (Schnitte zeigen Dachaufbau), Brandschutzpläne und VHP-Dokumente durchsucht. Keine Erwähnung von Dachbegrünung, Gründach, extensiver/intensiver Begrünung oder Substratlasten.
- **Hinweis:** Dachaufbau laut Architektur-Schnitten: „Dachabdichtung 1,5 mm, Dämmung PIR 100 mm, Dampfsperre 4 mm, Trapezblech 200 mm" — standardmäßiges Logistik-Industriedach ohne Vegetationsschicht. Trotz DGNB Gold-Zertifizierungsabsicht (lt. LV Ausschreibung) keine Begrünung dokumentiert. Konsistent mit dem Gebäudetyp Logistikhalle.

---

## Beobachtungen

Dieses Projekt unterscheidet sich grundlegend von den typischen REKERS-Hallenprojekten: Das Dachtragwerk wird nicht als REKERS Spannbeton-Satteldachbinder, sondern als Holz-Fischbauchbinder realisiert. REKERS liefert ausschließlich die Stahlbeton-Fertigteile (Stützen, Wände, Sockel, Mezzanin-Decken). Daher fehlen in den REKERS-Unterlagen die sonst üblichen Dachlasten-Spezifikationen (kN/m², Wind-/Schneezonen), die typischerweise im Abschnitt Spannbeton-Satteldachbinder der Vorbemerkungen aufgeführt werden.

Die Vertragsentwicklung zeigt drei Phasen: Freibleibendes Angebot 40138.0 (07.07.2023) auf Einzelpreisbasis → Pauschal-Angebot 40138.1 (14.11.2023, Vertragswert €1.475.000) → Nachtrag Nr. 1 (21.05.2024, AE +€21.000 = €1.496.000). Die Auftragsergänzung betrifft Mehrmengen durch Grundrissanpassung und Statikänderungen.

General-Unternehmer ist Köster GmbH (Gießen), Bauherr ist HE4 Lux Enterprises Rho S.à r.l. (Luxemburg) — eine Investmentgesellschaft von Hillwood. Montageplanung sieht 160-to-Gitter-Raupe vor, Bauzeit Mai–Juli 2024 in 4 Kolonnen (Achse 21→27, 14→21, 7→14, 1→7).

Dachbegrünung und Kran sind definitiv nicht vorhanden — das Gebäude ist eine standardmäßige Logistikhalle mit Gabelstaplerbetrieb, Foliendach auf Trapezblech und DGNB Gold-Anspruch ohne Vegetationsschicht.

---

## Quelle

- **Datenverzeichnis:** `~/Documents/projects/billable/REKERS__poc/data/2026-01-28_testdaten/40138/`
- **Design-Doc:** [Feasibility Report, Teil 2](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/design-doc-feasibility-report)
- **Session:** 49f9bdc4-2547-4c36-80a5-cc03644b5c23

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

