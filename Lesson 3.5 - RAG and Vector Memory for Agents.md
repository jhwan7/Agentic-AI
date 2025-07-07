# 🔍 Lesson 3.5: RAG and Vector Memory for Agents

---

## 🎯 **Learning Goals**

Understand how agents use **Retrieval Augmented Generation (RAG)** and **vector databases** to:
- Access external knowledge beyond training data
- Create searchable, persistent memory
- Ground responses in factual information
- Scale knowledge without retraining

---

## 🧠 **What Is RAG?**

**RAG = Retrieval Augmented Generation**

Instead of relying only on training data, RAG agents:
1. 🔍 **Retrieve** relevant information from external sources
2. 📝 **Augment** the prompt with retrieved context  
3. 🎯 **Generate** responses grounded in current, factual data

```
User Query → Vector Search → Retrieved Docs → LLM + Context → Response
```

---

## 🗄️ **Vector Databases: The Memory Layer**

Vector databases store **embeddings** - numerical representations of text that capture semantic meaning.

| Concept | Description | Example |
|---------|-------------|---------|
| **Embedding** | Text converted to numbers (768/1536 dimensions) | "Python programming" → [0.1, -0.3, 0.8, ...] |
| **Similarity Search** | Find semantically similar content | Query: "coding" finds docs about "programming" |
| **Chunking** | Split documents into searchable pieces | 500-token chunks with 50-token overlap |

### 🛠️ **Popular Vector Databases:**

| Database | Type | Best For |
|----------|------|----------|
| **Pinecone** | Cloud | Production, managed |
| **Weaviate** | Self-hosted | Advanced features, GraphQL |
| **Chroma** | Local | Development, prototyping |
| **FAISS** | Library | Research, custom implementations |
| **Qdrant** | Self-hosted | High performance, filtering |

---

## 🏗️ **RAG Architecture in Agents**

### **1. Ingestion Pipeline**
```python
# Simplified example
def ingest_documents(docs):
    chunks = text_splitter.split_documents(docs)
    embeddings = embedding_model.embed_documents([c.page_content for c in chunks])
    vector_store.add_embeddings(list(zip(chunks, embeddings)))
```

### **2. Retrieval During Agent Execution**
```python
def rag_tool(query: str) -> str:
    # Find similar chunks
    relevant_docs = vector_store.similarity_search(query, k=3)
    
    # Format context
    context = "\n".join([doc.page_content for doc in relevant_docs])
    
    # Return formatted context
    return f"Relevant information:\n{context}"
```

### **3. Agent Integration**
```python
from langchain.tools import tool

@tool 
def search_knowledge_base(query: str) -> str:
    """Search the company knowledge base for relevant information."""
    return rag_tool(query)

# Agent can now call this tool when it needs external knowledge
```

---

## 🔄 **RAG in Agent Memory Systems**

### **Short-term Memory**: Conversation buffer
```python
memory = ConversationBufferMemory(
    memory_key="chat_history",
    return_messages=True
)
```

### **Long-term Memory**: Vector store of past interactions
```python
# Store important interactions for future retrieval
def store_conversation_summary(summary, metadata):
    vector_store.add_texts(
        texts=[summary],
        metadatas=[metadata]
    )
```

### **Knowledge Memory**: External documents/data
```python
# Pre-loaded knowledge base
knowledge_retriever = vector_store.as_retriever(
    search_kwargs={"k": 5, "filter": {"source": "company_docs"}}
)
```

---

## 🧪 **Practical RAG Implementation**

### **Basic RAG Chain with LangChain:**
```python
from langchain.chains import RetrievalQA
from langchain.vectorstores import Chroma
from langchain.embeddings import OllamaEmbeddings

# Setup
embeddings = OllamaEmbeddings(model="nomic-embed-text")
vector_store = Chroma(embedding_function=embeddings)

# Create RAG chain
rag_chain = RetrievalQA.from_chain_type(
    llm=llm,
    chain_type="stuff",
    retriever=vector_store.as_retriever()
)

# Use in agent
response = rag_chain.run("What is our refund policy?")
```

### **RAG Tool for Agents:**
```python
@tool
def search_docs(query: str) -> str:
    """Search company documentation for relevant information."""
    docs = vector_store.similarity_search(query, k=3)
    if not docs:
        return "No relevant information found."
    
    context = "\n\n".join([f"Source: {doc.metadata.get('source', 'Unknown')}\nContent: {doc.page_content}" for doc in docs])
    return context
```

---

## ⚡ **RAG Best Practices**

### **1. Chunking Strategy**
```python
from langchain.text_splitter import RecursiveCharacterTextSplitter

text_splitter = RecursiveCharacterTextSplitter(
    chunk_size=500,        # Smaller chunks = more precise retrieval
    chunk_overlap=50,      # Overlap prevents context loss
    separators=["\n\n", "\n", ".", "!", "?", ",", " ", ""]
)
```

### **2. Embedding Model Selection**
| Model | Dimensions | Best For |
|-------|------------|----------|
| `text-embedding-ada-002` | 1536 | General purpose (OpenAI) |
| `nomic-embed-text` | 768 | Local deployment (Ollama) |
| `sentence-transformers/all-MiniLM-L6-v2` | 384 | Fast, lightweight |

### **3. Metadata Filtering**
```python
# Store with metadata for filtering
vector_store.add_texts(
    texts=chunks,
    metadatas=[
        {"source": "policy_doc", "department": "HR", "date": "2024-01"},
        # ...
    ]
)

# Retrieve with filters
docs = vector_store.similarity_search(
    "vacation policy",
    filter={"department": "HR"}
)
```

### **4. Hybrid Search (Vector + Keyword)**
```python
# Combine semantic and keyword search for better results
from langchain.retrievers import EnsembleRetriever
from langchain.retrievers import BM25Retriever

vector_retriever = vector_store.as_retriever()
keyword_retriever = BM25Retriever.from_texts(texts)

ensemble_retriever = EnsembleRetriever(
    retrievers=[vector_retriever, keyword_retriever],
    weights=[0.7, 0.3]  # 70% vector, 30% keyword
)
```

---

## 🎯 **RAG vs Fine-tuning**

| Approach | Pros | Cons | Best For |
|----------|------|------|----------|
| **RAG** | ✅ Real-time updates<br>✅ No retraining<br>✅ Traceable sources | ❌ Latency overhead<br>❌ Retrieval quality dependent | Dynamic knowledge, factual QA |
| **Fine-tuning** | ✅ Fast inference<br>✅ Specialized behavior | ❌ Expensive updates<br>❌ Static knowledge | Domain-specific tasks, style adaptation |

---

## 🧠 **Memory Hierarchy in Advanced Agents**

```
🔄 Immediate Context (prompt) 
    ↑
💭 Short-term Memory (conversation buffer)
    ↑  
🧠 Working Memory (vector search of recent interactions)
    ↑
📚 Long-term Memory (vector store of all past experiences)
    ↑
🌐 World Knowledge (RAG from external sources)
```

---

## ✅ **Key Takeaways**

| Concept | Takeaway |
|---------|----------|
| **RAG** | Grounds agent responses in external, current information |
| **Vector Databases** | Enable semantic search and scalable memory |
| **Embeddings** | Convert text to searchable numerical representations |
| **Memory Hierarchy** | Different types of memory serve different purposes |
| **Best Practices** | Chunking, metadata, and hybrid search improve quality |

---

## 🧪 **Quick Quiz**

1. **What problem does RAG solve?** → Accessing knowledge beyond training data
2. **What are embeddings?** → Numerical representations of text that capture meaning  
3. **Why use chunking?** → Makes large documents searchable and fits in context windows
4. **Vector search vs keyword search?** → Vector finds semantic similarity, keyword finds exact matches

---

## 🚀 **Coming Next: Lesson 4 - Building Your First RAG Agent**

We'll implement a complete RAG system with:
- Document ingestion pipeline
- Vector storage and retrieval  
- RAG-enabled agent with tools
- Memory persistence across sessions