# medical-rag-qa
🩺 An AI-powered medical Q&amp;A assistant built with Retrieval-Augmented Generation (RAG) — combines semantic search over trusted medical sources (MedQuAD) with LLM-based answer generation for grounded, cited responses.
# 🩺 MediAssist-RAG

**An AI-powered medical information assistant using Retrieval-Augmented Generation (RAG)**

MediAssist-RAG answers medical questions by retrieving relevant information from a curated
knowledge base of trusted medical Q&A pairs (MedQuAD — sourced from NIH, MedlinePlus, GARD,
and other verified health authorities) and generating grounded, context-aware answers using
a language model — rather than relying purely on the model's internal (and potentially
outdated or hallucinated) knowledge.

## ✨ Features

- 🔍 **Semantic Search** — Retrieves the most relevant medical documents using
  sentence-transformer embeddings (PubMedBERT) and FAISS vector search
- 🧠 **Grounded Generation** — Answers are generated strictly from retrieved context,
  reducing hallucination risk
- 📚 **Source Citations** — Every answer includes the source/topic it was derived from,
  for transparency and verifiability
- 🌐 **Web Interface** — Simple Flask-based frontend for asking questions and viewing
  results in real time
- 💾 **Persistence** — Embeddings and FAISS index can be cached to avoid expensive
  recomputation across sessions

## 📝 Project History & Learning Notes

This project went through two development iterations. Both are documented here
for **learning and reference purposes**, so that others can see what kind of
issue can arise in a RAG pipeline and how it was diagnosed and fixed.

### ❌ v1 — Initial Version (Issue Present)

In this version, the RAG pipeline components (`retrieve()`, `generate_text()`,
`rag_answer()`) and the Flask frontend were tested across separate, inconsistent
Colab sessions. Whenever the Colab runtime restarted or disconnected, all
in-memory functions and variables (`documents`, `index`, `embed_model`, etc.)
were lost — however, the Flask app continued to reference these same functions
as if they still existed. This resulted in:

- A generic "Something went wrong" error on the frontend
- Backend errors such as `NameError: name 'rag_answer' is not defined`
- The model failing to generate answers despite being correctly trained,
  simply because the required function was never available to call

**Root cause:** The pipeline's dependent cells were not being executed in a
single, consistent session in the correct order — particularly when only the
data-reload cell was run without redefining the rest of the pipeline
(`retrieve`, `generate_text`, `rag_answer` functions).

### ✅ v2 — Corrected Version (Currently Working)

In this version, the entire pipeline was restructured into a consistent,
dependency-ordered sequence:

1. Load/reload dataset
2. Load embedding model
3. Define `retrieve()` function
4. Load generator model (FLAN-T5)
5. Define `generate_text()` function
6. Define `rag_answer()` function (combines all the above)
7. Define Flask application
8. Start ngrok tunnel and server

Running these cells top-to-bottom in a fresh Colab session now ensures the
entire pipeline works consistently, and the frontend correctly returns
model-generated answers.

### 🎓 Learning Takeaway

When working in stateful-but-ephemeral environments like Google Colab, it is
essential that all dependent functions and variables be defined within a
**single, continuous session flow**. Otherwise, some cells may appear to run
successfully (e.g., loading variables) while the actual pipeline logic that
depends on them is missing — and this kind of issue often only becomes
apparent during full end-to-end testing.

