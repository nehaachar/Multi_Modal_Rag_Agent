# Multi-Modal RAG Agent 🤖

A Retrieval-Augmented Generation (RAG) system that processes PDF documents with text, tables, and images, then provides intelligent answers using LLMs and semantic search.

## 🎯 What is This?

This project implements an AI agent that:
1. **Extracts** text, tables, and images from PDFs using Unstructured.io
2. **Chunks** documents intelligently while preserving context
3. **Stores** chunks as vector embeddings in a database (Chroma)
4. **Retrieves** relevant information based on user queries
5. **Generates** accurate answers using LLM with the retrieved context

## 🏗️ How It Works (Pipeline)

```
PDF → Extract Elements → Smart Chunking → Generate Embeddings → Store in Vector DB → Query → LLM Answer
```

**Key Steps:**
- **Document Partitioning**: Extract text, tables, images with metadata
- **Intelligent Chunking**: Title-based chunks (max 3000 chars) preserve context
- **Multi-Modal Processing**: Handle text, tables, and images separately
- **Semantic Search**: Find relevant chunks using embeddings (not keyword search)
- **LLM Integration**: LLM generates answers using retrieved context

## 🔑 Key Features

| Feature | Benefit |
|---------|---------|
| **High-Resolution PDF Parsing** | Accurate text extraction from complex PDFs |
| **Table Preservation** | Tables extracted as HTML, not mangled text |
| **Image Extraction** | Images stored as base64 for downstream processing |
| **Title-Based Chunking** | Maintains document structure and context |
| **Semantic Search** | Understands meaning, not just keywords |
| **Multi-Modal Support** | Handles text, tables, and images together |


### Setup
```bash
# Install dependencies
pip install "unstructured[all-docs]" langchain_chroma langchain langchain-community langchain-openai python-dotenv

# Create .env file with your OpenAI API key
echo "OPENAI_API_KEY=your_key_here" > .env
```

## 🚀 Usage

The notebook walks through 5 main stages:

### 1. Extract Document Elements
```python
from unstructured.partition.pdf import partition_pdf

elements = partition_pdf(
    filename="./docs/document.pdf",
    strategy="hi_res",
    infer_table_structure=True,
    extract_image_block_types=["Image"],
    extract_image_block_to_payload=True
)
```

### 2. Create Smart Chunks
```python
from unstructured.chunking.title import chunk_by_title

chunks = chunk_by_title(
    elements,
    max_characters=3000,
    new_after_n_chars=2400,
    combine_text_under_n_chars=500
)
```

### 3. Classify Content Types
Separate text, tables, and images for optimal processing.

### 4. Store in Vector Database
```python
from langchain_chroma import Chroma
from langchain_openai import OpenAIEmbeddings

vector_store = Chroma.from_documents(chunks, OpenAIEmbeddings())
```

### 5. Query and Generate Answers
```python
results = vector_store.similarity_search("Your question", k=5)
# Feed results to GPT-4 for final answer
```

## 💡 Technical Highlights

**Why This Approach?**
- **Semantic Search**: Understands meaning
- **Context Awareness**: Keeps related information together in chunks
- **Scalability**: Works with 50-500+ page documents
- **Accuracy**: Preserves tables and images instead of losing them

**Architecture Choices:**
- **Unstructured.io**: Better than PyPDF2 or pdfplumber for multi-modal documents
- **Title-Based Chunking**: Better than naive splitting; preserves document structure
- **Chroma**: Fast, simple, reliable vector database for this use case
- **OpenAI Embeddings + GPT-4**: State-of-the-art quality (can swap for other models)

## 📋 Project Structure

```
multi_modal_rag.ipynb  # Main notebook with all steps
README.md              # This file
```

---

**Tech Stack:** Python • LangChain • Unstructured.io • OpenAI • Chroma  
