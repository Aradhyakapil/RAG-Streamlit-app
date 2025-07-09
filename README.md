https://github.com/user-attachments/assets/0a3a3998-83ec-4198-8c2e-8cf6eca07f55

# RAG PDF Chatbot

A Streamlit-based Retrieval-Augmented Generation (RAG) chatbot that enables users to interactively query the contents of any PDF document. Leveraging OpenAI’s GPT‑4o‑mini for embeddings and chat completion, PyPDF2 for text extraction, FAISS for efficient similarity search, and SentenceSplitter for chunking, this application transforms static PDFs into conversational interfaces.

---

## 🚀 Key Features

* **PDF Text Extraction**: Parses multi-page PDFs using PyPDF2 and concatenates page-level text.
* **Intelligent Chunking**: Splits extracted text into \~500‑character chunks via SentenceSplitter for optimal embedding granularity.
* **Embeddings & Vector Indexing**: Uses OpenAI’s `text-embedding-3-small` to generate vector embeddings, stored in a FAISS `IndexFlatL2` for fast nearest-neighbor retrieval.
* **Retrieval-Augmented Generation**: Retrieves top‑k relevant chunks as context and passes them to GPT‑4o‑mini to generate accurate, context-aware answers.
* **Stateful Conversation**: Maintains the last 20 message turns in session state to preserve dialogue context across follow‑up questions.
* **Secure API Key Handling**: Prompts users to enter their OpenAI API key through a Streamlit sidebar, ensuring credentials never leave the client.

---

## 📦 Installation & Setup

1. **Clone the repository**

   ```bash
   git clone https://github.com/your-org/rag-pdf-chatbot.git
   cd rag-pdf-chatbot
   ```

2. **Create a virtual environment** (recommended)

   ```bash
   python3 -m venv .venv
   source .venv/bin/activate
   ```

3. **Install dependencies**

   ```bash
   pip install --upgrade \
     streamlit \
     openai \
     PyPDF2 \
     sentence_splitter \
     numpy \
     faiss-cpu
   ```

4. **Run the app**

   ```bash
   streamlit run app.py
   ```

   * By default, this will launch on `http://localhost:8501`.

---

## 🔧 Configuration

1. **OpenAI API Key**

   * The first time the app loads, enter your OpenAI API key in the sidebar.
   * This key is stored only in the Streamlit session state and is never logged or sent elsewhere.

2. **Session State**

   * The app tracks the following session variables to maintain state:

     * `pdf_bytes` – Raw PDF bytes uploaded by the user
     * `chunks` – List of text segments
     * `embeddings` – NumPy array of chunk embeddings
     * `index` – FAISS index for vector search
     * `messages` – Conversation history (last 20 turns)

---

## ⚙️ Application Workflow

1. **Upload PDF**

   * User uploads a PDF via the Streamlit file uploader.
   * Internally, the PDF is read into memory and extracted text is chunked.

2. **Build Embedding Index**

   * Each text chunk is embedded using OpenAI’s `text-embedding-3-small` model.
   * A FAISS `IndexFlatL2` is constructed for fast similarity search.

3. **Query Interface**

   * User submits a natural-language question in the chat input.
   * The top‑k most relevant chunks are retrieved using FAISS.
   * These chunks are prepended as context in a system prompt to GPT‑4o‑mini.
   * The model’s response is displayed and appended to the session messages.

4. **Context Preservation**

   * Up to 20 previous messages are stored and included in each new request, enabling contextual, follow‑up questions.

---

## 🖥️ Code Structure

```text
app.py                # Streamlit application entrypoint
requirements.txt      # Pinned Python dependencies

├── extract.py         # PDF parsing & text extraction functions
├── chunk.py           # SentenceSplitter-based chunking logic
├── embed.py           # OpenAI embedding wrapper & FAISS index builder
├── retrieve.py        # FAISS similarity search & context retrieval
└── chat.py            # Chat interaction & OpenAI chat completion
```

---

## 🔄 Extending & Customization

* **Model Variants**

  * Swap `text-embedding-3-small` for a different embedding model (e.g., `text-embedding-ada-002`).
  * Use GPT-4 or GPT‑4o if you have access, by updating the `client.chat.completions.create` call.

* **Index Tuning**

  * Replace `IndexFlatL2` with an approximate index (e.g., `IndexIVFFlat`) for large-scale PDFs.

* **UI Enhancements**

  * Add file type support (e.g., DOCX, TXT).
  * Integrate authentication (OAuth, API tokens) for multi-user scenarios.

---


