# doc-agent

A small Streamlit app for asking questions about your own PDF files, answered by Google Gemini.

## What it is

This was a quick retrieval-augmented generation (RAG) experiment. You upload one or more PDFs, the app indexes their text, and then you ask it questions. The model is told to answer only from the uploaded documents and to say "answer is not available in the context" when it can't find the answer there.

How it works, step by step:

1. PyPDF2 pulls the text out of every page.
2. LangChain's `RecursiveCharacterTextSplitter` cuts it into 10,000-character chunks with 1,000 characters of overlap.
3. Each chunk is embedded with Google's `models/embedding-001` and stored in a local FAISS index (`faiss_index/`).
4. A question is matched against that index, and the closest chunks go to `gemini-pro` through a LangChain "stuff" QA chain.

## What's inside

- `chatpdf1.py`: the whole app (PDF parsing, chunking, FAISS index, QA chain and the Streamlit UI)
- `requirements.txt`: dependencies, unpinned

## How to run

You need a Google AI Studio API key.

```bash
pip install -r requirements.txt
echo "GOOGLE_API_KEY=<your key>" > .env
streamlit run chatpdf1.py
```

Upload PDFs in the sidebar, click **Submit & Process**, then type a question.

## Status

Experiment from August 2024, not maintained. It was written against `gemini-pro`, `embedding-001` and an older LangChain API. With current library versions you'll probably have to update the model names and imports, and pass `allow_dangerous_deserialization=True` to `FAISS.load_local` (only do that for an index you built yourself).
