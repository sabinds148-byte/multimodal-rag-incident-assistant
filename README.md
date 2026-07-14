# Azure Multimodal RAG - Smart Incident Assistant

A multimodal Retrieval-Augmented Generation (RAG) pipeline built on Azure OpenAI, Azure AI Document Intelligence, and Azure AI Search. It ingests incident report PDFs, standard operating procedure (SOP) documents, and incident-scene images, indexes them for hybrid (vector + keyword) search, and answers incident-response questions grounded in that content.

## How it works

1. **Extraction**
   - [extract_incidents.py](extract_incidents.py) — parses incident report PDFs (`pdfs/`) with Azure Document Intelligence into `data/parsed_incidents.json`.
   - [extract_sops.py](extract_sops.py) — reads SOP text files (`sops/`) into `data/parsed_sops.json`.
   - [extract_images_caption.py](extract_images_caption.py) — captions incident images (`images/`) using an Azure OpenAI vision-capable deployment into `data/parsed_images.json`.
2. **Indexing**
   - [prepare_search_documents.py](prepare_search_documents.py) — combines the parsed JSON into search documents and generates embeddings via Azure OpenAI.
   - [create_vector_index.py](create_vector_index.py) — (re)creates the Azure AI Search index with vector + keyword fields.
3. **Retrieval & Response**
   - [hybrid_search.py](hybrid_search.py) — runs hybrid (vector + keyword) queries against the Azure AI Search index.
   - [rag_response.py](rag_response.py) — orchestrates retrieval and generates grounded answers with an Azure OpenAI chat deployment, keeping a rolling chat history.

## Prerequisites

- Python 3.9+
- An Azure subscription with:
  - Azure OpenAI (chat + embedding + vision-capable deployments)
  - Azure AI Search
  - Azure AI Document Intelligence (Form Recognizer)

## Setup

1. Install dependencies:
   ```bash
   pip install -r requirements.txt
   ```
2. Create a `.env` file in this directory with the following variables:
   ```
   DOC_INTELLIGENCE_ENDPOINT=
   DOC_INTELLIGENCE_KEY=
   AZURE_OPENAI_ENDPOINT=
   AZURE_OPENAI_KEY=
   AZURE_EMBEDDING_DEPLOYMENT=
   AZURE_GPT_DEPLOYMENT=
   AZURE_SEARCH_ENDPOINT=
   AZURE_SEARCH_KEY=
   AZURE_SEARCH_INDEX=
   ```

## Usage

Run the pipeline in order:

```bash
python extract_incidents.py
python extract_sops.py
python extract_images_caption.py
python prepare_search_documents.py
python create_vector_index.py
python rag_response.py
```

## Project structure

```
pdfs/     Source incident report PDFs
sops/     Standard operating procedure text files
images/   Incident-scene images
data/     Extracted/parsed JSON artifacts
extracted/ Additional extraction output
```
