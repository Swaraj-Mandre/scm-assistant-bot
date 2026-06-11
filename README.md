# SCM Assistant Bot — BQBYTE Technologies Supply Chain Chatbot

## Public Chatbot URL
https://cloud.flowiseai.com/chatbot/b146ee8b-489d-4489-914c-697504457bee

## Project Overview
A RAG-based supply chain chatbot built with Flowise that answers questions about supplier performance, SLA compliance, risk levels, penalties, certifications, and governance policy for BQBYTE Technologies.

## Tech Stack
| Component | Tool |
|---|---|
| Platform | Flowise Cloud |
| LLM | Mistral — mistral-small-latest |
| Embeddings | Cohere — embed-english-v3.0 |
| Vector Store | Qdrant Cloud |
| Data Sources | SupplyChain_Governance_Policy_v3.2.pdf + supplier_summaries.txt |

>> Used Groq's "llama-3.3-70b-versatile initially" , but switched to Mistral due to free-tier token exhaustion.

## Architecture
```
PDF + Supplier Data
        ↓
Document Store (Flowise)
        ↓
Cohere Embeddings (embed-english-v3.0, 1024 dims)
        ↓
Qdrant Vector Store (scm-store collection, 150 chunks)
        ↓
Conversational Retrieval QA Chain
        ↓
Mistral LLM (mistral-small-latest)
        ↓
Public Chatbot URL
```

## Chunk Configurations Tried

### Configuration 1 (Initial)
- PDF: Recursive Character Text Splitter, Chunk Size 1000, Overlap 200 → 8 chunks
- CSV: Recursive Character Text Splitter, Chunk Size 500, Overlap 50 → 4000 chunks
- Result: Too many chunks, retrieval quality poor, LLM context flooded with irrelevant rows

### Configuration 2 (Final — Used)
- PDF: Recursive Character Text Splitter, Chunk Size 1000, Overlap 200 → 19 chunks
- Supplier Summaries: Recursive Character Text Splitter, Chunk Size 500, Overlap 50 → 126 chunks
- Analytical Summaries: No splitter → 5 chunks (one pre-computed answer per key business question)
- Total: 150 chunks
- Result: Precise retrieval, accurate and complete answers

## Sample Q&A (Verbatim Bot Answers)

**Q1: Which Tier-3 suppliers have an active disruption flag, and what response level applies per policy?**

There are 16 Tier-3 suppliers with active disruption flags. All High Risk suppliers trigger Level 3 Activate per Policy Section 9. Medium Risk suppliers trigger Level 2 Manage.

1. SUP-010 (Bohai Electronics) - Risk: High, Disruption: Typhoon season disruption, Response: Level 3 Activate
2. SUP-011 (Dravex Components India) - Risk: High, Disruption: Typhoon season disruption, Response: Level 3 Activate
3. SUP-017 (Sahyadri Alloy Tech) - Risk: High, Disruption: Export license risk, Response: Level 3 Activate
4. SUP-018 (Deltaforge Vietnam) - Risk: Medium, Disruption: Port congestion risk, Response: Level 2 Manage
5. SUP-020 (MeKong Pack Co) - Risk: Medium, Disruption: Geopolitical tension flag, Response: Level 2 Manage
6. SUP-022 (DaNang Metal Works) - Risk: High, Disruption: Typhoon season disruption, Response: Level 3 Activate
7. SUP-041 (Archipelago PCB Corp) - Risk: High, Disruption: Labour strike risk, Response: Level 3 Activate
8. SUP-042 (Visayas Textile Co) - Risk: Medium, Disruption: Port congestion risk, Response: Level 2 Manage
9. SUP-075 (Vistula Pack Sp) - Risk: High, Disruption: Regulatory enforcement pending, Response: Level 3 Activate
10. SUP-078 (Varna Electronics EAD) - Risk: Medium, Disruption: Port congestion risk, Response: Level 2 Manage
11. SUP-079 (Helios Pack Greece) - Risk: High, Disruption: Labour strike risk, Response: Level 3 Activate
12. SUP-080 (Maghreb Castworks) - Risk: Medium, Disruption: Tariff adjustment pending, Response: Level 2 Manage
13. SUP-089 (Yucatan Polymer Mfg) - Risk: High, Disruption: Port closure risk, Response: Level 3 Activate
14. SUP-094 (Bogota Pack Ltda) - Risk: High, Disruption: Labour strike risk, Response: Level 3 Activate
15. SUP-095 (Cerromax Mineria) - Risk: High, Disruption: Audit overdue; compliance review, Response: Level 3 Activate
16. SUP-096 (Lima Polymer SA) - Risk: High, Disruption: Audit overdue; compliance review, Response: Level 3 Activate

Note: Response levels per Policy Section 9 — High Risk suppliers with any disruption flag trigger Level 3 Activate; Medium Risk suppliers trigger Level 2 Manage.

---

**Q2: Which suppliers qualify for the annual Volume Rebate Program and how many are there?**

5 suppliers qualify for the Volume Rebate Program per Policy Section 4.2.
Criteria: Tier-1 + OTD >= 93% + Defect Rate < 0.5% + Sustainability Score >= 85.

- SUP-028 (Hokkaido Alloy Tech): OTD=97.1%, Defect=0.28%, Sustainability=97
- SUP-044 (Broken Hill Alloys): OTD=94.5%, Defect=0.47%, Sustainability=94
- SUP-049 (Munich Alloy Technik): OTD=93.9%, Defect=0.42%, Sustainability=93
- SUP-060 (Nordloom Finland Oy): OTD=96.8%, Defect=0.4%, Sustainability=90
- SUP-102 (Crestline Chemical Supply): OTD=93.8%, Defect=0.39%, Sustainability=92

---

**Q3: Which region has the highest total PO value, and does it breach the concentration limit?**

APAC has the highest total PO value at $131,620,356.14 (37.0% of total spend $356,045,248.18).
Policy Section 5.3 limits any single region to a maximum of 45% of total procurement spend.
37.0% ≤ 45% → No breach.

Regional spend breakdown:
- APAC: $131,620,356.14 (37.0%)
- EMEA: $94,122,434.32 (26.4%)
- LATAM: $83,971,664.94 (23.6%)
- NA: $46,330,792.78 (13.0%)

---

**Q4: Which suppliers are on Supplier Watch List (SWL) status and what does it restrict?**

9 suppliers are on Supplier Watch List (SWL) status per Policy Section 3.4.
SWL criteria: Compliance Score below 60. Restriction: New PO issuance limited to 20% of prior quarter volume.

- SUP-014 (Deccan Polymer Mfg): Compliance Score=57, Tier=Tier-3, Risk=Low
- SUP-017 (Sahyadri Alloy Tech): Compliance Score=52, Tier=Tier-3, Risk=High
- SUP-020 (MeKong Pack Co): Compliance Score=52, Tier=Tier-3, Risk=Medium
- SUP-022 (DaNang Metal Works): Compliance Score=54, Tier=Tier-3, Risk=High
- SUP-078 (Varna Electronics EAD): Compliance Score=58, Tier=Tier-3, Risk=Medium
- SUP-089 (Yucatan Polymer Mfg): Compliance Score=53, Tier=Tier-3, Risk=High
- SUP-092 (Buenos Aires Pack): Compliance Score=54, Tier=Tier-3, Risk=Low
- SUP-095 (Cerromax Mineria): Compliance Score=53, Tier=Tier-3, Risk=High
- SUP-096 (Lima Polymer SA): Compliance Score=52, Tier=Tier-3, Risk=High

---

**Q5: Which product category has the highest average defect rate and does it exceed the Tier-2 limit?**

Packaging Materials has the highest average defect rate at 1.91%.
Tier-2 maximum permissible defect rate per Policy Section 3.2 is 2.50%.
1.91% ≤ 2.50% → No breach.

Category average defect rates:
- Packaging Materials: 1.91%
- Mechanical Components: 1.89%
- Raw Materials: 1.71%
- Industrial Textiles: 1.71%
- Specialty Alloys: 1.61%
- Electronic Components: 1.59%

---

## What I Would Improve
1. **Replace supplier_summaries.txt with direct CSV ingestion** — Flowise Cloud free tier times out on 2000-row CSV upsert. Self-hosted Flowise or paid tier would handle this natively.
2. **Add metadata filtering** — tag each chunk with supplier tier, region, risk level for precise filtered retrieval instead of relying on semantic search alone.
3. **Dynamic Top K** — use higher K for aggregate queries (list all matching X), lower K for specific supplier lookups.
4. **Conversation memory persistence** — current setup uses in-session memory only; a persistent store (Redis/Postgres) would enable cross-session context.
5. **Hybrid search** — combine semantic search with BM25 keyword search for exact supplier ID lookups (e.g. "tell me about SUP-042").
