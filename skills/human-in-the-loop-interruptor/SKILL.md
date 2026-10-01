---
name: human-in-the-loop-interruptor
description: "Configures interrupt-before and interrupt-after breakpoints for state modification and human authorization."
---

# Human-in-the-Loop Interruptor

## Overview
The `human-in-the-loop-interruptor` skill integrates approval gates and dynamic human steering into compiled graph workflows using `interrupt_before` and `interrupt_after` directives.

## Interrupt Workflow
1. **Identify High-Impact Nodes:** Mark nodes that execute payments, external notifications, or database writes for inspection.
2. **Halt Execution:** The graph runtime pauses execution immediately prior to or following node invocation.
3. **State Mutation & Approval:** Operators inspect state payloads via `get_state()`, optionally update values with `update_state()`, and resume execution with `None`.
