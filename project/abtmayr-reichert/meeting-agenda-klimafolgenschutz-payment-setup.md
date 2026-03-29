---
publish: true
---

# Meetingagenda: Klimafolgenschutz — Zahlungseinrichtung & Vereins-Voraussetzungen
[[client-abtmayr-reichert]]

## Meetingziel

Zuerst das Design Doc gemeinsam durchgehen — gibt es etwas, das nicht gefällt oder fehlt? Danach die offenen Punkte klären, damit wir mit der Umsetzung starten können.

1. **Design Doc Review** — gemeinsam durchgehen, Feedback und Korrekturen
2. **Vereinsstatus + Stripe** — offene Punkte klären (Punkte 1-2)
3. **Zulieferungen + Ideen** — was Sie liefern und was Sie sich zusätzlich vorstellen (Punkte 3-4)
4. **Projektumfang abstimmen** — Phasen, Aufwand und Meilensteine (Punkt 5)

## Vorab-Lektüre

- [Design Doc: Klimafolgenschutz Website](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/abtmayr-reichert/design-doc-klimafolgenschutz-website) — insbesondere Teil 3 (Zahlung), aktualisiert am 12. Feb 2026
- [Aufwandsschätzung: Klimafolgenschutz Website](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/abtmayr-reichert/scoping-estimate-klimafolgenschutz-website) — Richtpreisangebot mit Phasen-Meilensteinen

---

## Diskussionspunkte

*Ausgangspunkte für die Diskussion, nicht darauf beschränkt.*

### 1. Vereinsstatus — zwei offene Fragen

Zwei getrennte Vorgänge bestimmen, was die Website beim Launch anbieten kann:

**Vereinsregister (Amtsgericht):**
- Antrag seit Juli 2025, Stand 9. Feb: noch in Prüfung
- Ohne Eintrag: kein Bankkonto, keine Gläubiger-ID, kein Impressum, keine Zahlung
- Website kann trotzdem ohne Zahlung starten — Zahlungsaktivierung erfolgt separat
- **→ Wann rechnen Sie mit dem Eintrag?**

**Gemeinnützigkeit:**
- Spendenbutton funktioniert in jedem Fall
- Mit Freistellungsbescheid: "steuerlich absetzbar" auf der Website + Spendenbescheinigungen ab €100
- Ohne: Spenden möglich, aber ohne Steuer-Hinweis
- **→ Liegt ein Freistellungsbescheid vor?**

### 2. Stripe-Konto einrichten und Zugangsdaten bereitstellen

- Stripe-Konto registrieren: [Stripe Registrierung](https://dashboard.stripe.com/register)
- Stripe führt durch die Einrichtung — welche Dokumente benötigt werden, steht hier: [Verifikation für Vereine](https://support.stripe.com/questions/documents-for-business-verification-of-unincorporated-entities-partnerships-or-non-profits)
- Wir integrieren Ihr Stripe-Konto in die Website
- Bei Spenden ab €100 erfasst der Checkout automatisch Name + Adresse — das ist gesetzlich vorgeschrieben für die Spendenbescheinigung

**Referenzen:**
- [Preise](https://stripe.com/de/pricing)
- [SEPA-Lastschrift in Deutschland](https://stripe.com/resources/more/direct-debit-germany)
- [Stripe für Vereine](https://stripe.com/resources/more/membership-fees-associations-germany)
- [Rabatt für gemeinnützige Organisationen](https://support.stripe.com/questions/fee-discount-for-nonprofit-organizations)

- **→ Bitte Stripe-Konto einrichten und uns die API-Zugangsdaten bereitstellen**

### 3. Ihre zusätzlichen Ideen

Sie haben zwei Erweiterungen vorgeschlagen, die nicht im aktuellen Umfang enthalten sind:

- **Interaktive Bedarfsanalyse-Seite** — Gemeinden prüfen vorhandene Infrastruktur (Hitzeschutz, Digitalisierung, Sensorik) über einen Fragebogen, der in den Mitgliedschafts-Flow mündet
- **Spielerisches Engagement-Element ("Moorhuhn")** — Gamification-Ansatz auf der Website

Beide Ideen brauchen eine Beschreibung dessen, was Sie sich konkret vorstellen, bevor eine Aufwandsschätzung möglich ist.

### 4. Offene Zulieferungen von Ihnen

Folgende Punkte werden von Ihnen benötigt, um mit der Umsetzung beginnen zu können:

| Zulieferung | Wofür | Blockiert Phase |
|-------------|-------|-----------------|
| Ionos DNS-Zugangsdaten | Domain klimafolgenschutz.com auf Server zeigen | Phase 1 |
| Logo-Dateien | Branding, Header, Favicon | Phase 1 |
| Impressum über [e-Recht24](https://www.e-recht24.de/) ausfüllen | Impressum + Datenschutzerklärung | Phase 2 |
| Gemeinde-Preisstufen bestätigen (Staffelung nach Einwohnerzahl) | Mitgliedschaftsformular + Preisanzeige | Phase 2 |
| Zertifizierungskriterien für "klimaresiliente Gemeinde" | Zertifizierungs-Sektion | Phase 4 |

- Keine dieser Zulieferungen blockiert den Projektstart — Platzhalter werden eingesetzt
- **Benötigt:** Bestätigen Sie, was Sie bis wann liefern können

### 5. Projektumfang in fünf Phasen mit Meilenstein-Preisen

Die Website ist in fünf aufeinander aufbauende Phasen gegliedert. Jede Phase liefert ein eigenständiges Ergebnis. Abrechnung erfolgt nach tatsächlichem Aufwand (T&M, €120/Std.).

| Phase | Lieferergebnis | ~Stunden | Kumuliert (€120/Std.) |
|-------|---------------|----------|----------------------|
| 1 | Technische Basis + Design | ~3-5 | ~€360-600 |
| 2 | Inhaltsseiten + Mitgliedschaftsformulare | ~6-10 | ~€1.080-1.800 |
| 3 | Zahlungsintegration (Stripe) | ~4-6 | ~€1.560-2.520 |
| 4 | Zertifizierung | ~2-3 | ~€1.800-2.880 |
| 5 | CMS — eigenständige Inhaltspflege | ~4-6 | ~€2.280-3.600 |

- Phase 3 ist abhängig vom Vereinsregistereintrag (aktuell blockiert)
- Phase 4 setzt voraus, dass Sie die Zertifizierungskriterien liefern
- Phase 5 ist optional — ohne CMS werden Inhaltsänderungen vom Entwickler umgesetzt
- Richtpreisangebot innerhalb des bestehenden Rahmenvertrags

## Meetingformat

- **Typ:** Review (45-60 Min)
- **Vorbereitung:** Punkt 1 (Gemeinnützigkeit) vorab mit Steuerberater prüfen falls möglich
- **Ergebnis:** Aktualisiertes Design Doc (Zahlungspunkte Defined) + abgestimmter Projektumfang für Umsetzungsstart

## Verknüpfungen

- **Issue:** [#726 — Michael Reichert Climate Association Website Development](https://github.com/DaveX2001/deliverable-tracking/issues/726)
- **Design Doc:** [Klimafolgenschutz Website](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/abtmayr-reichert/design-doc-klimafolgenschutz-website)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

