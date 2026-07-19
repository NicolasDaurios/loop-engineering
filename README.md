# Loop engineering — a reproducible framework for autonomous AI agents

Loop engineering turns an LLM from a response generator into an **execution system**: the agent doesn't deliver an answer, it delivers **finished, verified work**. It loops — attempt, diagnose, correct — until it reaches a **proof of success** defined in advance, or hits a **resource limit** defined in advance.

**The golden rule: no loop without a measurable exit criterion AND a resource limit.** A loop with only one of the two is either useless (it never knows it's done) or dangerous (it never knows when to stop).

## What's in this repo

| File | Purpose |
|---|---|
| [loop-engineering.md](loop-engineering.md) | The reference document (in French). Part A = the strategic foundation, common to all projects. Part B = the operational template. |
| [TEMPLATE-LOOP.md](TEMPLATE-LOOP.md) | Part B extracted as a standalone fill-in template — copy it for each new loop. |
| [workflow-agentique.md](workflow-agentique.md) | The **macro** loop (in French): the 7-step orchestration workflow that wraps loop engineering. Loop engineering is the **micro** layer (the engine of a single task); this document is the **macro** layer (steering a whole project with agents). |
| [README.fr.md](README.fr.md) | This README in French. |

## How to use it

1. **Copy the template** into a `LOOP-<your-task>.md` file.
2. **Start with the proof thresholds** (section B3): objective, testing tool, test, diagnosis, proof of success. If you can't fill in all five elements, the task isn't ready for an autonomous loop.
3. **Fill in the rest**: trigger, guard rails (max iterations, max time, budget, rollback), permissions, the three loop exits (success / assumed failure / human escalation), memory, observability.
4. **The engagement rule: any empty field = the loop is not allowed to start.** An agent that respects this rule will ask you the right questions instead of improvising.
5. **Run supervised first**, measure (success rate, cost per session, escalation rate), then progressively grant autonomy — and switch execution to a cheaper model once the numbers justify it.

The framework is model-agnostic and file-based (markdown + scripts), so a loop designed with a frontier model can be executed by a much cheaper one, and the whole package stays portable across machines and providers.

The reference document is currently written in French. Contributions and translations are welcome.

## License

[CC BY 4.0](LICENSE) — reuse and adapt freely, with attribution.
