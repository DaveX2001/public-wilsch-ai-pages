---
publish: true
---

# DS-Kit RAG Test Analysis - Question-by-Question Verification

Analysis date: 2026-01-13
**Last updated:** 2026-01-19
**Source:** [Meeting transcript 2026-01-15](https://app.fireflies.ai/view/01KF0N6JDANZB4JGW9WEP4GNMC)

---

## 1. Executive Summary

| Metric | Pre-Meeting | Post-Meeting |
|--------|-------------|--------------|
| Total questions | 29 | 29 |
| Working | 17 | **18** (+Q5) |
| Data Gap | 9 | **6** (Q6,Q12,Q13,Q17,Q18,Q24,Q27) |
| Intentional | 0 | **2** (Q16,Q28) |
| Fixed | 0 | **1** (Q21) |
| VLM Data Loss | 1 | 1 (Q14) |
| **Current accuracy** | 17/29 (58.6%) | **18/29 (62%)** |
| **After website extraction** | - | **24/29 (83%)** |

**Key finding:** Client meeting (2026-01-15) validated several "failures" as acceptable. Website extraction identified as solution for remaining DATA GAPs.

---

## 2. Corpus Contents

### Indexed (Production - VLM + Q&A)
- **VLM chunks:** 56 documents (GraniteDocling VLM parsing)
- **Q&A pairs:** 14 documents (fragen-navigationssystem.csv)
- **Total:** 70 documents

### Available but not in production index
- **Standard Docling chunks:** 95 documents (includes PDF header with official contact)

### Key content in Q&A CSV (14 pairs)
1. Webseitendatenschutzerklärung → Kapitel 8
2. Textgeneratoren → FreeTools
3. E-Mail hinterlegen → dskit@iitr.de
4. Mitarbeiter Zugang → dskit@iitr.de
5. Magic-Link Login → passwortfrei/passwortgestützt
6. Schulung Häufigkeit → jährlich
7. Beratungsstunde → 180€ / DS-Kit Plus
8. Kündigung → 3 Monate
9. Datenschutzverpflichtung → Kapitel 11.2 (✅ corrected during meeting from 09)
10. Womit anfangen → Kapitel 02, 04, 05, 06, eLearning
11. AV-Verträge → Kapitel 05
12. Datenschutzverletzung → Kapitel 10
13. Auskunft Kunde → Kapitel 07
14. Audit → 360€

---

## 3. Question Analysis Table (All 29)

| Q# | Status | Question | In Corpus? | Category | Solution |
|----|--------|----------|------------|----------|----------|
| 1 | ✅ | Texte Webseitengestaltung | ✅ | Working | - |
| 2 | ✅ | Textgeneratoren | ✅ | Working | - |
| 3 | ✅ | Andere E-Mail hinterlegen | ✅ | Working | - |
| 4 | ✅ | Zugang Unterlagen | ✅ | Working | - |
| 5 | ✅ | Schulungen eLearning | ✅ | **Working** | Client validated |
| 6 | ❌ | DSB auf Webseite | ❌ | Data Gap | Website extraction |
| 7 | ✅ | Beratungsstunde | ✅ | Working | - |
| 8 | ✅ | Audit | ✅ | Working | - |
| 9 | ✅ | Weiterer Mitarbeiter | ✅ | Working | - |
| 10 | ✅ | Zusätzliches Benutzerkonto | ✅ | Working | - |
| 11 | ✅ | TISAX Schulungsinhalte | ✅ | Working | - |
| 12 | ❌ | DSB Meldung wer | ❌ | Data Gap | Website extraction |
| 13 | ❌ | Wo DSB melden | ❌ | Data Gap | Website extraction |
| 14 | ❌ | DSB Kontaktdaten | ❌ | VLM Loss | Standard Docling |
| 15 | ✅ | Kündigung Termin | ✅ | Working | - |
| 16 | ⏸️ | eLearning 300€ | ❌ | **Intentional** | No prices |
| 17 | ❌ | Englisch Zertifikat | ❌ | Data Gap | Masterfragen |
| 18 | ❌ | Zugang DS-Kit URL | ❌ | Data Gap | Website extraction |
| 19 | ✅ | Magic-Link wiederholt | ✅ | Working | - |
| 20 | ✅ | Jahresvertrag automatisch | ✅ | Working | - |
| 21 | ✅ | Mitarbeiter Docs | ✅ | **Fixed** | CSV corrected |
| 22 | ✅ | Mitarbeiter einladen | ✅ | Working | - |
| 23 | ✅ | Schulung Häufigkeit | ✅ | Working | - |
| 24 | ❌ | Video E-Mail Vereinbarung | ❌ | Data Gap | Website/Masterfragen |
| 25 | ✅ | Datenschutzverletzung | ✅ | Working | - |
| 26 | ✅ | Datenschutzhinweise DE/EN | ✅ | Working | - |
| 27 | ❌ | Bewerber unterschreiben | ❌ | Data Gap | Masterfragen |
| 28 | ⏸️ | Cookie Banner 180€ | ❌ | **Intentional** | Support escalation |
| 29 | ✅ | Webseite Beratung | ✅ | Working | - |

---

## 4. Detailed Answer Comparison (All 29)

### Q1: Texte Webseitengestaltung ✅
- **Question:** "Wo finde ich die Texte für die Webseitengestaltung?"
- **Expected:** Texte für die Webseiten-Datenschutzerklärung finden Sie in Kapitel 8 des Datenschutz-Kit.
- **Current:** Die Texte für die Webseitengestaltung finden Sie nicht direkt im Datenschutz-Kit. Allerdings finden Sie Texte für Webseitendatenschutzerklärung in Kapitel 8...
- **Note:** ✅ Correct - mentions Kapitel 8

### Q2: Textgeneratoren ✅
- **Question:** "Bieten Sie auch Textgeneratoren für die Webseite?"
- **Expected:** Wir bieten auf unserer Webseite im Bereich "FreeTools" entsprechende Textgeneratoren an.
- **Current:** Ja, wir bieten Textgeneratoren für die Webseiten-Datenschutzerklärung an. Sie finden diese im Bereich FreeTools...
- **Note:** ✅ Correct - mentions FreeTools

### Q3: Andere E-Mail hinterlegen ✅
- **Question:** "Können Sie eine andere E-Mail-Adresse als Zugriffberechtigten hinterlegen?"
- **Expected:** Wir können eine andere E-Mailadresse für den Login hinterlegen. Nachricht an dskit@iitr.de
- **Current:** Ja, es ist möglich, eine andere E-Mail-Adresse hinterlegen. Kontakt: dskit@iitr.de
- **Note:** ✅ Correct - provides dskit@iitr.de

### Q4: Zugang Unterlagen ✅
- **Question:** "Erhalten wir Zugang zu allen Datenschutzunterlagen?"
- **Expected:** Im Datenschutz-Kit finden Sie alle erforderlichen Vorlagen für Datenschutzkonformität.
- **Current:** Ja, Sie erhalten Zugang zu allen im Datenschutz-Kit hinterlegten Datenschutzunterlagen...
- **Note:** ✅ Correct - confirms full access

### Q5: Schulungen eLearning ✅ WORKING (validated 2026-01-15)
- **Question:** "Kriegen wir auch Schulungen, damit wir unsere Mitarbeiter ab und zu mal beim Datenschutz auf den Stand bringen?"
- **Expected:** Über das Datenschutz-Kit können Sie im Bereich "eLearning" auf eine Auswahl von Schulungsmodulen zugreifen + Statistik-Funktion
- **Current:** Ja, Sie erhalten Schulungen. Die Datenschutzschulung sollte mindestens einmal im Jahr erfolgen...
- **Note:** ✅ **Client validated (meeting 2026-01-15):** General answer IS correct for non-expert users. RAG answering the spirit of the question, not expecting navigation-specific answer. Stellmacher: "Die Frage zielt darauf ab, ob Schulungen im Leistungsportfolio sind. Die RAG Response ist richtig." CSV update pending.

### Q6: DSB auf Webseite ❌ DATA GAP → Website Extraction
- **Question:** "Kann ich Sie als Datenschutzbeauftragter auf meiner Webseite angeben?"
- **Expected:** Wenn Sie uns als Ihren externen Datenschutzbeauftragten bestellt haben, können Sie uns auf Ihrer Webseite mit folgenden Kontaktdaten angeben...
- **Current:** Nein, Sie können uns nicht als Datenschutzbeauftragten auf Ihrer Webseite angeben...
- **Note:** ❌ Wrong - RAG says "Nein" but correct is "Ja, wenn bestellt".
- **Solution (meeting 2026-01-15):** Content exists in core.iitr.de Chapter 01 ("Verwenden Sie bitte folgende Angaben: IITR Datenschutz GmbH..."). Website extraction will fix.

### Q7: Beratungsstunde ✅
- **Question:** "Wir brauchen tiefergehende Beratung, wie können Sie uns helfen?"
- **Expected:** Beratungsstunde zu 180€ netto oder Upgrade auf DS-Kit Plus.
- **Current:** Als Datenschutz-Kit Basis-Kunde können Sie Beratung im Rahmen einer Beratungsstunde zu 180€ netto...
- **Note:** ✅ Correct - mentions 180€ and options

### Q8: Audit ✅
- **Question:** "Machen Sie ein Audit?"
- **Expected:** Wir überprüfen Ihren Datenschutz-Status. Aufwand: 360€ zzgl. USt.
- **Current:** Ja, wir können ein Audit durchführen. Dabei überprüfen wir Ihren allgemeinen Datenschutz-Status...
- **Note:** ✅ Correct - confirms audit service

### Q9: Weiterer Mitarbeiter ✅
- **Question:** "Wie kann ein weiterer Mitarbeiter auf das Datenschutz-Kit zugreifen?"
- **Expected:** Wir richten gerne einen weiteren Benutzerzugang ein. E-Mail an dskit@iitr.de
- **Current:** Um einem weiteren Mitarbeiter Zugriff zu gewähren, E-Mail an dskit@iitr.de senden...
- **Note:** ✅ Correct - provides contact

### Q10: Zusätzliches Benutzerkonto ✅
- **Question:** "Wie kann ich ein zusätzliches Benutzerkonto einrichten?"
- **Expected:** Wir richten gerne einen weiteren Benutzerzugang ein. E-Mail an dskit@iitr.de
- **Current:** Um ein zusätzliches Benutzerkonto einzurichten, wenden Sie sich an das DS-Kit-Team unter dskit@iitr.de...
- **Note:** ✅ Correct - provides contact

### Q11: TISAX Schulungsinhalte ✅
- **Question:** "Im Rahmen unseres TISAX Audits müssen wir genau dokumentieren welche Schulungsinhalte wir verwenden..."
- **Expected:** Innerhalb des Datenschutz-Kits finden Sie unter Kapitel 11 im Unterpunkt 3 eine Übersicht aller Schulungsmodule.
- **Current:** Ja, im Datenschutz-Kit finden Sie eine Übersicht über alle Sensibilisierungsschulungen im Dokument 11-03...
- **Note:** ✅ Correct - references Dokument 11-03

### Q12: DSB Meldung wer ❌ DATA GAP → Website Extraction
- **Question:** "Übernehmen Sie die Meldung des externen Datenschutzbeauftragten?"
- **Expected:** Die Meldung des DSB bei der Aufsichtsbehörde muss durch den Verantwortlichen erfolgen.
- **Current:** Die Meldung des externen Datenschutzbeauftragten an die zuständige Datenschutz-Aufsichtsbehörde...
- **Note:** ❌ Doesn't clarify WHO must file.
- **Solution:** Website extraction (Chapter 01 contains DSB policy cluster).

### Q13: Wo DSB melden ❌ DATA GAP → Website Extraction
- **Question:** "Wo melde ich den externen Datenschutzbeauftragten?"
- **Expected:** Wenn Sie uns als DSB bestellt haben, können Sie uns mit folgenden Kontaktdaten melden: [IITR details]
- **Current:** Die Frage, wo Sie den externen Datenschutzbeauftragten melden, wird im Kontext nicht direkt beantwortet...
- **Note:** ❌ RAG admits it can't answer.
- **Solution:** Website extraction (Chapter 01 contains DSB policy cluster).

### Q14: DSB Kontaktdaten ❌ VLM LOSS → Standard Docling
- **Question:** "Mit welchen Kontaktdaten melde ich den externen Datenschutzbeauftragten?"
- **Expected:** email@iitr.de, IITR Datenschutz GmbH, Marienplatz 2, 80331 München, Tel (089) 18 91 73 60
- **Current:** Für die Kontaktaufnahme: IITR Datenschutz GmbH, Dr. Sebastian Kraska, dskit@iitr.de
- **Note:** ❌ Wrong email (dskit@ vs email@), missing address/phone. VLM chunks lost PDF header data.
- **Solution:** Switch to standard Docling (includes PDF header with official contact) or website extraction.

### Q15: Kündigung Termin ✅
- **Question:** "Zu welchem Termin kann ich den Vertrag kündigen?"
- **Expected:** Die Kündigung ist drei Monate vor Vertragsverlängerung schriftlich unter dskit@iitr.de einzureichen.
- **Current:** Der Bestellvertrag hat feste Laufzeit und verlängert sich automatisch. Kündigung drei Monate vor Vertragsende...
- **Note:** ✅ Correct - mentions 3 months

### Q16: eLearning 300€ ⏸️ INTENTIONAL (no prices)
- **Question:** "Wie kann ich auf weitere eLearning Module zugreifen?"
- **Expected:** Im DS-Kit Basis stehen standardmäßig vier eLearning-Module zur Verfügung. Vollzugriff für 300€ netto jährlich.
- **Current:** Wenn Sie auf weitere eLearning-Module zugreifen möchten, wenden Sie sich an den Support...
- **Note:** ⏸️ **Client decision (meeting 2026-01-15):** Do NOT include prices. Kraska: "Wollen wir überhaupt die Preise nennen? Eigentlich ja nicht." Generic "contact support" answer is intentionally acceptable.

### Q17: Englisch Zertifikat ❌ DATA GAP → Masterfragen
- **Question:** "Stellen Sie auch englischsprachige Zertifikate zur Verfügung?"
- **Expected:** Wir können Ihnen gerne ein englischsprachiges Datenschutz-Zertifikat ausstellen.
- **Current:** Die DS-Kit-Plattform bietet standardmäßig keine englischsprachigen Zertifikate an...
- **Note:** ❌ Wrong - RAG says "Nein" but correct is "Ja, gerne". Service not documented in corpus.
- **Solution:** Masterfragen addition (Stellmacher to provide).

### Q18: Zugang DS-Kit URL ❌ DATA GAP → Website Extraction
- **Question:** "Wie erhalte ich einen Zugang zum Datenschutz-Kit?"
- **Expected:** core.iitr.de aufrufen und unter Angabe Ihrer E-Mail-Adresse einen Login-Link anfordern.
- **Current:** Um einen Zugang zu erhalten, wenden Sie sich direkt an die IITR...
- **Note:** ❌ Doesn't mention "core.iitr.de" URL. Login URL not in corpus.
- **Solution:** Website extraction will include platform URL reference.

### Q19: Magic-Link wiederholt ✅
- **Question:** "Muss ich bei wiederholtem Besuch jedes Mal einen neuen Zugangslink anfordern?"
- **Expected:** Login ist standardmäßig passwortfrei (Magic-Link). Passwortgestützter Zugang auf Wunsch möglich.
- **Current:** Sie müssen nicht unbedingt einen neuen Link anfordern. Das Login-Verfahren ist auf passwortfreie Anmeldung (Magic-Link) eingestellt...
- **Note:** ✅ Correct - explains Magic-Link

### Q20: Jahresvertrag automatisch ✅
- **Question:** "Läuft unser Jahresvertrag automatisch aus?"
- **Expected:** Der Vertrag verlängert sich automatisch, sofern keine Kündigung erfolgt.
- **Current:** Der Jahresvertrag läuft nicht automatisch ab. Der Bestellvertrag hat feste Laufzeit und verlängert sich...
- **Note:** ✅ Correct - explains auto-renewal

### Q21: Mitarbeiter Docs ✅ FIXED (CSV corrected)
- **Question:** "Welche Dokumente brauchen wir unterschrieben von den Mitarbeitern?"
- **Expected:** Verpflichtungserklärung auf Einhaltung des Datenschutzes. Vorlage unter Kapitel 11, Unterpunkt 2.
- **Current:** Für die Erfüllung der datenschutzrechtlichen Pflichten gibt es mehrere Dokumente... [mentions Kapitel 9]
- **Note:** ✅ **Fixed during meeting (2026-01-15):** CSV corrected from Kapitel 9 → Kapitel 11.2. Stellmacher: "Kapitel 11 ist es... Kapitel 9 ist ein ganz anderes Thema." **Action:** Re-index after CSV update.

### Q22: Mitarbeiter einladen ✅
- **Question:** "Wie kann ich Mitarbeiter zu den Schulungen einladen?"
- **Expected:** Über den Menüpunkt "Mitarbeiterschulung" in der Kategorie "eLearning" Online-Schulungen per E-Mail versenden.
- **Current:** Um Mitarbeiter zu den Schulungen einzuladen: Mitarbeiterschulung im eLearning-Bereich auswählen...
- **Note:** ✅ Correct - describes eLearning process

### Q23: Schulung Häufigkeit ✅
- **Question:** "Wie häufig sollte ich meine Mitarbeiter schulen?"
- **Expected:** Die Datenschutz-Schulung sollte mindestens einmal im Jahr erfolgen (Erwartung der Aufsichtsbehörden).
- **Current:** Die Datenschutzschulung sollte mindestens einmal im Jahr erfolgen. Dies ergibt sich aus der Erwartung...
- **Note:** ✅ Correct - mentions yearly requirement

### Q24: Video E-Mail Vereinbarung ❌ DATA GAP → Website/Masterfragen
- **Question:** "In welchen Videos wird die Personal-Vereinbarung bzgl. E-Mail nach Beendigung besprochen?"
- **Expected:** Eine Vorlage für Vereinbarung über Zugriff auf E-Mail nach Beendigung finden Sie in Kapitel 11, Unterpunkt 3.
- **Current:** Die Information über E-Mails mit privatem Inhalt wird nicht direkt in Verbindung mit Videos erwähnt...
- **Note:** ❌ RAG can't answer. Post-employment email policy not in corpus.
- **Solution:** Website extraction (Chapter 11) or Masterfragen addition.

### Q25: Datenschutzverletzung ✅
- **Question:** "Wir haben eine Datenschutzverletzung, was ist zu tun?"
- **Expected:** In Kapitel 10 Dokument "Interne Meldung Datenschutzverletzung" herunterladen und ausfüllen. An dskit@iitr.de senden.
- **Current:** Zunächst das Dokument "Interne Meldung Datenschutzverletzung" in Kapitel 10 herunterladen...
- **Note:** ✅ Correct - references Kapitel 10 and process

### Q26: Datenschutzhinweise DE/EN ✅
- **Question:** "Müssen wir unsere Datenschutzhinweise in Deutsch und Englisch aktualisieren?"
- **Expected:** Unter Kapitel 8 erhalten Sie eine deutsche und englische Vorlage für die Datenschutzerklärung.
- **Current:** Ja, Sie gehen richtig in der Annahme. Unter Kapitel 8 im Datenschutz-Kit erhalten Sie Vorlagen...
- **Note:** ✅ Correct - mentions Kapitel 8

### Q27: Bewerber unterschreiben ❌ DATA GAP → Masterfragen
- **Question:** "Müssen unsere Bewerber unsere Datenschutzbestimmungen unterschreiben?"
- **Expected:** Es ist nicht erforderlich, dass Bewerber unterschreiben. Sie sollten jedoch zur Kenntnis nehmen.
- **Current:** Die Frage, ob Bewerber unterschreiben müssen, wird im Kontext nicht direkt beantwortet...
- **Note:** ❌ RAG can't answer. Applicant policy not in corpus.
- **Solution:** Masterfragen addition (Stellmacher to provide).

### Q28: Cookie Banner ⏸️ INTENTIONAL (support escalation)
- **Question:** "Ist unser Cookie Banner so in Ordnung?"
- **Expected:** Gerne prüfen wir Ihre Webseite auf Datenschutzkonformität zu 180€ netto Stundensatz.
- **Current:** Die derzeit genutzte Cookie-Entscheidung mit "ich stimme zu" und "ich lehne ab"... [gives technical advice]
- **Note:** ⏸️ **Client decision (meeting 2026-01-15):** Out-of-scope questions should escalate to human support. Kraska: "Immer wenn er nach Preise fragt, ausschleifen auf Rückruf." **Action:** Implement support escalation path (Level 2: email link).

### Q29: Webseite Beratung ✅
- **Question:** "Bieten Sie Beratung zu Webseiten im Bereich Datenschutz an?"
- **Expected:** Beratung im Rahmen einer Beratungsstunde zu 180€ netto oder DS-Kit Plus Upgrade.
- **Current:** Ja, wir bieten Beratung zu Webseiten im Bereich Datenschutz an. Im Rahmen einer Beratungsstunde zu 180€ netto...
- **Note:** ✅ Correct - mentions 180€ and options

---

## 5. Recommendations

### Content Additions (9 items)
Client should provide answers for:
1. DSB listing policy on customer websites (Q6)
2. Who files DSB registration - customer or IITR? (Q12)
3. Official DSB registration contact details (Q13)
4. eLearning tier pricing - 4 standard modules, 300€ for full access (Q16)
5. English certificate availability (Q17)
6. Login URL - core.iitr.de (Q18)
7. Post-employment email access agreement location (Q24)
8. Applicant data protection acknowledgment policy (Q27)
9. Website review service description and pricing (Q28)

### Q&A Correction (1 item)
- Row 9: Verify "Datenschutzverpflichtung" chapter reference (currently says Kapitel 09, likely should be Kapitel 11)

### Index Decision
Current production uses VLM chunks (70 docs) which lost official contact metadata.

Options:
1. **Revert to standard Docling** - Has official contact but more OCR noise
2. **Add targeted Q&A** - Supplement VLM with explicit Q&A for lost content
3. **Merge both** - Use VLM for content, add standard Docling header chunk

---

*Document created for client discussion. Add findings and decisions as comments below.*

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

