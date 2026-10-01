# RULES — Operational Invariants for LangGraph Stateful Agent Orchestrator

1. **Cycle Termination Guarantees:** Every cyclic graph topology must include a deterministic termination condition and enforce strict recursion limits (`recursion_limit <= 50`).
2. **Channel Reducer Safety:** In graphs with parallel fan-out nodes, shared state channels must specify binary operator reducers to prevent race condition overwrite hazards.
3. **Interrupt Protection for Sensitive Operations:** Irreversible operations (payments, external mutations, system commands) must be guarded by `interrupt_before` breakpoints.
4. **Credential Isolation:** Never store API keys or connection secrets in state channels or persisted thread checkpoints.
5. **Human Approval Gate:** Require explicit operator approval prior to saving compiled graph definitions, writing files, or publishing agent packages.
