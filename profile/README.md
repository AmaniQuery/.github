# AmaniQuery

**AI-Powered Legal & News Intelligence Platform for Kenya**

[![Go Version](https://img.shields.io/badge/Go-1.21+-00ADD8?style=flat&logo=go)](https://go.dev/)
[![License](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Build Status](https://img.shields.io/badge/Build-Passing-success)](https://github.com/amaniquery/amaniquery)

AmaniQuery is an AI agent framework designed to democratize access to legal information and civil education in Kenya. Built with a production-ready, Go-based RAG (Retrieval-Augmented Generation) architecture, it provides accurate, contextual answers about Kenya Law and current affairs.

---

## 🎯 Vision

**Democratizing Access to Information and Civil Education**

AmaniQuery aims to make legal knowledge accessible to every Kenyan citizen by:
- Providing accurate answers to legal questions in plain language
- Aggregating and contextualizing news relevant to civic matters
- Offering a reliable, fast, and secure platform for information retrieval

---

**Democratizing Access to Legal and News Intelligence for Kenya**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

## 🌍 About

AmaniQuery is a Retrieval-Augmented Generation (RAG) agent designed specifically for the Kenyan legal and news space. We believe that access to legal information and news intelligence should be available to everyone, not just those with legal expertise or expensive subscriptions.

Our platform leverages advanced AI technology to make Kenyan laws, legal precedents, and news information accessible, understandable, and actionable for all citizens.

## ✨ Key Features

- **Legal Information Retrieval**: Access comprehensive Kenyan legal documents, statutes, and case law
- **News Intelligence**: Stay updated with curated and contextualized Kenyan news
- **Natural Language Queries**: Ask questions in plain language and receive accurate, context-aware responses
- **RAG-Powered Accuracy**: Combines the power of large language models with verified legal and news sources
- **Civil Education**: Educational resources to help citizens understand their rights and civic responsibilities
- **Context-Aware Responses**: Get answers tailored to the Kenyan legal framework and local context

## 🚀 Use Cases

### Legal Awareness
- Understanding employment rights and labor laws
- Navigating property and land law
- Learning about constitutional rights
- Accessing information about legal procedures

### News Intelligence
- Tracking legislative changes and their implications
- Understanding policy developments
- Following court decisions and their impact
- Staying informed on civic matters

### Civil Education
- Educating citizens about their rights and responsibilities
- Providing accessible legal literacy
- Empowering communities with knowledge
- Supporting informed civic participation

## 🛠️ Technology

AmaniQuery is built on state-of-the-art RAG (Retrieval-Augmented Generation) technology, which:
- Retrieves relevant information from curated legal and news databases
- Generates accurate, contextual responses using AI
- Ensures responses are grounded in verified sources
- Provides citations and references for transparency


## 📐 Architecture Overview

### High-Level System Architecture

```mermaid
graph TB
    subgraph "Client Layer"
        UI[Web UI/React]
        API[REST/gRPC API]
        CLI[Command Line]
    end

    subgraph "Control Plane"
        AGENT[Agent Orchestrator<br/>Go-based Coordinator]
        ROUTER[Query Router<br/>Semantic Classifier]
        EVAL[Evaluation Engine<br/>Quality Scorer]
        GUARDRAILS[NeMo Guardrails<br/>Safety Filter]
    end

    subgraph "Data Plane"
        EMBED[Embedding Service<br/>GPU-Accelerated]
        RETRIEVER[Retriever Service<br/>Hybrid Search]
        RERANK[Reranker Service<br/>Cross-Encoder]
        GENERATOR[Generator Service<br/>LLM Gateway]
    end

    subgraph "Storage Layer"
        VDB[(Vector DB<br/>Qdrant)]
        CACHE[(Redis Cache)]
        OBJ[(Object Store<br/>MinIO/S3)]
        GRAPH[(Graph DB<br/>Neo4j)]
    end

    subgraph "Infrastructure"
        MONITOR[Monitoring<br/>Prometheus/Grafana]
        TRACING[Tracing<br/>Jaeger]
        VAULT[Secrets Vault<br/>HashiCorp Vault]
    end

    UI --> API
    CLI --> API
    API --> AGENT
    AGENT --> ROUTER
    ROUTER --> GUARDRAILS
    GUARDRAILS --> EMBED
    GUARDRAILS --> RETRIEVER
    RETRIEVER --> VDB
    RETRIEVER --> GRAPH
    EMBED --> VDB
    RETRIEVER --> RERANK
    RERANK --> GENERATOR
    GENERATOR --> EVAL
    EVAL --> CACHE
    CACHE --> API
    AGENT --> MONITOR
    AGENT --> TRACING
    VAULT --> AGENT
```

### Query Router & Semantic Classification

```mermaid
graph LR
    Q[User Query] --> PREPROCESS[Preprocessor<br/>Cleaning/Normalization]
    PREPROCESS --> CLASSIFIER[Classifier<br/>Intent Detection]
    CLASSIFIER --> C1{Query Type}
    C1 -->|Factual| VSS[Vector Search]
    C1 -->|Keyword| BM25[BM25 Search]
    C1 -->|Relational| GRAPH[GraphRAG]
    C1 -->|Multi-hop| AGENT[Agentic Search]
    
    VSS --> HYBRID[Hybrid Results]
    BM25 --> HYBRID
    GRAPH --> HYBRID
    AGENT --> HYBRID
    HYBRID --> FUSION[Rank Fusion<br/>Reciprocal Rank]
    FUSION --> OUTPUT[Ranked Chunks]
```

### Security Architecture

```mermaid
graph TB
    subgraph "Perimeter Security"
        WAF[Web App Firewall]
        API_GATEWAY[API Gateway<br/>Kong/Envoy]
        RATE_LIMIT[Rate Limiter<br/>Token Bucket]
    end
    
    subgraph "Authentication & Authorization"
        OIDC[OIDC Provider<br/>Keycloak]
        JWT[JWT Validator<br/>RS256]
        OPA[OPA Policy Agent]
        POLICY[Rego Policies]
    end
    
    subgraph "Data Security"
        TLS[TLS 1.3<br/>mTLS Internal]
        ENCRYPT[AES-256-GCM]
        VAULT[(HashiCorp Vault)]
    end
    
    subgraph "Compliance"
        AUDIT[Audit Logger]
        GUARDRAILS2[Content Filter]
        PII[PII Detector]
    end
    
    CLIENT[Client] --> WAF
    WAF --> RATE_LIMIT
    RATE_LIMIT --> API_GATEWAY
    API_GATEWAY --> OIDC
    OIDC --> JWT
    JWT --> OPA
    OPA --> POLICY
    POLICY --> SERVICE[Core Services]
    SERVICE --> TLS
    SERVICE --> VAULT
    SERVICE --> AUDIT
    SERVICE --> GUARDRAILS2
    SERVICE --> PII
```

### Synchronous Query Flow

```mermaid
sequenceDiagram
    participant Client
    participant API_Gateway
    participant Agent
    participant Router
    participant Guardrails
    participant Retriever
    participant Generator
    participant Cache
    
    Client->>API_Gateway: POST /query
    API_Gateway->>Agent: Forward query
    
    Agent->>Cache: Check cache
    alt Cache Hit
        Cache-->>Agent: Cached result
        Agent-->>Client: Response 200ms
    else Cache Miss
        Agent->>Router: RouteQuery()
        Router-->>Agent: RoutingDecision
        
        Agent->>Guardrails: ValidateQuery()
        Guardrails-->>Agent: Safe/Unsafe
        
        Agent->>Retriever: HybridSearch()
        Retriever-->>Agent: Top-K chunks
        
        Agent->>Generator: GenerateResponse()
        Generator-->>Agent: Answer
        
        Agent->>Cache: Store result
        Agent-->>Client: Response 2-3s
    end
```

### Agentic Multi-Step Flow

```mermaid
sequenceDiagram
    participant Client
    participant Agent
    participant Planner
    participant Tools
    participant Evaluator
    
    Client->>Agent: Complex query
    Agent->>Planner: Create plan
    Planner-->>Agent: Multi-step plan
    
    loop For each step
        Agent->>Tools: Execute tool
        Tools-->>Agent: Result
        Agent->>Planner: Update plan
    end
    
    Agent->>Evaluator: Validate answer
    Evaluator-->>Agent: Score
    
    alt Score >= threshold
        Agent-->>Client: Final answer
    else Score < threshold
        Agent->>Planner: Revise plan
    end
```

### Data Ingestion Pipeline

```mermaid
graph TB
    SRC[Data Sources<br/>PDF/DB/API] --> INGEST[Ingestion API]
    
    INGEST --> QUEUE1[Message Queue<br/>Kafka/RabbitMQ]
    
    QUEUE1 --> PREPROC[Preprocessor<br/>Go Workers]
    
    PREPROC --> CHUNK[Chunking Engine<br/>Semantic Splitter]
    
    CHUNK --> ENRICH[Enrichment<br/>Metadata/NER]
    
    ENRICH --> EMBED2[Embedding Worker<br/>Batched GPU]
    
    EMBED2 --> INDEX[Indexing Worker]
    
    INDEX --> VDB2[(Vector DB)]
    INDEX --> GRAPH2[(Graph DB)]
    INDEX --> CACHE3[(Cache)]

    style PREPROC fill:#4A90D9
    style CHUNK fill:#50C878
    style EMBED2 fill:#FF6B6B
    style INDEX fill:#DA70D6
```

### Multi-Tier Caching Strategy

```mermaid
graph LR
    CLIENT[Query] --> CACHE1[L1 Cache<br/>CDN/Edge]
    
    CACHE1 -->|Miss| CACHE2[L2 Cache<br/>Redis Cluster]
    
    CACHE2 -->|Miss| CACHE3[L3 Compute Cache]
    
    CACHE3 -->|Miss| EMBED[Embedding Cache<br/>Local LRU]
    
    EMBED -->|Miss| MODEL[Embedding Model]
    
    style CACHE1 fill:#FF69B4
    style CACHE2 fill:#6495ED
    style CACHE3 fill:#90EE90
    style EMBED fill:#FFB6C1
```

---

## 🤝 Contributing

We welcome contributions from developers, legal professionals, journalists, and civic educators. Whether you want to:
- Improve our AI models
- Expand our legal database
- Enhance news coverage
- Translate content
- Report issues or suggest features

Please check our contributing guidelines in individual repositories under the AmaniQuery organization.

## 📜 License

AmaniQuery is open source and available under the MIT License. We believe in transparency and community-driven development.

## 📬 Contact & Community

- **GitHub**: [@AmaniQuery](https://github.com/AmaniQuery)
- **Issues**: Report bugs or request features through our repository issue trackers

---

**AmaniQuery** - *Empowering Kenyans through accessible legal and news intelligence*

> "Amani" means "peace" in Swahili. Through knowledge and understanding, we build a more informed and peaceful society.
**AI-Powered Legal & News Intelligence Platform for Kenya**
