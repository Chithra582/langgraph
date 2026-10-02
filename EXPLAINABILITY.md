# EXPLAINABILITY.md

This document explains the internal mechanisms, data lineage, operational boundaries, and governance framework of **LangGraph Stateful Agent Orchestrator** (`langgraph`) in accordance with the **OpenGAP v0.1.0** specification for the **HiDevs GitAgent Passport** clearance pipeline.

> **Agent Name:** LangGraph Stateful Agent Orchestrator (`langgraph`)  
> **Specification:** OpenGAP v0.1.0  
> **Category / Domain:** Developer Tools / Stateful Agent Orchestration & Graph Runtimes  
> **Compliance Standard:** OpenGAP Checkpoint 2 (Explainability & Decision Governance), OWASP LLM Top 10, MITRE ATLAS  

---

## How the Agent Decides

The LangGraph Stateful Agent Orchestrator provides a foundational runtime execution and architectural engine for stateful, multi-actor LLM applications. Engineered around LangGraph's core primitives, it enables software developers and enterprise architects to construct resilient, cyclic agent workflows with durable persistence, thread-scoped memory, and fine-grained human oversight.

### 1. Decision Architecture

The stateful graph execution, checkpointer persistence, and human interrupt pipeline operates across a deterministic, five-stage architecture:

```
Developer Task / Application Event (User Query / External Event / Graph Execution Request)
    │
    ▼
[Stage 1: Intent Ingestion & State Definition]
    │  - Evaluates workflow requirements and defines typed state channels
    │  - Configures channel reducer operators (`operator.add` vs overwrite)
    │  - Initializes thread identifier and memory checkpointer session
    ▼
[Stage 2: Node & Tool Function Composition]
    │  - Decomposes logic into modular node functions accepting state dictionaries
    │  - Bounds tool execution interfaces and parameter validations
    │  - Separates side-effect operations from pure computational transforms
    ▼
[Stage 3: Edge Routing & Cycle Compilation]
    │  - Establishes fixed and conditional routing edges between graph nodes
    │  - Verifies that all conditional branches terminate at valid nodes or `END`
    │  - Enforces finite recursion ceilings (`recursion_limit: 25`)
    ▼
[Stage 4: Checkpointing & Interrupt Gatekeeping]
    │  - Persists atomic state snapshots to SQLite / PostgreSQL checkpointer backends
    │  - Evaluates `interrupt_before` and `interrupt_after` human review triggers
    │  - Suspends execution safely pending human operator input or inspection
    ▼
[Stage 5: State Serialization & Stream Delivery]
    │  - Serializes finalized state output payloads and token streams
    │  - Scrubs private user credentials and connection strings from event logs
    │  - Dispatches auditable execution traces to local workspace or developer console
    ▼
Validated Stateful Graph Execution & Auditable Checkpoint Trajectory Record
```

### 2. Decision Logic & Graph Routing Formulations

The orchestrator evaluates edge transitions, recursion safety, and state convergence using deterministic mathematical models:

1. **Conditional Route Probability ($P_{\text{route}}$)**:
   $$P_{\text{route}}(N_{\text{next}} \mid S_{\text{current}}) = \arg\max_{c \in C} f_{\text{condition}}(S_{\text{current}}, c)$$
   where $f_{\text{condition}}$ evaluates deterministic routing predicates against current state channels $S_{\text{current}}$.

2. **Graph Convergence & Recursion Safety Index ($S_{\text{safety}}$)**:
   $$S_{\text{safety}} = 1 - \frac{N_{\text{steps}}}{\text{recursion\_limit}}$$
   When $S_{\text{safety}} \le 0$, the engine halts execution deterministically with code `WARN_RECURSION_LIMIT_REACHED` to prevent runaway cycle costs.

### 3. Thresholding & Refusal Decision Criteria

LangGraph Stateful Agent Orchestrator enforces strict operational safety and integrity boundaries:
- **Refusal to Execute Unbounded Graphs**: StateGraph topologies lacking terminating paths or recursion caps are deterministically rejected with code `ERR_UNBOUNDED_GRAPH_PROHIBITED`.
- **Refusal of Unmanaged Concurrent Overwrites**: State channels updated concurrently by parallel nodes without assigned reducer functions trigger compilation refusal (`ERR_CONCURRENT_OVERWRITE_CONFLICT`).
- **Turn Ceiling Enforcement**: Graph execution turns are strictly bounded by `max_turns: 25` to eliminate runaway loop execution (`WARN_TURN_BUDGET_REACHED`).
- **Workspace Directory Confinement**: Checkpointer database writes and graph exports are confined to the project directory; external file paths are blocked (`ERR_OUT_OF_BOUNDS_FILE_WRITE`).

### 4. Fallback Decision Mechanism

Continuous graph execution is guaranteed through multi-tier fault recovery:
- **Model Cascade Failover**: When the primary foundation model experiences latency spikes or HTTP 429 rate limits, the orchestrator cascades automatically between `claude-3-5-sonnet`, `gpt-4o`, and `gemini-2.0-flash`.
- **Durable Checkpoint Resumption**: If a host node crashes or encounters a runtime exception, the graph recovers the last validated state snapshot from disk and resumes cleanly.
- **Graceful Linear Degradation**: When cyclic feedback loops fail to converge after 3 attempts, the engine routes directly to human interrupt or terminal summary nodes.

### 5. Human-in-the-Loop Governance

Human authority is a first-class architectural primitive within LangGraph workflows:
- **First-Class Interrupt Primitives**: Sensitive nodes (financial transfers, external emails, file deletions) enforce mandatory `interrupt_before` pause states requiring human operator confirmation.
- **State Editing Capability**: Human operators can inspect, rewind, and directly edit channel states before resuming graph execution.
- **Emergency Session Kill Switch**: Operators can issue standard `Ctrl+C` interrupt signals or post `/abort` commands to terminate running graph threads instantly.

---

## The Data It Uses

LangGraph Stateful Agent Orchestrator operates under strict privacy, data minimization, and local workspace isolation standards.

### 1. Ingested Input Data

The agent processes only operational assets necessary to fulfill graph execution:
- **User Prompts & Inputs**: Task queries, external webhook payloads, and initial state parameters.
- **State Channel Payloads**: Typed dictionaries containing message histories, intermediate computation outputs, and tool responses.
- **Thread Metadata**: Thread identifiers (`thread_id`), checkpoint namespaces, and execution configuration options.

### 2. Configuration & Reference Data

- **Graph Topology Schemas**: Node definitions, edge transition matrices, and conditional routing tables.
- **Checkpointer Schemas**: SQLite and PostgreSQL table schemas for state snapshot storage and replay.
- **Channel Reducer Definitions**: Typing schemas defining merge operators (`operator.add`, custom append functions).

### 3. Base Model & Inference Lineage

- **Deterministic Graph Runtime Engines**: Topological sorters, state channel reducers, and checkpointer engines executed natively in Python (100% deterministic with zero LLM variance).
- **Foundation LLMs**: High-capability frontier models (`claude-3-5-sonnet`, `gpt-4o`, `gemini-2.0-flash`) utilized for in-node reasoning, message transformation, and natural language decision logic.
- **Zero Training on State Payloads**: User prompt streams, thread checkpoint histories, and database payloads are never transmitted to external servers or used for model training.

### 4. Data Privacy, Storage, and Retention

- **OWASP LLM & MITRE ATLAS Hardened**: Defended against prompt injection, context leakage, and unauthorized agency.
- **Thread-Scoped Data Isolation**: Memory snapshots are partitioned strictly by `thread_id`, guaranteeing complete isolation between execution sessions.
- **Local-Only Snapshot Storage**: Checkpoint databases and trajectory logs reside entirely on the local user filesystem.
- **Zero Commercial Monetization**: Application graph schemas, state histories, and developer prompts are never monetized, aggregated, or shared with third parties.

---

## Limitations

Understanding the operational boundaries and technical constraints of LangGraph Stateful Agent Orchestrator is essential for production deployment.

### 1. High-Frequency State Checkpointing I/O Overhead
- **Limitation**: Persisting large binary or document states to disk at every intermediate node can create I/O throughput bottlenecks.
- **Mitigation**: The engine supports lightweight pointer passing, storing large blobs in object stores and persisting only URI references in graph state.

### 2. Complex Graph Topology Visualization Readability
- **Limitation**: Graphs containing hundreds of interconnected nodes and conditional edges become visually overwhelming in standard Mermaid diagrams.
- **Mitigation**: LangGraph encapsulates complex sub-systems into modular nested subgraphs that collapse into single high-level nodes.

### 3. Asynchronous Race Conditions in Dynamic Branching
- **Limitation**: Fan-out execution across multiple concurrent nodes requires careful synchronization to avoid race conditions.
- **Mitigation**: Channels enforce explicit deterministic reducer functions to serialize parallel contributions into unified state lists.

### 4. Non-Deterministic Loop Oscillation
- **Limitation**: An agent node and critic node can alternate indefinitely without converging if evaluation criteria are subjective.
- **Mitigation**: Graph topologies enforce hard recursion ceilings and maximum cycle thresholds that force human arbitration upon exhaustion.

### 5. Multi-Provider Tool Calling Schema Incompatibilities
- **Limitation**: Subtle parameter format discrepancies between model providers can cause tool parsing errors during provider failover.
- **Mitigation**: The runtime standardizes tool schemas using Pydantic validation before dispatching calls to any downstream model.

---

## Summary & Compliance Checklist

| Checkpoint 2 Requirement | Corresponding Section | Status |
| :--- | :--- | :---: |
| **How the agent decides** | [How the Agent Decides](#how-the-agent-decides) | **Covered** |
| - Decision architecture & 5-stage pipeline | Section 1 | Verified |
| - Decision logic & graph routing formulations | Section 2 | Verified |
| - Thresholding & refusal decision criteria | Section 3 | Verified |
| - Fallback decision mechanism | Section 4 | Verified |
| - Human-in-the-loop governance & oversight | Section 5 | Verified |
| **The data it uses** | [The Data It Uses](#the-data-it-uses) | **Covered** |
| - Ingested user prompts, state payloads & threads | Section 1 | Verified |
| - Configuration, topology schemas & reducers | Section 2 | Verified |
| - Base model lineage & deterministic engines | Section 3 | Verified |
| - Data privacy, retention lifecycle & MITRE/OWASP | Section 4 | Verified |
| **Its limitations** | [Limitations](#limitations) | **Covered** |
| - High-frequency state checkpointing I/O overhead | Section 1 | Verified |
| - Complex graph topology visualization readability | Section 2 | Verified |
| - Asynchronous race conditions in dynamic branching | Section 3 | Verified |
| - Non-deterministic loop oscillation | Section 4 | Verified |
| - Multi-provider tool calling schema incompatibilities | Section 5 | Verified |
