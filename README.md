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

Yeh project develop karte waqt do versions se guzra hai — dono is repo mein
**sirf learning/reference purpose** ke liye rakhe gaye hain, taake koi bhi
seekhne wala dekh sake ke ek RAG pipeline mein kya masla aa sakta hai aur
usko kaise fix kiya jata hai.

### ❌ v1 — Initial Version (Issue wali)
Is version mein RAG pipeline (`retrieve()`, `generate_text()`, `rag_answer()`)
aur Flask frontend alag alag Colab sessions mein test kiye gaye the. Jab bhi
Colab runtime restart/disconnect hota, saari functions aur variables
(`documents`, `index`, `embed_model`, etc.) memory se udd jate the — lekin
Flask app phir bhi unhi functions ko call karne ki koshish karta raha, jiski
wajah se:

- Frontend par "Something went wrong" ka generic error aata tha
- Backend mein `NameError: name 'rag_answer' is not defined` jaisi errors aati thi
- Model sahi trained hone ke bawajood answers generate nahi ho rahe the,
  kyunke function hi call nahi ho pa raha tha

**Root cause:** Colab session ki saari cells ek hi order mein, ek hi session
mein run na hona — especially jab reload/persistence wali cell chalayi jati
thi lekin baaki pipeline (retrieve/generate/rag_answer functions) dobara
define nahi hoti thi.

### ✅ v2 — Corrected Version (Ab working)
Is version mein poori pipeline ko ek consistent order mein structure kiya gaya:

1. Dataset load/reload
2. Embedding model load
3. `retrieve()` function define
4. Generator model (FLAN-T5) load
5. `generate_text()` function define
6. `rag_answer()` function define (jo upar ke sab functions ko combine karta hai)
7. Flask app definition
8. Ngrok tunnel + server start

Ab har naye Colab session mein yeh saari cells top-se-bottom run karne se
poora pipeline consistently kaam karta hai, aur frontend model se sahi
answers le kar dikhata hai.

### 🎓 Learning Takeaway
Colab jaise stateful-but-ephemeral environments mein kaam karte waqt yeh
zaroori hai ke saari dependent functions/variables **ek hi session ki flow**
mein define ho — warna kuch cells "kaam karti dikhengi" (variables load ho
jayengi) lekin actual pipeline (jo un variables ko use karti hai) missing
rahegi, aur error sirf tab pata chalega jab end-to-end test karenge.
