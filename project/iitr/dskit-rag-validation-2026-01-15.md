---
publish: true
---

# DS-Kit RAG Validation Report - 2026-01-15

Validation of test failures against source documents (PDF + CSV) for client meeting.

---

## Summary

| Metric | Value |
|--------|-------|
| Total Questions | 29 |
| Working | 17 (58.6%) |
| Failures Validated | 12 |

### Failure Categories

| Category | Count | Color | Action Required |
|----------|-------|-------|-----------------|
| TRUE DATA GAP | 9 | Red | Client must provide content |
| VLM DATA LOSS | 1 | Blue | Fixable - add header chunk |
| Q&A DATA ERROR | 1 | Green | Fixable - correct CSV |
| AMBIGUOUS | 1 | Yellow | Client decides preference |

---

## Q5: Schulungen / eLearning

**Category:** AMBIGUOUS

### Question
Kriegen wir auch Schulungen, damit wir unsere Mitarbeiter ab und zu mal beim Datenschutz auf den Stand bringen?

### Expected Answer
Über das Datenschutz-Kit können Sie im Bereich "eLearning" auf eine Auswahl von Schulungsmodulen zugreifen, mithilfe derer Sie Ihre Beschäftigten unter anderem im Bereich Datenschutz unterweisen können. Unter "Statistik" finden Sie die Dokumentation der absolvierten und nicht absolvierten Schulungen.

### RAG Response
Ja, Sie erhalten Schulungen, um Ihre Mitarbeiter regelmäßig beim Thema Datenschutz zu schulen. Die Datenschutzschulung der Mitarbeiter sollte mindestens einmal im Jahr erfolgen. Dies ergibt sich aus der Erwartung der deutschen Datenschutz-Aufsichtsbehörden.

### Source Check: In Data?
- **PDF p.9-10:** eLearning features exist ✓
- **CSV Row 6:** Frequency answer exists ✓
- **Result:** BOTH answers exist in source data

### Source Check: Retrieved by RAG?
Retrieved 6 chunks (Typesense search "Schulung"):
1. **Rank 1 (CSV):** "Wie häufig sollte ich... schulen? - mindestens einmal im Jahr" ← LLM used this
2. **Rank 2 (PDF):** Statistik feature description
3. **Rank 3 (PDF):** eLEARNING overview
4. **Rank 4 (PDF):** Schulungsmodul selection
5. **Rank 5 (CSV):** Beratungsstunde (not relevant)
6. **Rank 6 (PDF):** Mitarbeiterschulung in eLEARNING

**Result:** eLearning content WAS retrieved (ranks 2-6), but LLM chose rank 1 (frequency)

### Client Discussion
Client decides: Feature description (where/how to access) OR frequency (how often)?

---

## Q6: DSB auf Webseite angeben

**Category:** TRUE DATA GAP

### Question
Kann ich Sie als Datenschutzbeauftragter auf meiner Webseite angeben?

### Expected Answer
Wenn Sie uns als Ihren externen Datenschutzbeauftragten bestellt haben, können Sie uns auf Ihrer Webseite sowie in Verträgen und bei der zuständigen Datenschutzaufsichtsbehörde mit den folgenden Kontaktdaten benennen: IITR Datenschutz GmbH, Dr. Sebastian Kraska, Marienplatz 2, 80331 München, E-Mail: email@iitr.de, Telefon: 089 189 173 60

### RAG Response
Nein, Sie können uns nicht als Datenschutzbeauftragten auf Ihrer Webseite angeben. Der Datenschutzbeauftragte des DS-Kit ist die IITR Datenschutz GmbH, vertreten durch Dr. Sebastian Kraska.

### Source Check
- **PDF:** NOT FOUND (16 pages checked)
- **CSV:** NOT FOUND (14 rows checked)
- **Result:** Policy about listing IITR on customer website does NOT exist

### What RAG Got Right
- ✓ Company name: IITR Datenschutz GmbH
- ✓ Contact person: Dr. Sebastian Kraska

### What RAG Got Wrong
1. **Said "Nein"** - Should be "Ja" (with condition)
2. **Missing condition:** "wenn Sie uns als externen DSB bestellt haben"
3. **Missing contact details:** Address, phone, correct email

### Why It Failed
RAG has no policy statement saying customers CAN list IITR. Without explicit permission, it defaulted to "Nein."

### Client Action Required
Add ONE Q&A pair to CSV:

**Frage:** "Kann ich Sie als Datenschutzbeauftragter auf meiner Webseite angeben?"

**Antwort:** "Ja. Wenn Sie uns als Ihren externen Datenschutzbeauftragten bestellt haben, können Sie uns auf Ihrer Webseite mit folgenden Kontaktdaten angeben: IITR Datenschutz GmbH, Dr. Sebastian Kraska, Marienplatz 2, 80331 München, E-Mail: email@iitr.de, Telefon: 089 189 173 60"

---

## Q12: DSB Meldung - Wer?

**Category:** TRUE DATA GAP

### Question
Übernehmen Sie die Meldung des externen Datenschutzbeauftragten?

### Expected Answer
Die Meldung des Datenschutzbeauftragten bei der zuständigen Datenschutzaufsichtsbehörde muss durch den Verantwortlichen erfolgen. Wenn Sie uns als Ihren externen Datenschutzbeauftragten bestellt haben, sollten Sie die Meldung bei Ihrer zuständigen Datenschutzbehörde bitte selbstständig vornehmen. Weitere Informationen hierzu finden Sie unter Kapitel 1 des Datenschutz-Kits.

### RAG Response
Die Meldung des externen Datenschutzbeauftragten (DSB) an die zuständige Datenschutz-Aufsichtsbehörde erfolgt gemäß den Vorgaben des DS-Kit. Dazu steht Ihnen das Dokument 01-02 DS-Kit Meldung DSB an Aufsicht zur Verfügung.

### Source Check: In Data?
- **PDF p.11:** REFERENCES Document 01-02, but only says "use this document"
- **Templates folder:** Document 01-02 does NOT exist (templates start at Chapter 03)
- **CSV:** NOT FOUND
- **Result:** The actual Document 01-02 is NOT in our corpus - only a reference to it

### Source Check: Retrieved by RAG?
- Retrieved the PDF chunk that references Document 01-02
- But cannot retrieve the document itself (doesn't exist in corpus)

### What RAG Got Right
- ✓ Document 01-02 exists for DSB registration
- ✓ It's for registering with Aufsichtsbehörde

### What RAG Got Wrong
- **Missing WHO:** Does not say the CUSTOMER must file (not IITR)
- **Missing responsibility:** "muss durch den Verantwortlichen erfolgen"

### Why It Failed
Same pattern as Q6: RAG knows WHAT (the document) but not WHO (customer's responsibility). Document 01-02 itself doesn't exist in corpus - only a reference to it.

### Client Action Required
Add ONE Q&A pair to CSV:

**Frage:** "Übernehmen Sie die Meldung des externen Datenschutzbeauftragten?"

**Antwort:** "Nein. Die Meldung des DSB bei der Aufsichtsbehörde muss durch den Verantwortlichen (Sie als Kunde) erfolgen. Weitere Informationen finden Sie in Dokument 01-02 unter Kapitel 1 des Datenschutz-Kits."

---

## Q13: DSB Meldung - Wo?

**Category:** TRUE DATA GAP

### Question
Wo melde ich den externen Datenschutzbeauftragten?

### Expected Answer
Wenn Sie uns als Ihren externen Datenschutzbeauftragten bestellt haben, können Sie uns auf Ihrer Webseite sowie in Verträgen und bei der zuständigen Datenschutzaufsichtsbehörde mit den folgenden Kontaktdaten benennen: IITR Datenschutz GmbH, Dr. Sebastian Kraska, Marienplatz 2, 80331 München, E-Mail: email@iitr.de, Telefon: 089 189 173 60

### RAG Response
Die Frage, wo Sie den externen Datenschutzbeauftragten melden, wird im Kontext nicht direkt beantwortet.

### Source Check
- **PDF p.1:** Contact in header exists ✓
- **BUT:** Not in DSB registration context
- **CSV:** NOT FOUND
- **Result:** DSB registration contact details missing in proper context

### What RAG Got Right
- ✓ Knows DSB registration involves Aufsichtsbehörde

### What RAG Got Wrong
- Could not answer WHERE to register
- Contact details exist in PDF header but NOT in DSB context

### Why It Failed
Same root cause as Q6, Q12, Q14: **No DSB policy content exists.** Contact info is there, but not connected to "use this for DSB registration."

### Client Action Required
**ONE DSB policy Q&A would fix Q6, Q12, Q13, Q14:**

**Frage:** "Welche Kontaktdaten verwende ich für die DSB-Meldung?"

**Antwort:** "Für die Meldung des externen Datenschutzbeauftragten verwenden Sie: IITR Datenschutz GmbH, Dr. Sebastian Kraska, Marienplatz 2, 80331 München, E-Mail: email@iitr.de, Telefon: 089 189 173 60. Die Meldung muss durch Sie als Verantwortlicher erfolgen."

---

## Q14: DSB Kontaktdaten

**Category:** VLM DATA LOSS

### Question
Mit welchen Kontaktdaten melde ich den externen Datenschutzbeauftragten?

### Expected Answer
IITR Datenschutz GmbH, Dr. Sebastian Kraska, Marienplatz 2, 80331 München, E-Mail: email@iitr.de, Telefon: 089 189 173 60

### RAG Response
Für die Kontaktaufnahme: IITR Datenschutz GmbH, Dr. Sebastian Kraska, E-Mail: dskit@iitr.de

### Source Check
- **PDF p.1 HEADER:** email@iitr.de, Marienplatz 2, Tel (089) 18 91 73 60 EXISTS ✓
- **Result:** VLM parsing LOST header data (wrong email: dskit@ instead of email@)

### Client Discussion
**FIXABLE:** Add PDF header chunk to index, or use standard Docling instead of VLM.

---

## Q16: eLearning 300€ Pricing

**Category:** TRUE DATA GAP

### Question
Ich bin bereits Kunde des Datenschutz-Kits. Wie kann ich auf weitere eLearning Module zugreifen?

### Expected Answer
Im Rahmen des Datenschutz-Kit Basis stehen Ihnen standardmäßig vier eLearning-Schulungsmodule zur Verfügung. Zu einem Aufpreis von 300 € netto jährlich schalten wir Ihnen gerne den Vollzugriff auf alle Schulungsinhalte frei. Bitte wenden Sie sich hierzu an dskit@iitr.de

### RAG Response
Wenn Sie bereits Kunde des Datenschutz-Kits sind und auf weitere eLearning-Module zugreifen möchten, können Sie sich an den Support wenden. Bitte senden Sie eine E-Mail an dskit@iitr.de.

### Source Check
- **PDF p.16:** 11-03 DS-Kit eLearning mentioned, BUT no pricing
- **CSV:** NOT FOUND
- **Result:** "vier Module" and "300€" NOT in corpus

### Client Discussion
Client must provide: eLearning tier pricing (4 standard modules, 300€ for full access).

---

## Q17: Englisch Zertifikat

**Category:** TRUE DATA GAP

### Question
Stellen Sie auch englischsprachige Zertifikate zur Verfügung?

### Expected Answer
Wir können Ihnen gerne ein englischsprachiges Datenschutz-Zertifikat ausstellen, mit welchem Sie Ihre datenschutzrechtliche Befassung nachweisen können. Bitte wenden Sie sich hierzu an dskit@iitr.de

### RAG Response
Die DS-Kit-Plattform bietet standardmäßig keine englischsprachigen Zertifikate an. Sollten Sie dennoch einen englischsprachigen Nachweis benötigen, können Sie sich an das DS-Kit-Team wenden.

### Source Check
- **PDF:** NOT FOUND
- **CSV:** NOT FOUND
- **Result:** English certificate service NOT documented

### Client Discussion
Client must provide: English certificate availability (answer is YES, not NO).

---

## Q18: Login URL

**Category:** TRUE DATA GAP

### Question
Wie erhalte ich einen Zugang zum Datenschutz-Kit?

### Expected Answer
Bitte stellen Sie zunächst sicher, dass die IITR Datenschutz GmbH Ihnen einen Benutzerzugang zum Datenschutz-Kit eingerichtet hat. Sodann können Sie core.iitr.de aufrufen und unter Angabe Ihrer E-Mailadresse einen Login-Link anfordern.

### RAG Response
Um einen Zugang zum Datenschutz-Kit zu erhalten, wenden Sie sich bitte direkt an das IITR Datenschutz GmbH. Sie können uns eine E-Mail an dskit@iitr.de senden.

### Source Check
- **PDF:** NOT FOUND
- **CSV:** NOT FOUND
- **Result:** Login URL "core.iitr.de" NOT in corpus

### Client Discussion
Client must provide: Login URL (core.iitr.de).

---

## Q21: Mitarbeiter Dokumente

**Category:** Q&A DATA ERROR

### Question
Welche Dokumente brauchen wir unterschrieben von den Mitarbeitern?

### Expected Answer
Die Beschäftigten sollten die Verpflichtungserklärung auf Einhaltung des Datenschutzes unterzeichnet haben. Eine entsprechende Vorlage finden Sie unter Kapitel 11, Unterpunkt 2 in Ihrem Datenschutz-Kit.

### RAG Response
Für die Arbeit mit dem Datenschutz-Kit und die Erfüllung der datenschutzrechtlichen Pflichten gibt es mehrere Dokumente...

### Source Data Conflict

**CSV Row 9 says:**
> "Wo finde ich eine Vorlage für eine Datenschutzverpflichtung?"
> → "Eine Vorlage... erhalten Sie im **Kapitel 09** des Datenschutz-Kits."

**PDF Page 15-16 (Chapter 11) says:**
> "Das Dokument **11-01** DS-Kit Verpflichtung Datenschutz dient zur Verpflichtung der mit der Datenverarbeitung beschäftigten Personen auf die Einhaltung des Datenschutzes."

**Verdict:** CSV has wrong chapter number. PDF clearly shows Verpflichtung is in Chapter 11, Document 11-01.

### Client Action Required
**FIXABLE:** Correct CSV Row 9:
- **From:** "Kapitel 09"
- **To:** "Kapitel 11" (matching PDF)

---

## Q24: E-Mail Vereinbarung Video

**Category:** TRUE DATA GAP

### Question
In welchen Videos wird die Personal-Vereinbarung bzgl. E-Mail nach Beendigung besprochen?

### Expected Answer
Eine Vorlage für eine Vereinbarung über den Zugriff auf die E-Mail-Adresse des Beschäftigten nach Beendigung des Beschäftigungsverhältnisses finden Sie in Kapitel 11, Unterpunkt 3 Ihres Datenschutz-Kits.

### RAG Response
Die Information über E-Mails mit privatem Inhalt wird nicht direkt in Verbindung mit Videos im Portal erwähnt.

### Source Check: Expected Answer vs PDF

**Expected answer says:** "Kapitel 11, Unterpunkt 3" has post-employment email agreement

**PDF Chapter 11 actually lists:**
- 11-01: Verpflichtung Datenschutz
- 11-02: Verpflichtung IT
- 11-03: **eLearning overview** (NOT email agreement!)
- 11-06: Hardware im privaten Umfeld
- 11-07: Nutzung unternehmensfremder Hardware
- 11-08: Nutzung Email und Internet

**No post-employment email agreement template described in PDF.**

### Client Discussion
**EXPECTED ANSWER MAY BE WRONG.** The PDF does not describe any post-employment email agreement template in Chapter 11.

Client must clarify:
1. Does this template actually exist?
2. If yes, where is it located?
3. Is the expected answer pointing to wrong location?

---

## Q27: Bewerber Unterschrift

**Category:** TRUE DATA GAP

### Question
Müssen unsere Bewerber unsere Datenschutzbestimmungen unterschreiben?

### Expected Answer
Es ist nicht erforderlich, dass Bewerber Datenschutzhinweise unterschreiben. Bewerber sollten jedenfalls die Datenschutzhinweise zur Kenntnis nehmen.

### RAG Response
Die Frage, ob Bewerber Ihre Datenschutzbestimmungen unterschreiben müssen, wird in den bereitgestellten Dokumenten nicht direkt beantwortet.

### Source Check
- **PDF:** NOT FOUND
- **CSV:** NOT FOUND
- **Result:** Applicant data protection policy NOT in corpus

### Client Discussion
Client must provide: Applicant policy (acknowledge, NOT sign).

---

## Q28: Cookie Banner

**Category:** TRUE DATA GAP

### Question
Ist unser Cookie Banner so in Ordnung?

### Expected Answer
Gerne prüfen wir Ihre Webseite auf Datenschutzkonformität zu unserem Stundensatz von 180€ netto (Abrechnung im 10-Minuten-Takt). Bitte wenden Sie sich dazu an dskit@iitr.de

### RAG Response
Die derzeit genutzte Cookie-Entscheidung mit den Optionen „ich stimme zu" und „ich stimme nicht zu" ist nicht vollständig datenschutzkonform...

### Source Check
- **PDF:** NOT FOUND
- **CSV:** NOT FOUND
- **Result:** Website review service (180€) NOT in corpus

### Client Discussion
Client must provide: Website review service description + pricing (180€).

---

## Action Items for Client

### Must Provide (9 items)
1. **Q6:** Policy for listing IITR as DSB on customer websites
2. **Q12:** Clarification that customer files DSB registration
3. **Q13:** DSB registration contact details
4. **Q16:** eLearning tier pricing (4 modules, 300€ full)
5. **Q17:** English certificate availability
6. **Q18:** Login URL (core.iitr.de)
7. **Q24:** Post-employment email agreement location
8. **Q27:** Applicant data protection policy
9. **Q28:** Website review service description

### Fixable by IITR (2 items)
1. **Q14:** Add PDF header chunk to recover contact details
2. **Q21:** Correct CSV Row 9 chapter reference

### Client Decision (1 item)
1. **Q5:** Feature description vs frequency - which is preferred answer?

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

