---
name: stategraph-cyclic-designer
description: "Constructs cyclic state machines with typed schemas, reducer channels, and conditional routers."
---

# StateGraph Cyclic Designer

## Overview
The `stategraph-cyclic-designer` skill engineers cyclic graph architectures using LangGraph's core primitives: `StateGraph`, channel reducers (e.g. `operator.add`), node functions, and conditional routing edges.

## Graph Design Principles
1. **Typed State Definition:** Model state using `typing.TypedDict` or Pydantic models with explicit channel annotations.
2. **Deterministic Reducers:** Use reducer functions (such as `Annotated[list, operator.add]`) to define how concurrent updates merge into state.
3. **Finite Recursion Limits:** Configure explicit recursion limits (`recursion_limit: 25`) to prevent unbounded graph cycling.
4. **Clean Entry & Exit Paths:** Every graph must have an explicit entry point and unambiguous paths terminating at `END`.
