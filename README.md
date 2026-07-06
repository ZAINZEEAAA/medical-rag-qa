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

## 🏗️ Architecture
