# Eunoia — Mental Health Literacy Chatbot

> *Eunoia* (Greek): a state of beautiful thinking; a healthy mind full of goodwill.

Eunoia is a locally-run RAG chatbot built to help young adults recognize early signs of mental health discomfort. Rather than waiting weeks for an appointment, Eunoia offers a non-judgmental, conversational check-in grounded entirely in trusted sources — no hallucinated advice, no generic internet searches.

When the available context isn't enough to answer a question, Eunoia says so and points the user to a real professional resource.

---

## Features

- Retrieval-Augmented Generation (RAG) — answers are grounded in curated mental health content, not invented
- Dual FAISS index search across a knowledge collection and a Q&A bank
- Streaming responses via Ollama (Mistral) with Nomic embeddings
- Rotating motivational quotes and supportive messages in the UI
- Hallucination guard — falls back to a professional referral when context is insufficient
- Fully local — no data leaves your machine

---

## Tech Stack

| Layer | Technology |
|---|---|
| Web framework | Flask |
| Embeddings | `nomic-embed-text` via Ollama |
| Generation | `mistral:latest` via Ollama |
| Vector search | FAISS (CPU) |
| Data sources | Beyond Blue, Headspace, Black Dog Institute, Lifeline |

---

## Project Structure

```
eunoia/
├── app.py                  # Flask app — routes and UI logic
├── src/
│   ├── build_index.py      # One-time FAISS index builder
│   ├── search.py           # RAG search + generation pipeline
│   ├── evaluate.py         # Evaluation runner
│   └── utils.py            # Ollama embedding + generation helpers
├── data/
│   ├── collections.csv     # Knowledge base (disease context paragraphs)
│   └── questions.csv       # Q&A pairs per disease
├── target/                 # Generated indexes, embeddings, and answer logs
├── configs/                # Per-source scraping configs (YAML)
├── templates/index.html    # Chat UI
├── static/style.css        # Stylesheet
└── requirements.txt
```

---

## Setup

### 1. Create a virtual environment (optional but recommended)

```bash
conda create -n eunoia python=3.12 -y
conda activate eunoia
```

### 2. Install dependencies

```bash
pip install -r requirements.txt
```

### 3. Pull Ollama models

In a separate terminal, pull the required models and start Ollama on port `11435`:

```bash
ollama pull nomic-embed-text
ollama pull mistral:latest
```

**macOS**
```bash
OLLAMA_HOST=127.0.0.1:11435 ollama serve
```

**Windows (PowerShell)**
```powershell
$env:OLLAMA_HOST = "127.0.0.1:11435"; ollama serve
```

### 4. Build the vector indexes (one-time)

```bash
python -m src.build_index
```

This generates FAISS index files and embedding arrays inside `target/`.

---

## Usage

### Run the web app

```bash
python app.py
```

Then open `http://localhost:5000` in your browser.

### Run a terminal search

```bash
python -m src.search
```

### Evaluate and save results

```bash
python -m src.evaluate
```

Results are written to `target/evaluation.json` and `target/answers.jsonl`.

---

## Data

`data/collections.csv` — paragraphs extracted from official mental health sources, tagged by disease and source URL.

`data/questions.csv` — 4–5 questions per disease with answers derived from the collections. Both files are linked on the `Disease` column.

Sources: [Beyond Blue](https://www.beyondblue.org.au), [Headspace](https://headspace.org.au), [Black Dog Institute](https://www.blackdoginstitute.org.au), [Lifeline](https://www.lifeline.org.au)

---

## Important Note

Eunoia is a **mental health literacy tool**, not a diagnostic or clinical service. It is not a substitute for professional medical advice. If you or someone you know is in crisis, please reach out to a qualified mental health professional or a crisis line such as [Lifeline Australia — 13 11 14](https://www.lifeline.org.au).
