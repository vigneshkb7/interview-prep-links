



## LLM API fundamentals

- [x] How models actually work (just enough)
- [x] Tokens, context windows, attention — mental model only
- [x] Prompt engineering: system/user roles, few-shot, chain-of-thought
- [ ] Temperature, top-p, stop sequences, max_tokens
- [ ] OpenAI SDK + Anthropic SDK (both — interviewers test either)
- [x] Streaming responses with SSE / async iterables
- [x] Rate limits, retries, exponential backoff

*Your edge: You already know async JS and HTTP deeply — streaming LLM responses is just SSE over fetch.*


## Prompt engineering (production level)

- [ ] Structured output: JSON mode, function calling / tool use
- [ ] System prompt design, persona, constraints
- [ ] Output formatting contracts (XML, JSON, Markdown)
- [ ] Prompt injection defense, output sanitization
- [ ] Prompt versioning — treating prompts like code
- [ ] Evals: building test suites for prompt quality
- [ ] Anthropic, OpenAI, LangSmith


## Embeddings & vector search

- [ ] The semantic layer under most AI products
- [ ] What embeddings are: dense float vectors, cosine similarity
- [ ] Embedding models: text-embedding-3-small, Cohere, local models
- [ ] Vector DBs: Pinecone, Qdrant, pgvector (pick one deep, know others exist)
- [ ] ANN search: HNSW index, approximate vs exact
- [ ] Chunking strategies: fixed, semantic, recursive, sliding window
- [ ]    Metadata filtering + hybrid search (BM25 + vector)


## RAG pipelines

- [ ] Retrieval-Augmented Generation — every company builds this
- [ ] Ingestion pipeline: load → chunk → embed → store
- [ ] Query pipeline: embed query → ANN search → rerank → prompt
- [ ] Reranking: cross-encoder models (Cohere Rerank, BGE)
- [ ] Advanced RAG: HyDE, parent-child chunks, multi-query retrieval
- [ ] RAG evaluation: context recall, faithfulness, answer relevancy
- Failure modes: hallucination, missing context, context stuffing


## Agents & tool use

- [ ] The frontier of current AI engineering work
- [ ] ReAct pattern: Reason + Act loop
- [ ] Tool/function calling: schema design, result parsing
- [ ] Multi-step agents with memory (short-term conversation + long-term store)
- [ ] Agent orchestration: LangGraph, CrewAI, custom loops
- [ ] Error handling in agentic loops: retry, fallback, human-in-loop
- Agent eval: trajectory correctness, tool call accuracy


## Orchestration frameworks

- [ ] Know at least one deeply
- [ ] LangChain: chains, memory, retrievers, agents (most asked about)
- [ ] LlamaIndex: document indexing, query engines, nodes
- [ ] LangGraph: stateful multi-agent workflows with checkpointing
- [ ] Vercel AI SDK: streaming UI + RSC integration (your frontend edge)
- [ ] When NOT to use a framework — direct SDK calls for simple flows


## Evals & observability

- [ ] Production AI without evals is flying blind
- [ ]LLM-as-judge: using a model to grade model outputs
- Golden dataset construction and maintenance
- Tracing: LangSmith, Langfuse, Braintrust
- Metrics: latency, cost per query, retrieval precision/recall
- A/B testing prompts and models in prod
- Regression testing on prompt changes


## AI-native UI patterns

- [ ] Streaming UX, generative interfaces
- [ ] Streaming text: incremental rendering, cursor animation, token-by-token display
- [ ] AI chat interfaces: message threading, history management, context window budget
- [ ] Generative UI: rendering structured LLM output as dynamic components
- [ ] Optimistic UI for AI responses
- [ ] Error states: model failures, rate limits, content filters
- [ ] React Server Components + AI SDK for streaming RSC


## API & backend patterns

- [ ]Productionizing LLM endpoints
- [x] Streaming API routes (Node.js + ReadableStream / TransformStream)
- Middleware: auth, rate limiting, cost budgeting per user
- Caching: semantic cache (embeddings similarity), exact cache (Redis)
- Queue-based async jobs for long LLM tasks (BullMQ / SQS)
- Webhook patterns for async agent results
- Multi-model routing: GPT-4o for complex, Haiku for simple


## Safety, trust & guardrails

- [ ] Non-negotiable in production AI
- [ ] Input/output moderation: OpenAI moderation API, custom classifiers
- [ ] PII detection and redaction before sending to LLM
- [ ] Prompt injection attack vectors and defense patterns
- [ ] Content policy enforcement at API layer
- [ ] Jailbreak detection heuristics
- Data residency: what leaves your infra vs what doesn't


## Fine-tuning basics

- [ ] Know when to fine-tune vs RAG vs prompt
- [ ] When to fine-tune: style/format consistency, domain vocab, low-latency use cases
- [ ] OpenAI fine-tuning API: dataset format (JSONL), training runs, eval
- [ ] LoRA / QLoRA: parameter-efficient fine-tuning on open models
- [ ] Hugging Face ecosystem: datasets, transformers, PEFT library
- [ ] RLHF intuition: reward models, preference data
- Fine-tune vs RAG decision framework (interviewers love this)


## MLOps & serving basics

- [ ] Know enough to work with ML platform teams
- [ ] Model serving: vLLM, TGI (Text Generation Inference), Triton
- [ ] Inference optimization: quantization (GPTQ, AWQ), batching, KV cache
- [ ] GPU basics: why A100 vs H100, VRAM constraints, tensor parallelism
- [ ] Containerizing ML workloads: Docker + CUDA, multi-stage builds
- [ ] Kubernetes for ML: GPU node pools, resource requests, autoscaling
- Model registries: MLflow, Weights & Biases, HF Hub


## Data pipelines for AI

- [ ] Where the real leverage is for AI systems at scale
- [ ] ETL for AI: document ingestion at scale (PDFs, HTML, structured data)
- [ ] Apache Airflow / Prefect for orchestrating ingestion pipelines
- [ ] Data quality: deduplication, noise detection, schema validation
- [ ] Synthetic data generation for fine-tuning datasets
- [ ] Feature stores for ML: Feast, Tecton concepts
- Streaming pipelines: Kafka + real-time embedding updates


## Multi-agent systems

- [ ] Staff-level system design territory
- [ ] Supervisor-worker agent architectures
- [ ] Agent communication: shared memory, message passing, tool handoffs
- [ ] Parallelism: fan-out / fan-in agent patterns
- [ ] Long-running agent state: checkpointing, resumability
- [ ] Human-in-the-loop: approval gates, interrupts, corrections
- Cost and latency optimization across multi-agent pipelines


## Multimodal & specialized

- [ ] Expanding the surface area of what you can build
- [ ] Vision models: GPT-4V, Claude vision, Gemini — image + text inputs
- [ ] Audio: Whisper for STT, TTS with OpenAI / ElevenLabs
- [ ] Code generation: interpreter patterns, sandboxed execution
- [ ] Structured extraction: extracting data from documents at scale
- [ ] Graph RAG: knowledge graphs as retrieval substrate
- [ ] MCP (Model Context Protocol): emerging standard for tool integration

**AI-native UI patterns**

- [ ]  Streaming UX, generative interfaces
- [ ] Streaming text: incremental rendering, cursor animation, token-by-token display
- [ ] AI chat interfaces: message threading, history management, context window budget
- [ ] Generative UI: rendering structured LLM output as dynamic components

## Coding / ML systems rounds

1. Implement a basic RAG pipeline from scratch

	- Chunk text, embed with API, store in dict (no DB), retrieve top-k by cosine sim, pass to LLM
	- Very common

2. Build a streaming chat endpoint in Node/Python

	- SSE or WebSocket, backpressure, client reconnect, token counting
	- Common

3. Design a semantic cache for LLM responses

	- Embed queries, cosine sim threshold, Redis storage, cache invalidation strategy
	- Staff level

4. Implement tool/function calling loop

	- Parse tool call from model output, execute tool, feed result back, handle multi-turn
	- Very common

5. Evaluation harness for an LLM feature

	- Dataset construction, LLM-as-judge, accuracy/latency metrics, CI integration


## System design rounds

1. Design a production RAG system for 10M documents

	- Ingestion pipeline, chunking strategy, vector DB sharding, reranking, caching, cost model
	- Very common

2. Design an AI coding assistant (like Cursor/Copilot)

	- Context window management, code indexing, streaming, IDE extension architecture
	- Staff level

3. Design a multi-agent research pipeline

	- Task decomposition, agent roles, shared state, error recovery, human approval gates
	- Staff level

4. Design LLM serving infrastructure for 100k RPM

	- Load balancing, batching, model routing, cost optimization, fallback strategy
	- Principal+


## Behavioral / depth questions

- "RAG vs fine-tuning — when do you choose each?"
- "How do you prevent prompt injection in a prod system?"
- "Walk me through how you'd eval a new LLM before shipping"
- "How do you manage context window limits for long conversations?"
- "What are failure modes of agentic systems and how do you handle them?"


## Your differentiators (use these)

- Full-stack depth: "I can build the LLM pipeline AND the streaming UI"
- System design maturity: Staff-level distributed systems thinking
- Real deployments: spectoproduct.com, client projects, ebook
- Java + Node: rare combo — valuable for platform/infra teams
- Micro-frontend / monorepo: transferable to large AI platform codebases


## Weeks 1–4

- Foundation sprint
- LLM APIs (OpenAI + Anthropic)
- Prompt engineering & structured output
- Embeddings + one vector DB (pgvector or Qdrant)

Project:

- Semantic search over your own content
- Build something


## Weeks 5–8

- RAG + Agents
- Full RAG pipeline with LangChain or LlamaIndex
- Tool calling + basic agent loop
- LangSmith for tracing and evals

Project:

- RAG chatbot over a doc corpus with eval harness


## Weeks 9–12

- Full-stack AI integration
- Vercel AI SDK with Next.js streaming
- Production API patterns: caching, rate limits, cost controls
- Guardrails and safety middleware

Project:

- Ship a real AI product (add to spectoproduct.com or standalone)


## Weeks 13–16

- Interview crunch
- Fine-tuning basics (just enough to discuss)
- System design: RAG at scale, agent architectures
- Mock interviews: 2x/week coding, 1x/week system design

Apply: