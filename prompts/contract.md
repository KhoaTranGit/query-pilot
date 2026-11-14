# Future LLM prompt contract (planning specification, not runnable prompts)

Agent instructions must clearly distinguish trusted system policies from untrusted uploaded cell contents. No schema or business definition is considered approved merely because an LLM proposes it. Generated SQL must pass a programmatic policy check and restricted execution. All answers must include scope, aggregation grain, definitions, result-linked evidence, and uncertainties. Return typed statuses for absent information rather than completing the narrative from imagination. Cross-check arithmetic using deterministic tool calls. Never cite retrieved context as proof of numerical outputs unless the actual SQL result corroborates it.

Future implementation: store versioned prompt templates under `prompts/`; test adversarial content embedded in CSV headers/rows and retrieved documentation. Prompts do not replace access control, SQL sandbox, schema registry or deterministic numeric verification.
