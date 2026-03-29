---
publish: true
---

# Project Portfolio Documentation

[[business-intelligence]]
## [Developer Name]

---

**⚠️ Confidentiality Notice**

This document contains information about client projects that are subject to Non-Disclosure Agreements (NDAs). For this reason, client names, specific project designations, and identifying details have been anonymized. The technical details and business results correspond to real projects.

---

## General Information

### 1. Project Activity Period
**From April 25, 2024 to present**

Continuously involved in projects since this time, with possible minor gaps.

### 2. Number of Completed Projects
**Total of ~11 Client Projects**

Of which 4 are documented as most important/impactful case studies (anonymized under NDA):
- Legal Research System v1 (Project A)
- Legal Research System v2 (Project B)
- Enterprise Software Integration (Project C)
- Invoice Agent (Project D - Internal Tool)

### 3. Project Types
**Primarily AI (64%) and Web (36%)**

- **7 AI Projects:** RAG Systems, Entity Extraction, Natural Language Interfaces, AI Agents
- **4 Web Projects:** Frontend Applications, Landing Pages, Business Tools

### 4. Working Style
**Predominantly solo (8 of 11 projects)**

**Exceptions (in team/with others):**
- Invoice Agent (Internal Tool)
- 2 additional web projects

### 5. Role
**All roles in one:**
- Developer
- Tech Lead
- DevOps
- Client Contact (Customer Relations)

**Context:** As solo developer, covered all areas

---

## Work Methodology

### Fundamental Solution Approach

**Process Overview:**

#### 1. Initial Conversation (free)
- Client freely describes their problem or use case
- No preparation on my part - active listening and problem understanding
- Prerequisite: Client has concrete use case or describable problem

#### 2. Workshop 1 (90-120 minutes, structured)

Standardized 6-step pattern for Requirements Discovery:

1. **Success Criteria / Use Case Definition** - What does success mean for the AI system?
2. **Data Source Identification** - Where is relevant data located, how is it structured?
3. **AI Validator Assignment** - Who validates AI output as domain expert?
4. **POC Environment Specification** - Cloud, On-Premise or Hybrid? Data protection requirements?
5. **Output Format Definition** - How should results be presented/integrated?
6. **Commercial Framework** - Budget, Timeline, next steps

#### 3. Workshop 2 (if needed)
- Deep dive into complex technical or organizational requirements

#### 4. Proof of Concept (mostly Fixed Price)
- Evaluation-driven: Test infrastructure before optimization
- Iterative development with domain expert feedback
- Validation of feasibility and success criteria

#### 5. Production Deployment (Fixed Price or Time & Material)
- Depending on scope and project requirements
- Scaling and integration into existing systems

**Methodology Focus:** Business-requirements-driven workshops with clear deliverables per phase, technical implementation is evaluation-driven with measurable success criteria.

---

## Personal Reflection

### Greatest Positive Impact of My Work

**Making the Possibility Space Visible:**

Customers often know they have problems or inefficient processes, but have no idea whether and how AI could solve them. My role is to open this possibility space - to show them what is technically feasible, even before they think about AI.

**AI as "Employee", Not as Product:**

In the current phase, there are no standardized off-the-shelf AI solutions. AI behaves like an employee: You don't simply buy an employee, you hire and train them. I help customers understand and navigate this process - from problem definition through the training phase to productive use.

**Bridge Builder Between Business Problem and Technical Possibility:**

The most difficult part for many companies is not the implementation, but comprehending what is possible at all. I translate between business problems and AI capabilities and make abstract potentials tangibly concrete.

### Example of Problem Solving

All four main projects (Project A, B, C, D) represent complete problem solutions that I independently developed as Tech Lead/Solo-Developer - not partial contributions to larger teams.

**Reference to previous answers:**
- **Project A/B:** Automation of legal research, 85% of inquiries, new business opportunities
- **Project C:** Market expansion through democratization, new customer acquisition confirmed
- **Project D:** Format-agnostic entity extraction as core competency

Each project solved a business-critical problem completely - from requirements to production deployment.

---

## Most Important Projects (Case Studies)

I would designate the following 4 projects as the most important or formative:

1. **Project A** - Legal Research System v1 (RAG-based Q&A)
2. **Project B** - Legal Research System v2 (Case Law Integration)
3. **Project C** - Enterprise Software Integration (Natural Language Interface)
4. **Project D** - Invoice Agent (Entity Extraction System - Internal Tool)

---

# Detailed Project Documentation

## Project A - Legal Research System v1

### Executive Summary

**Customer Challenge**

A law firm specializing in data protection law (major city, Germany) faced a critical resource bottleneck: Over 50 routine GDPR inquiries per week consumed approximately 25 hours of senior legal experts' time and prevented them from focusing on complex, high-value mandates.

**Delivered Solution**

Development of an AI-powered legal assistant that automatically answers standardized data protection questions with legal precision. The system uses advanced language processing to search relevant GDPR documentation and generate precise German-language answers in under 3 seconds.

**Business Impact**

The solution achieved 85% automation of incoming inquiries with a reported answer accuracy of 97%, freeing substantial expert capacity for premium client work. The system operates 24/7 with minimal supervision and processes unlimited parallel inquiries without quality loss.

**Technical Foundation**

Built on secure on-premise infrastructure with complete data sovereignty. The architecture combines vector database technology for intelligent document search with a specialized German language model optimized for legal terminology. Multi-environment setup (development, staging, production) ensures reliable deployments and continuous quality improvement.

### Project Details

**Timeline:** February 2025 - September 2025

**Time Investment:** ~100 hours (not tracked, estimated)

**Main Goal/Problem:**
RAG system for German GDPR documentation. Enables question-answer interactions on data protection guidelines via vector database and German language model.

**Technologies/Frameworks/Tools:**
- Qdrant (Vector DB)
- Qwen 2.5 14B (LLM)
- Ollama (LLM Server)
- OpenWebUI (Chat Interface)
- Flask API (Orchestration)
- Langfuse, OpenLit (Monitoring)
- Docker Compose

**Compensation Model:** Fixed Price

**GitHub Repository:** [Organization Repository - Client v1]

---

## Project B - Legal Research System v2

### Executive Summary

**Customer Challenge**

The existing GDPR documentation system (v1) provided precise FAQ-based answers from data protection guidelines. However, for well-founded legal argumentation, lawyers additionally needed access to precedent cases from case law. Manual search through thousands of court judgments was time-consuming and correct citation was legally mandatory.

**Delivered Solution**

Development of a complementary research system for 2,567 German court judgments. The system enables semantic search for relevant precedent cases and automatically extracts correct citations. While v1 provides direct answers from documentation, v2 provides relevant court decisions that lawyers can interpret themselves and use in their argumentation.

**Business Impact**

The combination of both systems enables complete legal research: v1 as primary source for regulations, v2 as fallback for case law and precedents. Lawyers receive not only answers from regulations but also access to judicially confirmed legal interpretations with precise, citable references for professional legal work.

**Technical Foundation**

Separate system with hybrid search (semantic + full-text) for optimal precision in legal case research. Specialized citation extraction ensures legally compliant source references. Architecture complements v1 system without dependencies, both data sources remain independently maintainable and expandable.

### Project Details

**Timeline:** August 2025 - ongoing

**Time Investment:** ~380 hours (189h tracked + ~190h estimated before tracking)

**Main Goal/Problem:**
Complementary integration of German court judgments (2,567 cases) as fallback data source. Enables search for relevant precedent cases when FAQ-based GDPR documentation provides no direct answer. Lawyers interpret judgments themselves - system delivers precise citations for legal argumentation.

**Technologies/Frameworks/Tools:**
- Typesense (Hybrid Search)
- TEI + Qwen3-8B-4096 (Embeddings)
- Ollama (LLM Server)
- OpenWebUI (Chat Interface)
- Custom Python Pipelines
- Chunkr (Document Processing)
- Langfuse (Monitoring)
- Docker Compose

**Compensation Model:** Time & Material (Hourly Basis)

**Learning Effects:**
RAG Systems - Retrieval Augmented Generation as fundamental AI component. Practical experience with real use cases in production environments.

**Value Created:**
- 85% automation of routine inquiries, 97% answer accuracy achieved
- Release of substantial expert capacity for complex, high-value mandates
- System operates 24/7 and enables complete legal research (regulations + case law)
- **Time Savings:** Automated answering - lawyers can still bill
- **New Customer Acquisition:** Additional subscription feature enables acquisition of new clients
- **Scalability:** 24/7 availability without additional personnel costs
- **Revenue Potential:** Released capacity for premium mandates

**Customer Feedback:**
Ongoing monthly Time & Material contract - continuous commissioning and payment over several months confirms sustained value and satisfaction.

**GitHub Repository:** [Organization Repository - Client v2]

---

## Project C - Enterprise Software Integration

### Executive Summary

**Customer Challenge**

An established facility management platform was designed exclusively for professional building managers with domain expertise. Non-technical personnel (office employees, janitors, service workers) could not create maintenance requests because complex navigation and technical terminology prevented this. This significantly limited market reach and excluded a large portion of potential users.

**Delivered Solution**

Development of an AI-powered conversational interface that replaces domain expertise with natural language. Users describe problems in everyday language ("coffee machine broken"), while the system automatically identifies assets, creates work orders, and communicates with the Facility Management Platform REST API. User confirmation before each action ensures control and accuracy.

**Business Impact**

Market expansion from professional facility managers to all building-associated personnel. Opens up untapped market segment without technical barriers. Competitive advantage through local/on-premise deployment capability, critical for German and European customers. Three revenue paths identified: Platform-as-Service Hosting, On-Premise Partner Deployments, strategic partnerships.

**Technical Foundation**

Model Context Protocol (MCP) integration with Facility Management Platform API. FastMCP Python Server enables standardized data access and workflow automation. Progressive asset search with intelligent disambiguation. POC status validated through successful demo (July 2025), production deployment on remote infrastructure planned (LibreChat).

### Project Details

**Timeline:** June 2025 - September 2025

**Time Investment:** ~108 hours

**Main Goal/Problem:**
AI-powered language interface for facility management software. Enables non-technical building users to create maintenance requests through natural conversation, without domain knowledge in building management. Expands addressable market from professional facility managers to all building-associated personnel.

**Technologies/Frameworks/Tools:**
- FastMCP (Model Context Protocol)
- Python/FastAPI (Backend)
- Facility Management Platform REST API (Integration)
- LibreChat (UI Interface - deployed)
- Docker + uvicorn

**Compensation Model:** Fixed Price

**Learning Effects:**
Model Context Protocol (MCP) and AI Agents - two central areas within AI development. Many projects build on these fundamental components; real practical implementation was particularly valuable.

**Value Created:**
- **Market Expansion:** New business opportunities through democratization of facility management software
- **Competitive Advantage:** Language interface lowers entry barrier - opens up user groups competitors cannot reach
- **Acquisition Tool:** Application enables active new customer acquisition through expanded product offering
- Customer has repeatedly given positive feedback
- Confirmed that the work helped him open up new business opportunities
- Currently in concrete discussions for new business due to the developed application

**Customer Feedback:**
Two documented meeting transcripts with explicitly positive feedback on work quality. Customer specifically confirmed that the work contributed to opening up new business opportunities.

**GitHub Repository:** [Organization Repository - Client]

---

## Project D - Invoice Agent (Internal Tool)

### Executive Summary

**Customer Challenge**

Unstructured invoice documents (PDFs with varying formats, layouts, and suppliers) must be converted into structured database entries for accounting systems. Manual data entry is error-prone, time-consuming, and not scalable. Traditional template-based approaches fail with format variance and require extensive maintenance for each new supplier.

**Delivered Solution**

Reference implementation for AI-powered entity extraction from unstructured documents. LLM-based system reliably extracts structured entities (supplier, amounts, line items, tax codes) from any invoice format without template configuration. Fully integrated into production workflow with email polling, Supabase data storage, web interface for cost center assignment, and automated CSV export for external accounting system (DCW).

**Business Impact**

Demonstrates production-ready format-agnostic entity extraction as core competency for document processing use cases. While invoice automation as workflow is replicable, the project demonstrates the technical ability to reliably transform unstructured documents into structured business data. This competency opens broader market opportunities for any document-based business processes (contracts, orders, delivery notes).

**Technical Foundation**

LLM-based entity extraction via OpenRouter API with structured prompts. FastAPI backend for processing pipeline, Supabase for structured data storage, React frontend for business workflows. Langfuse integration for tracing and quality assurance of extraction results. Multi-entity extraction (header + line items + metadata) with business context mapping for accounting system integration.

### Project Details

**Timeline:** April 2025 - ongoing

**Time Investment:** ~137 hours (91.5h tracked * 1.5)

**Main Goal/Problem:**
AI-powered automation of invoice processing. System monitors email inbox, extracts invoice data from PDFs via LLM, stores structured data in database, and generates CSV exports for external accounting system (DCW). Replaces manual data entry with fully automated pipeline including web interface for cost center assignment.

**Technologies/Frameworks/Tools:**
- FastAPI + Supabase (Backend)
- OpenRouter API (AI Processing)
- React + Vite (Frontend)
- TanStack Query + Tailwind + shadcn/ui
- IMAP-tools (Email Polling)
- PyMuPDF (PDF Processing)
- Langfuse (Monitoring)
- Docker + Vercel

**Compensation Model:** Time & Material (Hourly Basis)

**GitHub Repository:** [Personal Repository - Invoice Agent]

---

## Summary

Over a period from April 2024 to present, I have completed 11 client projects in AI and web development. The four most important projects demonstrate my capabilities in:

- **RAG Systems** (Project A/B): Retrieval Augmented Generation for legal research
- **AI Agents & MCP** (Project C): Natural Language Interface for enterprise software
- **Entity Extraction** (Project D): Format-agnostic document processing

My role as Solo-Developer/Tech Lead encompasses all aspects from requirements gathering through technical implementation to production deployment. The workshop-driven approach enables rapid project starts with clear success criteria.

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

