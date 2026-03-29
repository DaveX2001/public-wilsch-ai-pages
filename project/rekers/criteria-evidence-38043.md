---
publish: true
---

# Kriteriennachweis — Projekt 38043: Neubau Nokera Werk Stegelitz
[[client-rekers]]

Nachweis für Design-Doc Teil 2. Datenverfügbarkeit pro Kriterium mit Quelldateien und extrahierten Werten.

Projekt: 38043 | Datum: 2021-06-24 | Wert: €15.272K

---

## Untersuchte Daten

| Ebene | Anzahl | Details |
|-------|--------|---------|
| L0: Ordnerdateien | 104 | 100 EML, 4 sonstige |
| L2: EML-Anhänge | 556 | 345 PDF, 211 sonstige (JPG, PNG, WMZ) |
| _extracted (ZIP) | 38 | 26 PDF, 6 ZIP, 6 sonstige |

Multiplikator: 6,7x (L0 vs. L0+L2+ZIP)

---

## Übersicht

| Kriterium | Status | Wert | Quellebene |
|-----------|--------|------|------------|
| Bauort | ✅ CSV | Möckern, 39291, D | L1 |
| Kundenreferenz | ✅ CSV | 14090 | L1 |
| Kalkulatorenwissen | ✅ CSV | 1028 | L1 |
| Gebäudetyp | ✅ | Fertigteilwerk (4 Produktionshallen + Eingangsgebäude + Kompressorraum) | L2 |
| Höhe | ✅ | 16,25 m (Gebäudehöhe); Traufhöhe 14,50 m; Attika +14,10 m; Eingangshalle +5,60 m | L2 |
| Kran | ✅ | Ja — Kranbahnrahmen in 3 von 4 Produktionshallen | L2 |
| Dachlasten | ✅ | Eingangshalle: Hohldielendecke 3,50 + Abklebung 0,25 + Kiesschüttung 0,80 + Installation 0,20 kN/m²; sk = 0,85 kN/m²; SLZ2; WLZ2 | L2 |
| Baustoff | ✅ | Spannbeton (Spannbinder, Spannabfangeträger); Stb.-Fertigteile; Spannbeton-Hohlkörperdecken d=20cm; Pi-Plattendecken (TT-Decken) | L2 |
| Dachbegrünung | ❌ | Nicht gefunden | — |

**8/9 nachgewiesen** (3 strukturiert + 5 extrahiert). 1 nicht vorhanden.

---

## Strukturierte Kriterien (Anfragen.csv)

### Bauort
- **Wert:** Möckern, 39291, D
- **Quelle:** Anfragen.csv, Zeile 2 → `BAUVORHABEN_ORT`, `PLZ`, `NATION`

### Kundenreferenz
- **Wert:** 14090
- **Quelle:** Anfragen.csv, Zeile 2 → `KUNDE`

### Kalkulatorenwissen
- **Wert:** 1028
- **Quelle:** Anfragen.csv, Zeile 2 → `KALKULATOR`

---

## Extrahierte Kriterien (Projektdateien)

### Gebäudetyp ✅
- **Wert:** Industrielles Fertigteilwerk (Nokera Werk) bestehend aus 4 Produktionshallen (Deckenproduktion, Wandproduktion, Fassadenproduktion, Attikaproduktion), einem 2-geschossigen Eingangsgebäude und einem Kompressorraum
- **Quelle:** [`3387 Nokera Werk Stegelitz-1.Genehmigungsstatik.pdf`](https://drive.google.com/file/d/1Z4WfF2Pas9xkRRD0dY-67zihmixTVRvj/view)
- **Textstelle:** Statikbestandteile listen explizit: „3. Halle Deckenproduktion", „4. Halle Wandproduktion", „5. Halle Fassadenproduktion", „6. Halle Attikaproduktion" — jeweils mit Fünf- bzw. Vier-Feldrahmen, Spannbindern und Rahmenstützen. Angebot 28844.1 für „Neubau Nokera Werk Steglitz".
- **Bestätigt durch:**
  - [`Anlage 4_3D-Modell.pdf`](https://drive.google.com/file/d/1Q6YeyZs4DldOftFGuB_8TAL0QWFf8R0e/view): 3D-BIM-Visualisierung zeigt die Gesamtanlage mit parallel angeordneten Produktionshallen und zentralem Eingangsgebäude.
  - [`Anlage 5_U412 Grundriss.pdf`](https://drive.google.com/file/d/15Nyv06gwam_NISikAE2qu_pRuC4BZjd4/view): Grundriss Eingangsgebäude (Zeichnung U412, Kom. 83640) mit Achsen 9–11/E–F, Hohlkörperdecken und Spann-Binder F106.
  - [`DGNB Anforderungen Gewerk Dachdeckenproduktion.pdf`](https://drive.google.com/file/d/1smijJ_VWOcFs5mR86KhT9kszNfhQ9Tgz/view): Beschreibt das Bauvorhaben als „Neubau nokera green factory Dachdeckenproduktion" mit DGNB-Gold-Zertifizierung. Bauherr: Nokera Grundbesitz 1 AG (Schweiz).
- **Hinweis:** Der Gebäudetyp ist ein spezialisiertes Betonfertigteilwerk für die Produktion von Wand-, Fassaden-, Dach- und Attikaelementen. Die vier Hallen sind funktional getrennt, aber baulich über eine gemeinsame Stützenraster- und Brandwandstruktur verbunden. Gesamtgebäudebreite 100 m (Anlage 2), Länge 40 m. Baugesellschaft: Walter Hellmich GmbH.

### Höhe ✅
- **Wert:** Gebäudehöhe 16,25 m (Gesamthöhe inkl. Dachaufbau); Traufhöhe 14,50 m; Attika Produktionshallen ca. +14,10 m; Eingangsgebäude max. Attikahöhe +5,60 m ü. OK Hallensohle; Kompressorraum OK +9,03 m
- **Quelle:** [`Anlage 2_Schneesack.pdf`](https://drive.google.com/file/d/1BGh4mw6eP2U5-s9id6ELqymProisulHi/view)
- **Textstelle:** „Geometrie Höhensprung": Gebäudehöhe h = 16,25 m, Gebäudebreite b = 100,00 m, Gebäudelänge l = 40,00 m, Traufhöhe ht = 14,50 m, Dachneigung αD = 1,0°, Anbauhöhe h2 = 5,70 m (Eingangshalle), Anbaubreite b2 = 16,00 m, Höhensprung h = 8,80 m.
- **Bestätigt durch:**
  - [`Anlage 1_ Lastannahmen, Stand 14.02.22.pdf`](https://drive.google.com/file/d/1rI9W1hw76MbsN7C6oqgKcCGuUNn2TOlx/view): „max. Höhe der Attika: ca. +5,60 m ü. OK Hallensohle" für die Eingangshalle.
  - [`3387 Nokera Werk Stegelitz-1.Genehmigungsstatik.pdf`](https://drive.google.com/file/d/1Z4WfF2Pas9xkRRD0dY-67zihmixTVRvj/view): Alle vier Produktionshallen mit „Attika ca. +14,10m".
  - [`Anlage 6_U413 Ansichten.pdf`](https://drive.google.com/file/d/1XhFZxqMiGgYm1ogrOXnKjukrL07so7N5/view): Schnitt A-A des Eingangsgebäudes zeigt Spannbinder F106 und Hohlkörperdecke d=20cm. OKFF ± 0,00m = 69,90m ü.NHN.
  - [`1453 - Angebot Nr. 28844.12  N8.pdf`](https://drive.google.com/file/d/1SxZ0ueV-qqlEITmvpz71dR4QQBCBguhE/view): Kompressorraum Innenstützen OK: +8,28 m bzw. +9,03 m; Wand OK: +9,03 m.
- **Hinweis:** Die Produktionshallen sind deutlich höher als das Eingangsgebäude (14,10 m vs. 5,60 m Attika), was den Höhensprung von 8,80 m zwischen den Gebäudeteilen erklärt. Die Gesamtgebäudehöhe von 16,25 m ergibt sich aus der Traufhöhe 14,50 m plus Dachaufbau/Attika. Dachneigung nur 1,0° — nahezu flach.

### Kran ✅
- **Wert:** Ja — Kranbahnrahmen in 3 von 4 Produktionshallen (Halle Deckenproduktion: Achse 33; Halle Wandproduktion: Achse 2; Halle Attikaproduktion: Drei-Feld-Kranbahnrahmen)
- **Quelle:** [`3387 Nokera Werk Stegelitz-1.Genehmigungsstatik.pdf`](https://drive.google.com/file/d/1Z4WfF2Pas9xkRRD0dY-67zihmixTVRvj/view)
- **Textstelle:**
  - Halle Deckenproduktion: „1 Kranbahnrahmen in Achse 33 mit entsprechenden Stützen und Fundamenten"
  - Halle Wandproduktion: „1 Kranbahnrahmen in Achse 2 mit entsprechenden Stützen und Fundamenten"
  - Halle Attikaproduktion: „1 Drei-Feld-Kranbahnrahmen zur Lastverteilung mit Attika ca. +14,10m"
  - Halle Fassadenproduktion: Kein Kranbahnrahmen erwähnt
  - Alle Fünf-Feldrahmen „ohne Berücksichtigung von Kra[nbahnen]" — d.h. die Kranlasten werden separat über dedizierte Kranbahnrahmen abgetragen.
- **Bestätigt durch:** [`Angebot Nokera, 39291 Moeckern.pdf`](https://drive.google.com/file/d/1K-ukF61CUYh1VUkSXiM7OKDguk5ckYu5/view) (H&L Baustoff-Werke, Seite 4): „Der Kran und der Kranstandplatz ist bauseits ausreichend zu dimensionieren und zu befestigen." — bestätigt Kranpräsenz auf der Baustelle.
- **Hinweis:** Krandaten sind weniger detailliert als bei Projekt 35764. Die Genehmigungsstatik benennt Kranbahnrahmen, jedoch keine Krantypen, Traglasten oder Hersteller. Die Produktionshallen benötigen Krane für die Fertigteilproduktion — Decken-, Wand- und Attikaproduktion erfordern schwere Hebevorgänge. Fassadenproduktion offenbar ohne Kranbahn (möglicherweise andere Handhabungsmethode).

### Dachlasten ✅
- **Wert:**
  - **Eingangshalle Dach:** Hohldielendecke h=20cm: g1 ≤ 3,50 kN/m²; Abklebung/Gefälledämmung: g2 ≤ 0,25 kN/m²; Kiesschüttung: g3 = 0,80 kN/m²; PV-Anlage: keine; Installationslasten: 0,20 kN/m² (keine Dachgeräte!)
  - **Schneelast:** Bodenschneelast sk = 0,85 kN/m²; Schneezone 2; Klimaregion Zentral-Ost
  - **Windlast:** Windzone 2; Geländekategorie Mischkategorie Binnenland; Basiswindgeschwindigkeit vb0 = 25,0 m/s; Geschwindigkeitsstaudruck qp,0(h) = 0,59 kN/m²
  - **Schneesack (Höhensprung):** s1 = 0,68 kN/m² (P/T); s2 = 2,04 kN/m² (P/T); Δs2 = 1,36 kN/m²
  - **Produktionshallen:** „normale Dachlasten incl. Fotovoltaik, in Teilbereichen Abhängelasten für Absauganlagen"
- **Quelle:** [`Anlage 1_ Lastannahmen, Stand 14.02.22.pdf`](https://drive.google.com/file/d/1rI9W1hw76MbsN7C6oqgKcCGuUNn2TOlx/view) (Lastannahmen Eingangshalle, Kom. 83640, Stand 09.02.22)
- **Textstelle:** „Lastannahmen Eingangshalle: max. Höhe der Attika ca. +5,60 m ü. OK Hallensohle. Lasten Dach: Hohldielendecke h=20cm g1 ≤ 3,50 KN/m²; Abklebung, Gefälledämmung g2 ≤ 0,25 kN/m²; Kiesschüttung g3 = 0,80 kN/m²; PV-Anlage = keine; Installationslasten = 0,20 kN/m²; Keine Dachgeräte!!"
- **Bestätigt durch:**
  - [`Anlage 2_Schneesack.pdf`](https://drive.google.com/file/d/1BGh4mw6eP2U5-s9id6ELqymProisulHi/view): Bodenschneelast sk = 0,85 kN/m²; Schneezone 2; DIN EN 1991-1-3/NA:2019-04.
  - [`Anlage 3_Wind+Schnee Eingangsgebäude.pdf`](https://drive.google.com/file/d/1U3pfZF2u8WpFSuj5NPSv0aElVwxOx0lK/view): Windzone 2; Mischkategorie Binnenland; qb0 = 0,39 kN/m²; qp,0(h) = 0,59 kN/m².
  - [`Anfrage Nr. 28844.61.pdf`](https://drive.google.com/file/d/1AkHStxlQdVxvuzalNdG5E59zE8WC5MUH/view): REKERS Anfrage Hohlkörperdecken bestätigt identische Lastannahmen: 0,25 Abklebung+Dämmung, 0,80 Kiesschüttung, 0,20 Installation, 0,68 Schnee, 1,36 Schneesack, 0,41 Winddruck.
  - [`3387 Nokera Werk Stegelitz-1.Genehmigungsstatik.pdf`](https://drive.google.com/file/d/1Z4WfF2Pas9xkRRD0dY-67zihmixTVRvj/view): „normale Dachlasten incl. Fotovoltaik, in Teilbereichen Abhängelasten für Absauganlagen" für die Produktionshallen.
- **Hinweis:** Die Lastannahmen für die Eingangshalle sind detailliert dokumentiert, die Produktionshallen werden in der Genehmigungsstatik nur pauschal mit „normale Dachlasten" beschrieben. PV-Anlage explizit nicht auf der Eingangshalle (aber auf den Produktionshallen geplant). Die Schneesacklast am Höhensprung (Δs2 = 1,36 kN/m²) ist die dominierende Schneelast und resultiert aus dem 8,80 m hohen Absatz zwischen Haupt- und Eingangsgebäude.

### Baustoff ✅
- **Wert:** Spannbeton (Spannbinder, Spannabfangeträger) für Hallendachtragwerk; Stahlbeton-Fertigteile (Stb.-Fertigteile) für Stützen, Wandplatten, Pi-Plattendecken; Spannbeton-Hohlkörperdecken (EFD 20/120, d=20cm) für Eingangshalle-Dach; Halbmontage-Pi-Plattendecke (TT-Decken) mit Ortbetonergänzung für Kompressorraum; Expositionsklassen XC1 (innen), XC2 (Gründung), XC4 (außen)
- **Quelle:** [`3387 Nokera Werk Stegelitz-1.Genehmigungsstatik.pdf`](https://drive.google.com/file/d/1Z4WfF2Pas9xkRRD0dY-67zihmixTVRvj/view)
- **Textstelle:** Für alle vier Produktionshallen jeweils: „1 Spannbinder + 1 Spannabfangeträger + 1 Randbalken"; „3 Rahmenstützen mit Fundament"; „2 Giebelstützen mit Fundament". Annahme: „Ortbetonköcherfundamente auf tragfähigem Baugrund, bzw Stützen mit angeformten Fundamenten".
- **Bestätigt durch:**
  - [`Anfrage Nr. 28844.61.pdf`](https://drive.google.com/file/d/1AkHStxlQdVxvuzalNdG5E59zE8WC5MUH/view): Spannbeton-Hohlkörperdecken d=20cm, Expositionsklasse XC1, Spannweite bis 8,00 m für Eingangshalle-Dachdecke.
  - [`AN220251.pdf`](https://drive.google.com/file/d/1l5It9EzQpSVBx2En4oM4cITJKzuuP42z/view) (ELBE delcon): EFD 20/120 Spannbetondeckenplatte d=20cm; REI 90 (F 90); Plattengewicht 344 kg/m²; Spannstahl 1180,00€/to (Kalkulationsstand).
  - [`1453 - Angebot Nr. 28844.12  N8.pdf`](https://drive.google.com/file/d/1SxZ0ueV-qqlEITmvpz71dR4QQBCBguhE/view): Kompressorraum mit Halbmontage-Pi-Plattendecke für bauseitigen Aufbeton, Rippenhöhe ca. 30 cm; Stützen b/d = ca. 50/50 cm; Mittelunterzüge b/d = ca. 40/55 cm; Wandplatten 16 cm und 20 cm.
  - [`Anlage 5_U412 Grundriss.pdf`](https://drive.google.com/file/d/15Nyv06gwam_NISikAE2qu_pRuC4BZjd4/view): Grundriss zeigt „Spann-Binder F106" und „Hohlkörperdecken d=20cm" im Eingangsgebäude. Expositionsklassen: XC1 Innenbauteile, XC2 Gründungsbauteile, XC4 Außenbauteile.
- **Hinweis:** Das Projekt verwendet ein breites Spektrum an Betonfertigteiltechnologien: Spannbeton für die Hallendachkonstruktionen (Spannbinder mit Binder-Montage L=15,76 m, ca. 10.400 kg), Spannbeton-Hohldielen für die Eingangshalle-Decke und Pi-Platten (TT-Decken) mit Ortbetonergänzung für den Kompressorraum. Herstellung im REKERS-Werk Spelle, bei Kapazitätsengpass Ausweichwerk Groß Ammensleben (Angebot 28844.12/N8). Brandschutzanforderung F90 durchgängig.

### Dachbegrünung ❌
- **Wert:** Nicht gefunden
- **Quelle:** Alle Angebotsversionen (28844.1, 28844.12/N8, 28844.61), Lastannahmen (Anlage 1–3), Grundrisse und Ansichten (Anlage 5–6), Genehmigungsstatik, DGNB-Anforderungsdokumente und 3D-Modell durchsucht. Keine Erwähnung von Dachbegrünung, Gründach, extensiver/intensiver Begrünung oder zusätzlichen Dachlasten über die dokumentierten Werte hinaus.
- **Hinweis:** Trotz angestrebter DGNB-Gold-Zertifizierung (Systemvariante NLO18) enthält keines der untersuchten Dokumente einen Hinweis auf Dachbegrünung. Die Eingangshalle hat Kiesschüttung auf Hohldielendecke, die Produktionshallen „normale Dachlasten". Die DGNB-Anforderungsdokumente fokussieren auf Materialgesundheit und Umweltverträglichkeit der Baustoffe, nicht auf Dachbegrünung. Bei einem Fertigteilwerk mit Spannbetonbindern und bis zu 100 m Gebäudebreite ist eine Dachbegrünung untypisch.

---

## Beobachtungen

Dieses Projekt ist mit einem Angebotswert von €15,3 Mio. und einer Gebäudebreite von 100 m das mit Abstand größte im Referenzdatensatz. Es handelt sich um ein komplettes Fertigteilwerk für die Nokera Grundbesitz 1 AG (Schweiz), errichtet durch die Baugesellschaft Walter Hellmich GmbH (Generalunternehmer) mit REKERS als Fertigteillieferant.

Die Angebotsentwicklung ist über zahlreiche Nachträge (N1–N8+) dokumentiert, wobei das Hauptangebot Nr. 28844.3 vom 21.02.2021 stammt und das hier untersuchte Ergänzungsangebot 28844.12/N8 für den Kompressorraum vom 25.03.2022 datiert. Der lange Zeitraum zwischen Anfrage (Juni 2021) und letzten Nachträgen (März 2022) deutet auf ein komplexes Bauvorhaben mit mehrphasiger Planung hin.

Bemerkenswert ist die DGNB-Gold-Zertifizierung — ungewöhnlich für ein Industriewerk dieser Größenordnung. Die DGNB-Anforderungen betreffen die Betonfertigteile (Gewerk Dachdeckenproduktion und Wand-/Fassadenproduktion) und fordern DGNB-Qualitätsstufe 4 mit EPD-Nachweisen, Materialzirkularität und Schadstofffreiheit.

Die Hohlkörperdecken für die Eingangshalle wurden parallel von zwei Anbietern bepreist: ELBE delcon (AN220251: €28.467, AN220378: €30.683) und H&L Baustoff-Werke (Angebot 123981: €31.080) — jeweils 610 m², d=20cm, REI 90. Beide Lieferanten verwenden Spannbeton-Hohldielen.

Krandokumentation ist im Vergleich zu anderen Projekten weniger detailliert: Die Genehmigungsstatik benennt Kranbahnrahmen und deren Achsen, enthält aber keine Kranspezifikationen (Hersteller, Traglasten, DIN-Klassifizierung). Die Kraninfrastruktur ist produktionsbedingt notwendig (Fertigteilherstellung).

---

## Quelle

- **Datenverzeichnis:** `~/Documents/projects/billable/REKERS__poc/data/2026-01-28_testdaten/38043/`
- **Design-Doc:** [Feasibility Report, Teil 2](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/design-doc-feasibility-report)
- **Session:** 0a67b61c-030d-4272-aaef-19d105731a05

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

