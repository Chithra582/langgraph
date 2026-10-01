---
name: subgraph-multi-agent-composer
description: "Nests modular agent subgraphs into hierarchical supervisor-worker architectures."
---

# Subgraph Multi-Agent Composer

## Overview
The `subgraph-multi-agent-composer` skill structures multi-agent collectives by treating compiled StateGraphs as discrete nodes within parent orchestrator graphs.

## Multi-Agent Topologies
- **Hierarchical Supervisor:** A parent supervisor inspects incoming tasks, routes to specialized worker subgraphs, and aggregates outputs.
- **Networked Peer Handoffs:** Subgraphs invoke sibling subgraphs directly via shared command primitives.
- **Isolated Subgraph States:** Subgraphs maintain private state schemas that map cleanly to and from the parent graph schema.
