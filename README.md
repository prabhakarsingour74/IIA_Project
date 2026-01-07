📘 README.md
# HYBRID LEGAL QUERY FEDERATION SYSTEM  
### Intelligent Information Access (IIA) – Task 2(b) + Task 3 Innovation  
### Indian Legal Retrieval using BNS 2023, BNSS 2023, BSA 2023, POCSO, IPC-Equivalent Mapping

---

## 📌 1. Project Overview

This project implements an **AI-powered hybrid legal information access system** designed to answer complex criminal-law queries written in **natural language**.

It integrates:
- **Bharatiya Nyaya Sanhita (BNS), 2023**
- **Bharatiya Nagarik Suraksha Sanhita (BNSS), 2023**
- **Bharatiya Sakshya Adhiniyam (BSA), 2023**
- **POCSO Act, 2012**
- **IPC-equivalent mappings (for historical reference)**  
- **Case law database**

The system supports:
- Crime identification from narratives  
- Section retrieval  
- Offence → Section mapping  
- Legal punishment extraction  
- Multi-source federation  
- Structured LLM reasoning  

---

## 🧠 2. Core Features (What the System Can Do)

### ✔ Natural-language crime story interpretation  
### ✔ Crime extraction (rule + LLM hybrid)  
### ✔ Multi-source federation (BNS, CRPC/BNSS API, case-law)  
### ✔ Safe section extraction (ignores ages, money)  
### ✔ Hybrid semantic ranking:
- Jaccard similarity  
- Edit distance  
- Embedding cosine similarity  
### ✔ Embedding-based candidate expansion  
### ✔ Offence-level section ranking  
### ✔ LLM JSON legal synthesis  
### ✔ Full fallback mode (works even without Gemini API)

This creates a **highly robust legal retrieval engine**.

---

## 🏗 3. System Architecture

The architecture consists of **8 key modules**:

### **1. Query Rewrite Module**
- Extracts keywords  
- Detects intent  
- Identifies explicit legal sections safely  
- Performs minor normalization  

### **2. Hybrid Crime Extraction**
- Rule-based pattern matching  
- LLM JSON-based extraction  
- Combined with provenance  
Example:
"rules": [...],
"llm": [...],
"merged": [...]

### **3. Legal Source Federation**
- Local BNS DB (SQLite)  
- Remote CRPC/BNSS API  
- Case-law JSON DB  

### **4. Hybrid Semantic Similarity (Innovation)**
Hybrid Score = 0.3 * Jaccard
+ 0.3 * Edit Distance
+ 0.4 * Embedding Cosine
This is the major innovation for Task-3.

### **5. Embedding Candidate Expansion**
If few BNS hits (<5), the system:
- Computes embedding similarity against *all* BNS sections  
- Expands candidate set  
- Improves recall drastically  

### **6. Offence → Section Mapping**
For each detected offence (Kidnapping, Rape, Murder, etc.), we compute:
- Section seeds via IPC-to-BNS mapping  
- Keyword search  
- Hybrid reranking  
Output:
offence → [top BNS sections]

### **7. LLM JSON Legal Reasoning**
Gemini / fallback LLM produces:
{
"mappings": [
{
"offence": "...",
"suggested_bns": [...],
"suggested_ipc": [...],
"reason": "..."
}
]
}

### **8. Final Synthesis**
Combines:
- Ranked BNS sections  
- CRPC/BNSS sections  
- Case law  
- Crime provenance  
- LLM reasoning  

---

## 📦 4. File Structure

├── full_pipeline.py # Main file (FULL FEATURE MAX)
├── bns.db # BNS 2023 legal sections (SQLite)
├── legal_cases.json # Case-law database
├── README.md # You are reading this file

---

## 💻 5. Installation

Install dependencies:

```bash
pip install sentence-transformers rapidfuzz numpy requests
pip install google-generativeai   # optional for Gemini API
Set Gemini API key (optional):
export GEMINI_API_KEY="your_key_here"
▶️ 6. Running the System
python full_pipeline.py
Then type any natural-language query such as:
A 16-year-old girl was kidnapped and...
What is punishment for cheating under BNS?
Explain kidnapping for ransom...
The system prints:
Query Rewrite
Hybrid crime extraction
BNS hits and CRPC hits
Case hits
Top-K BNS ranked sections
Structured LLM JSON mapping
Final integrated legal answer
🧪 7. Evaluation (Task-3 Ablation)
We support hybrid evaluation with a JSON test set:
hybrid_eval.json
Format:
[
  {"query": "punishment for rape", "true_section": 65},
  {"query": "cheating by personation", "true_section": 319}
]
Run:
python hybrid_similarity_eval.py
Outputs accuracy for:
Hybrid
Embedding-only
Jaccard-only
Edit-only
