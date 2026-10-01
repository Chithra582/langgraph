---
name: streaming-token-dispatcher
description: "Dispatches real-time node outputs, tool call chunks, and state updates via stream modes."
---

# Streaming Token Dispatcher

## Overview
The `streaming-token-dispatcher` skill manages multi-modal streaming protocols across complex graphs, emitting intermediate tokens, tool calls, and state transitions in real time.

## Stream Modes
- `values`: Emits the complete state dictionary after each graph node completes.
- `updates`: Emits only the state delta returned by the most recently executed node.
- `messages`: Emits token chunks and tool call fragments directly from LLM calls inside nodes.
- `custom`: Dispatches domain-specific progress events to external websocket or SSE clients.
