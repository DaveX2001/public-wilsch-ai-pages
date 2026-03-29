---
publish: true
---

# Pflichtenheft: DS-Kit Navigationssystem
[[client-iitr]]

*Technische Spezifikation für AI-gestütztes POC-Projekt*

---

## 1. Deckblatt

| | |
|---|---|
| **Projekt** | DS-Kit Navigationssystem |
| **Auftraggeber** | IITR Datenschutz GmbH |
| **Auftragnehmer** | Wilsch AI Services OÜ |

---

## 2. Zielbestimmung

### Must (POC Scope)
- RAG-System beantwortet DS-Kit Navigationsfragen ("Wo finde ich X?")
- Streaming-Antworten in Echtzeit (Typesense native RAG)
- Konversationsverlauf mit Follow-up Fragen
- Chat-Interface via OpenWebUI
- Verarbeitung aller Materialien:
  - PDF-Leitfaden (März 2025): Direkte Textextraktion
  - Excel Q&A: Frage-Antwort-Paare
  - ZIP-Vorlagen (56 Dateien): Vision-basierte Zusammenfassungen als Preprocessing
- Text-only Ausgabe (sprachfähig, keine URLs/Bilder/Anhänge)
- Ausgabeformat entspricht Excel "Antwort"-Spalte
- Evaluation: >90% korrekte Antworten auf Testfragen.xlsx
- Deployment auf IITR-STAGING (136.243.71.58)

### Deferred (Post-POC)
- Integration mit bestehendem Urteile RAG System
- Erweiterte Prompt-Optimierung via Langfuse

### Out of Scope
- Bilder/Screenshots in Antworten
- Dokument-Verlinkung / URL-Referenzen
- OCR für gescannte Dokumente
- Keycloak-Authentifizierung
- Navigationspfad-Visualisierung

---

## 3. Funktionale Anforderungen

### Must (POC Scope)

**RAG-System beantwortet DS-Kit Navigationsfragen**
- **FR-01:** System nimmt Benutzerfragen in natürlicher Sprache entgegen und liefert relevante Antworten aus dem DS-Kit Korpus.
- **FR-02:** Antworten werden in Echtzeit gestreamt (Token für Token).
- **FR-03:** System speichert Konversationsverlauf und ermöglicht Follow-up Fragen mit Kontextbezug.

**Chat-Interface**
- **FR-04:** OpenWebUI dient als Chat-Interface für Benutzerinteraktion.

**Verarbeitung aller Materialien**
- **FR-05:** PDF-Leitfaden (März 2025) wird vollständig extrahiert und für semantische Suche indexiert.
- **FR-06:** Excel Q&A-Paare werden als Referenz-Antworten integriert.
- **FR-07:** ZIP-Vorlagen (56 Dateien) werden via Vision-Modell zusammengefasst und als Dokumenten-Index indexiert.

**Text-only Ausgabe**
- **FR-08:** Antworten enthalten ausschließlich Text (keine URLs/Bilder/Anhänge).
- **FR-09:** Antwortformat orientiert sich am Stil der Excel "Antwort"-Spalte (z.B. "...finden Sie in Kapitel X").

**Qualitätssicherung**
- **FR-10:** System erreicht >90% korrekte Antworten auf Testfragen.xlsx Evaluationsdatensatz.

### Deferred (Post-POC)
- **FR-11:** Integration mit bestehendem Urteile RAG System für kombinierte Suche.

---

## 4. Nicht-funktionale Anforderungen

### Performance
- First-Token Latenz: < 4 Sekunden nach Anfrage
- Vollständige Antwort: < 15 Sekunden für typische Navigationsfragen

### Security
- Authentifizierung via OpenWebUI (aktivierbar für Client-Tests)
- Daten verbleiben auf IITR-STAGING Server (keine Cloud-Übertragung)

### Compatibility
- Deutsche Sprache: Vollständige Unterstützung für Fragen und Antworten
- Embedding-Modell: Deutsch-optimiert

### Usability
- Chat-Interface: Konversationelle Interaktion ohne technische Vorkenntnisse
- Mobile-kompatibel: Responsive Web-Interface

---

## 5. Abnahmekriterien

### FR-01 Navigation Fragen (Beispiele aus Excel)

| Test | Erwartetes Ergebnis |
|------|---------------------|
| "Wo finde ich die Texte für die Webseitendatenschutzerklärung?" | Antwort referenziert Kapitel 8 |
| "Wo finde ich eine Vorlage für eine Datenschutzverpflichtung?" | Antwort referenziert Kapitel 09 |
| "Wo finde ich Mustervorlagen für AV-Verträge?" | Antwort referenziert Kapitel 05 |

### FR-02 Streaming
- Test: Anfrage wird gesendet
- Erwartetes Ergebnis: Erste Tokens erscheinen < 4 Sekunden

### FR-03 Follow-up
- Test: Nach Frage zu Kapitel 05 fragt Benutzer "Was muss da rein?"
- Erwartetes Ergebnis: System antwortet mit Bezug zu AV-Verträgen

### FR-04 OpenWebUI
- Test: Benutzer öffnet System-URL
- Erwartetes Ergebnis: Chat-Interface ist zugänglich

### FR-05-07 Materialien
- Test: Frage zu ZIP-Vorlage
- Erwartetes Ergebnis: Relevantes Dokument wird referenziert

### FR-08-09 Output
- Test: Beliebige Anfrage
- Erwartetes Ergebnis: Nur Text, keine URLs/Bilder

### FR-10 Evaluation
- Test: Alle Fragen aus Testfragen.xlsx
- Erwartetes Ergebnis: >90% korrekt beantwortet

---

## 6. Technische Rahmen- und Randbedingungen

### Deployment Environment
- Server: IITR-STAGING (136.243.71.58)
- Container-basiert: Docker Compose

### Required Integrations
- Typesense: Vector-Datenbank und native Conversational RAG
- Ollama: Lokale LLM-Inferenz (via OpenAI-kompatible API)
- Ollama oder TEI: Embedding-Generierung (deutsch-optimiert)

### Technology Stack
- Chat-Interface: OpenWebUI
- RAG-Orchestrierung: OpenWebUI Pipeline → Typesense Conversational Search
- Preprocessing: Vision-Modell für ZIP-Template-Zusammenfassungen

---

## 7. Datenmodell

*Nicht anwendbar für dieses POC - Typesense Vector Store statt traditioneller Datenbank.*

---

## 8. Fachbegriffe (Domain Glossary)

| Begriff | Erklärung |
|---------|-----------|
| DSGVO | Datenschutz-Grundverordnung |
| TOM | Technische und Organisatorische Maßnahmen |
| AV-Vertrag | Auftragsverarbeitungsvertrag |
| DS-Kit | Datenschutz-Kit (IITR Compliance-Plattform) |
| RAG | Retrieval Augmented Generation |

---

## 9. Evaluation Infrastructure

### Test Datasets
- **Primär:** Testfragen.xlsx (28 Fragen) - Evaluationsdatensatz
- **Sekundär:** Fragen Navigationssystem.xlsx (15 Q&A-Paare) - Referenz-Antworten

### Success Metrics
- Primär: >90% korrekte Antworten auf Testfragen.xlsx
- Sekundär: Antworten entsprechen dem Stil der Excel "Antwort"-Spalte

### Validation
- Antwort muss korrektes Kapitel referenzieren

---

*WILSCH AI SERVICES OÜ | Harju maakond, Tallinn, Sepapaja tn 6, 15551*

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

