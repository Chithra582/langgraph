---
name: durable-checkpoint-manager
description: "Implements thread-scoped checkpointing (MemorySaver, SqliteSaver, PostgresSaver) for crash resilience."
---

# Durable Checkpoint Manager

## Overview
The `durable-checkpoint-manager` skill configures persistent checkpointers that capture state snapshots across every step of graph execution, enabling replay, time-travel debugging, and fault-tolerant resumes.

## Supported Checkpointers
- `MemorySaver`: Ephemeral in-memory checkpointer ideal for testing and unit tests.
- `SqliteSaver`: Embedded disk-backed persistence for local agent development.
- `PostgresSaver`: Production-grade distributed checkpointer supporting multi-node concurrency and connection pools.

## Thread Partitioning
- Partition execution histories strictly using unique `thread_id` keys in `RunnableConfig`.
- Validate that checkpoints are committed atomically before executing side-effecting operations.
