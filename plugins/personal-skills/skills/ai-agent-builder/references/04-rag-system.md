## 4. RAG System Construction

### 4.1 Complete RAG Pipeline

+---------------------------------------------------------------------+

|                       INGESTION PIPELINE                            |

|                                                                     |

|  Documents -> [Loader] -> [Chunker] -> [Embedder] -> [Vector Store] |

|                                          |                          |

|                                   [Metadata Store]                  |

+---------------------------------------------------------------------+

                                    |

                              (index built)

                                    |

+---------------------------------------------------------------------+

|                        QUERY PIPELINE                               |

|                                                                     |

|  Query -> [Query Embed] -> [Vector Search] -> [Reranker] -> [LLM]  |

|              |                  |                |                  |

|         [HyDE opt.]        [Metadata          [Context             |

|                              Filter]           Format]              |

+---------------------------------------------------------------------+

### 4.2 Full Python RAG Implementation

#!/usr/bin/env python3

"""

Production RAG pipeline with chunking, embedding, retrieval, and reranking.

"""

import hashlib

import json

from dataclasses import dataclass, field

from pathlib import Path

from typing import Any

# --- Data Models -------------------------------------------------------------

@dataclass

class Document:

    content: str

    metadata: dict[str, Any] = field(default_factory=dict)

    doc_id: str = field(default="")

    

    def __post_init__(self):

        if not self.doc_id:

            self.doc_id = hashlib.md5(self.content.encode()).hexdigest()[:12]

@dataclass

class Chunk:

    content: str

    doc_id: str

    chunk_index: int

    metadata: dict[str, Any] = field(default_factory=dict)

    embedding: list[float] = field(default_factory=list)

@dataclass

class RetrievedChunk:

    chunk: Chunk

    score: float

    rerank_score: float = 0.0

# --- Chunking Strategies -----------------------------------------------------

class ChunkingStrategy:

    """Base class — override chunk()."""

    def chunk(self, doc: Document) -> list[Chunk]:

        raise NotImplementedError

class FixedSizeChunker(ChunkingStrategy):

    """Fixed token-count chunks with overlap. Good for general text."""

    

    def __init__(self, chunk_size: int = 512, overlap: int = 64):

        self.chunk_size = chunk_size

        self.overlap    = overlap

    

    def chunk(self, doc: Document) -> list[Chunk]:

        # Simple word-based split (use tiktoken for production token counting)

        words = doc.content.split()

        chunks = []

        step = self.chunk_size - self.overlap

        

        for i in range(0, len(words), step):

            chunk_words = words[i:i + self.chunk_size]

            if not chunk_words:

                break

            chunks.append(Chunk(

                content=" ".join(chunk_words),

                doc_id=doc.doc_id,

                chunk_index=len(chunks),

                metadata=doc.metadata,

            ))

        return chunks

class SentenceChunker(ChunkingStrategy):

    """Sentence-boundary-aware chunking. Better for structured prose."""

    

    def __init__(self, sentences_per_chunk: int = 5, overlap_sentences: int = 1):

        self.n  = sentences_per_chunk

        self.ov = overlap_sentences

    

    def chunk(self, doc: Document) -> list[Chunk]:

        import re

        sentences = re.split(r'(?\<=[.!?])s+', doc.content.strip())

        chunks = []

        step = self.n - self.ov

        

        for i in range(0, len(sentences), step):

            batch = sentences[i:i + self.n]

            if not batch:

                break

            chunks.append(Chunk(

                content=" ".join(batch),

                doc_id=doc.doc_id,

                chunk_index=len(chunks),

                metadata=doc.metadata,

            ))

        return chunks

class RecursiveChunker(ChunkingStrategy):

    """Hierarchical chunking — preserves section structure. Best for long docs."""

    

    SEPARATORS = ["\\n\\n", "\\n", ". ", " "]

    

    def __init__(self, max_chunk_size: int = 800, min_chunk_size: int = 100):

        self.max_size = max_chunk_size

        self.min_size = min_chunk_size

    

    def chunk(self, doc: Document) -> list[Chunk]:

        chunks = []

        self._split(doc.content, doc.doc_id, doc.metadata, 0, chunks, [])

        return chunks

    

    def _split(self, text, doc_id, metadata, depth, chunks, idx_counter):

        if len(text.split()) \<= self.max_size or depth >= len(self.SEPARATORS):

            idx_counter.append(None)

            chunks.append(Chunk(

                content=text,

                doc_id=doc_id,

                chunk_index=len(chunks),

                metadata=metadata,

            ))

            return

        

        sep = self.SEPARATORS[depth]

        parts = text.split(sep)

        current = ""

        

        for part in parts:

            candidate = (current + sep + part).strip() if current else part

            if len(candidate.split()) \<= self.max_size:

                current = candidate

            else:

                if current and len(current.split()) >= self.min_size:

                    self._split(current, doc_id, metadata, depth + 1, chunks, idx_counter)

                current = part

        

        if current:

            self._split(current, doc_id, metadata, depth + 1, chunks, idx_counter)

# --- Vector Store (using Chroma for local dev) --------------------------------

class VectorStore:

    """Minimal abstract vector store interface."""

    

    def upsert(self, chunks: list[Chunk]) -> None:

        raise NotImplementedError

    

    def query(self, embedding: list[float], top_k: int = 10,

              filter_metadata: dict | None = None) -> list[RetrievedChunk]:

        raise NotImplementedError

class ChromaVectorStore(VectorStore):

    """Local development store using Chroma."""

    

    def __init__(self, collection_name: str, persist_dir: str = "./chroma_db"):

        import chromadb

        client = chromadb.PersistentClient(path=persist_dir)

        self.collection = client.get_or_create_collection(

            name=collection_name,

            metadata={"hnsw:space": "cosine"},

        )

    

    def upsert(self, chunks: list[Chunk]) -> None:

        self.collection.upsert(

            ids=[f"{c.doc_id}_{c.chunk_index}" for c in chunks],

            documents=[c.content for c in chunks],

            embeddings=[c.embedding for c in chunks],

            metadatas=[c.metadata for c in chunks],

        )

    

    def query(self, embedding: list[float], top_k: int = 10,

              filter_metadata: dict | None = None) -> list[RetrievedChunk]:

        kwargs: dict[str, Any] = {

            "query_embeddings": [embedding],

            "n_results": top_k,

            "include": ["documents", "metadatas", "distances"],

        }

        if filter_metadata:

            kwargs["where"] = filter_metadata

        

        results = self.collection.query(**kwargs)

        retrieved = []

        for doc, meta, dist in zip(

            results["documents"][0],

            results["metadatas"][0],

            results["distances"][0],

        ):

            retrieved.append(RetrievedChunk(

                chunk=Chunk(content=doc, doc_id=meta.get("doc_id",""), chunk_index=0, metadata=meta),

                score=1.0 - dist,  # cosine: distance -> similarity

            ))

        return retrieved

# --- Embedder ----------------------------------------------------------------

class Embedder:

    """Embed text using OpenAI-compatible API."""

    

    def __init__(self, model: str = "text-embedding-3-small"):

        import openai

        self.client = openai.OpenAI()

        self.model  = model

    

    def embed(self, texts: list[str]) -> list[list[float]]:

        response = self.client.embeddings.create(model=self.model, input=texts)

        return [item.embedding for item in response.data]

    

    def embed_one(self, text: str) -> list[float]:

        return self.embed([text])[0]

# --- Reranker ----------------------------------------------------------------

class CrossEncoderReranker:

    """Rerank retrieved chunks with a cross-encoder for precision improvement."""

    

    def __init__(self, model: str = "cross-encoder/ms-marco-MiniLM-L-6-v2"):

        from sentence_transformers import CrossEncoder

        self.model = CrossEncoder(model)

    

    def rerank(self, query: str, chunks: list[RetrievedChunk], top_k: int = 5) -> list[RetrievedChunk]:

        pairs  = [(query, c.chunk.content) for c in chunks]

        scores = self.model.predict(pairs)

        for chunk, score in zip(chunks, scores):

            chunk.rerank_score = float(score)

        return sorted(chunks, key=lambda c: c.rerank_score, reverse=True)[:top_k]

# --- RAG Pipeline ------------------------------------------------------------

class RAGPipeline:

    def __init__(

        self,

        chunker: ChunkingStrategy,

        embedder: Embedder,

        vector_store: VectorStore,

        reranker: CrossEncoderReranker | None = None,

        llm_fn: callable = None,

        retrieval_top_k: int = 10,

        rerank_top_k: int = 5,

    ):

        self.chunker      = chunker

        self.embedder     = embedder

        self.store        = vector_store

        self.reranker     = reranker

        self.llm          = llm_fn

        self.retrieval_k  = retrieval_top_k

        self.rerank_k     = rerank_top_k

    

    # -- Ingestion ------------------------------------------------------------

    def ingest(self, documents: list[Document]) -> int:

        all_chunks: list[Chunk] = []

        

        for doc in documents:

            chunks = self.chunker.chunk(doc)

            texts  = [c.content for c in chunks]

            embeddings = self.embedder.embed(texts)

            for chunk, emb in zip(chunks, embeddings):

                chunk.embedding = emb

            all_chunks.extend(chunks)

        

        self.store.upsert(all_chunks)

        return len(all_chunks)

    

    # -- Query -----------------------------------------------------------------

    def retrieve(

        self,

        query: str,

        metadata_filter: dict | None = None,

        use_hyde: bool = False,

    ) -> list[RetrievedChunk]:

        # Optional HyDE: generate a hypothetical document to improve retrieval

        embed_target = query

        if use_hyde and self.llm:

            hypothetical = self.llm(

                f"Write a short passage that would answer this question:\\n{query}"

            )

            embed_target = hypothetical

        

        q_embedding = self.embedder.embed_one(embed_target)

        candidates  = self.store.query(q_embedding, top_k=self.retrieval_k,

                                       filter_metadata=metadata_filter)

        

        if self.reranker and candidates:

            return self.reranker.rerank(query, candidates, top_k=self.rerank_k)

        return candidates[:self.rerank_k]

    

    def answer(self, query: str, metadata_filter: dict | None = None) -> dict:

        chunks = self.retrieve(query, metadata_filter=metadata_filter)

        

        context = "\\n\\n---\\n\\n".join(

            f"[Source {i+1}] {c.chunk.content}"

            for i, c in enumerate(chunks)

        )

        

        prompt = f"""Answer the question using ONLY the provided context.

If the context doesn't contain the answer, say "I don't have enough information."

Context:

{context}

Question: {query}

Answer:"""

        

        response = self.llm(prompt) if self.llm else "[No LLM configured]"

        

        return {

            "answer": response,

            "sources": [

                {

                    "content":  c.chunk.content[:200] + "...",

                    "score":    c.score,

                    "rerank":   c.rerank_score,

                    "metadata": c.chunk.metadata,

                }

                for c in chunks

            ],

        }

### 4.3 Vector Database Selection

| Database | Hosting | Scale | Latency | Best For |
| :---- | :---- | :---- | :---- | :---- |
| Pinecone | Managed cloud | High | Low | Production, zero-ops |
| Qdrant | Self-hosted / cloud | High | Very Low | Performance-critical |
| Weaviate | Both | High | Low | Hybrid (keyword + vector) |
| Chroma | Self-hosted | Medium | Low | Local dev / prototyping |
| pgvector | Self-hosted (Postgres) | Medium | Medium | Existing Postgres stacks |
| Redis VSS | Both | Medium | Very Low | Real-time / cache-adjacent |
| Milvus | Self-hosted / cloud | Very High | Low | Enterprise scale |

### 4.4 Chunking Strategy Selection

| Strategy | Chunk Size | Overlap | Best For |
| :---- | :---- | :---- | :---- |
| Fixed-size | 500-1000 tokens | 50-100 tokens | General text, unknown structure |
| Sentence | 3-5 sentences | 1 sentence | News articles, documentation |
| Semantic | Variable | Meaning-based | Research papers, books |
| Recursive | Hierarchical | Parent-child | Long documents with headers |

### 4.5 RAG Evaluation Metrics

| Metric | Definition | Target |
| :---- | :---- | :---- |
| Context Relevance | % of retrieved chunks relevant to query | > 0.80 |
| Answer Faithfulness | % of answer grounded in context | > 0.90 |
| Retrieval Precision@5 | Relevant chunks in top 5 / 5 | > 0.70 |
| Context Coverage | % of questions with >=1 relevant chunk in top-5 | > 0.85 |
| End-to-end Accuracy | Correct answers / total questions | > 0.80 |

# Evaluate a RAG pipeline

python scripts/rag_evaluator.py 

  --contexts retrieved_contexts.json 

  --questions eval_questions.json 

  --metrics relevance,faithfulness,coverage 

  --output report.json

---