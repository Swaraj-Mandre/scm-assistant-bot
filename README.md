# SCM Assistant Bot — BQBYTE Technologies Supply Chain Chatbot

## Public Chatbot URL
https://cloud.flowiseai.com/chatbot/b146ee8b-489d-4489-914c-697504457bee

## Project Overview
A RAG-based supply chain chatbot built with Flowise that answers questions about supplier performance, SLA compliance, risk levels, penalties, certifications, and governance policy for BQBYTE Technologies.

## Tech Stack
| Component | Tool |
|---|---|
| Platform | Flowise Cloud |
| LLM | Groq : llama-3.3-70b-versatile | 
| Or  | Mistral : mistral-small-latest |
| Embeddings | Cohere — embed-english-v3.0 |
| Vector Store | Qdrant Cloud |
| Data Sources | SupplyChain_Governance_Policy_v3.2.pdf + supplier_summaries.txt |

## Architecture
```
PDF + Supplier Data
        ↓
Document Store (Flowise)
        ↓
Cohere Embeddings (embed-english-v3.0, 1024 dims)
        ↓
Qdrant Vector Store (scm-store collection)
        ↓
Conversational Retrieval QA Chain
        ↓
Groq LLM (llama-3.3-70b-versatile)
        ↓
Public Chatbot URL
```

## Chunk Configurations Tried

### Configuration 1 (Initial)
- PDF: Recursive Character Text Splitter, Chunk Size 1000, Overlap 200 → 8 chunks
- CSV: Recursive Character Text Splitter, Chunk Size 500, Overlap 50 → 4000 chunks
- Result: Too many chunks, retrieval quality poor, LLM context flooded

### Configuration 2 (Final — Used)
- PDF: Recursive Character Text Splitter, Chunk Size 1000, Overlap 200 → 19 chunks
- Supplier Summaries: Recursive Character Text Splitter, Chunk Size 500, Overlap 50 → 126 chunks
- Analytical Summaries: No splitter → 5 chunks (one per key business question)
- Total: 150 chunks
- Result: Precise retrieval, accurate answers

## Sample Q&A

**Q1: Which Tier-3 suppliers have an active disruption flag, and what response level applies per policy?**

16 Tier-3 suppliers have active disruption flags. High Risk suppliers trigger Level 3 Activate per Policy §9 (immediate CPO escalation, alternate supplier at minimum 40% volume, safety stock +50%, full RCA within 15 business days). Medium Risk suppliers trigger Level 2 Manage (bi-weekly escalation calls, safety stock +30%, alternate on 48-hour notice).

High Risk (Level 3): SUP-010 Bohai Electronics, SUP-011 Dravex Components India, SUP-017 Sahyadri Alloy Tech, SUP-022 DaNang Metal Works, SUP-041 Archipelago PCB Corp, SUP-075 Vistula Pack Sp, SUP-079 Helios Pack Greece, SUP-089 Yucatan Polymer Mfg, SUP-094 Bogota Pack Ltda, SUP-095 Cerromax Mineria, SUP-096 Lima Polymer SA

Medium Risk (Level 2): SUP-018 Deltaforge Vietnam, SUP-020 MeKong Pack Co, SUP-042 Visayas Textile Co, SUP-078 Varna Electronics EAD, SUP-080 Maghreb Castworks

**Q2: Which suppliers qualify for the annual Volume Rebate Program and how many are there?**

5 suppliers qualify per Policy §4.2 (Tier-1 + OTD ≥ 93% + Defect < 0.5% + Sustainability ≥ 85):
SUP-028 Hokkaido Alloy Tech, SUP-044 Broken Hill Alloys, SUP-049 Munich Alloy Technik, SUP-060 Nordloom Finland Oy, SUP-102 Crestline Chemical Supply.

**Q3: Which region has the highest total PO value, and does it breach the concentration limit?**

APAC has the highest spend at $131,620,356.14 (37.0% of total $356,045,248.18). No breach — below the 45% regional concentration cap per Policy §5.3.

**Q4: Which suppliers are on Supplier Watch List (SWL) status and what does it restrict?**

9 suppliers have Compliance Score below 60 and are on SWL status per Policy §3.4. SWL restricts new PO issuance to 20% of prior quarter volume: SUP-014 Deccan Polymer Mfg, SUP-017 Sahyadri Alloy Tech, SUP-020 MeKong Pack Co, SUP-022 DaNang Metal Works, SUP-078 Varna Electronics EAD, SUP-089 Yucatan Polymer Mfg, SUP-092 Buenos Aires Pack, SUP-095 Cerromax Mineria, SUP-096 Lima Polymer SA.

**Q5: Which product category has the highest average defect rate and does it exceed the Tier-2 limit?**

Packaging Materials has the highest average defect rate at 1.91% across all POs. Below the Tier-2 ceiling of 2.50% per Policy §3.2 — no breach, but approaching the limit.

## What I Would Improve
1. **Replace supplier_summaries.txt with direct CSV ingestion** — current Flowise Cloud free tier times out on 2000-row CSV upsert. Self-hosted Flowise or paid tier would handle this properly.
2. **Add metadata filtering** — tag each chunk with supplier tier, region, risk level for precise filtered retrieval instead of semantic search alone.
3. **Increase Top K dynamically** — for aggregate queries (list all X), use higher K; for specific lookups, use lower K.
4. **Add conversation memory persistence** — current setup uses in-session memory only.
5. **Hybrid search** — combine semantic search with keyword search for exact supplier ID lookups.
