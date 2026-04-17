# Multi-Modal RAG Agent 🤖

A Retrieval-Augmented Generation (RAG) system that processes PDF documents with text, tables, and images, then provides intelligent answers using LLMs and semantic search.

<img width="569" height="312" alt="{5A518BE8-E4A7-4957-8153-8A67EBEC1CF7}" src="https://github.com/user-attachments/assets/2e095fbd-0363-4aa2-b49f-e3979e903868" />

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
- **Semantic Search**: Find relevant chunks using embeddings (not keyword search)
- **LLM Integration**: LLM generates answers using retrieved context


### Setup
```bash
# Install dependencies
pip install "unstructured[all-docs]" langchain_chroma langchain langchain-community langchain-openai python-dotenv

```

## 🚀 Usage

The notebook walks through 5 main stages:

### 1. Extract Document Elements
```python
def partition_document(file_path: str):
    print(f"📄 Partitioning document: {file_path}")
  
    elements = partition_pdf(
        filename=file_path,
        strategy="fast", 
        infer_table_structure=False, 
        extract_image_block_types=[], 
    )
    
    print(f"✅ Extracted {len(elements)} elements")
    return elements
```

### 2. Create Smart Chunks
```python
def create_chunks_by_title(elements):
    print("🔨 Creating smart chunks...")
    
    chunks = chunk_by_title(
        elements,
        max_characters=3000,
        new_after_n_chars=2400,
        combine_text_under_n_chars=500
    )
    
    print(f"✅ Created {len(chunks)} chunks")
    return chunks
```

### 3. Classify Content Types
Separate text, tables, and images for optimal processing.

### 4. Store in Vector Database
```python
def create_vector_store(documents, persist_directory="chroma_db"):
    print("🔮 Creating vector store...")
    
    # We use the 'legacy' string which is actually the most stable across all versions
    embeddings = GoogleGenerativeAIEmbeddings(
        model="gemini-embedding-001"
    )
    
    db = Chroma.from_documents(
        documents,
        embedding=embeddings,
        persist_directory=persist_directory
    )
    
    print("✅ Vector DB ready")
    return db
```

### 5. Query and Generate Answers
```python
query = "What are the components of Transformer?"
retriever = db.as_retriever(search_kwargs={"k": 3})

chunks = retriever.invoke(query)

answer = generate_final_answer(chunks, query)
print(answer)
```

## 📋 Project Structure

```
agent.ipynb  # Main notebook with all steps
README.md              # This file
```

---


## Output

<img width="1174" height="525" alt="Screenshot 2026-04-17 140217" src="https://github.com/user-attachments/assets/ee71cbf7-04b3-49bd-b8c8-8591e0063a18" />

<img width="1025" height="324" alt="image" src="https://github.com/user-attachments/assets/b1812003-6055-44e9-9fcd-60d3ca57d739" />

<img width="1163" height="307" alt="Screenshot 2026-04-17 134910" src="https://github.com/user-attachments/assets/b689998f-9eb6-4ba3-b99d-e934841a45c5" />

