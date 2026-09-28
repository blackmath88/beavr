# XINX integration boundary

## Status

Future integration concept only. This does **not** change XINX's current roadmap priority or make BEAVR a dependency of XINX.

## Roles

```text
XINX
  field understanding
  "What exists, what is it, and what changed?"

Weavr
  semantic control plane
  "Given intent, evidence and authority, what may happen next?"

BEAVR
  bounded execution harness
  "Execute this experiment inside these walls."

Morrow
  local semantic worker
  "Perform this bounded reasoning/tool task."

BT FIELD / Kite / Bruce / other instruments
  observation and interaction
  "Here is what actually happened."
```

## Intended loop

```text
WORLD
  ↓
INSTRUMENTS
  ↓
XINX
Explore → Inspect
  ↓
CASE
evidence + uncertainty + authorization
  ↓
candidate EXPERIMENT
  ↓
Weavr
authority / policy / capability decision
  ↓
BEAVR (only when adaptive multi-step execution is useful)
  ↓
bounded instrument actions
  ↓
observed result events
  ↓
XINX
compare before / after → update case
```

BEAVR enters primarily at **Experiment**, not Explore.

Normal sensing, evidence import, provenance, deterministic inference, identity uncertainty and simple comparisons remain XINX responsibilities and should not require an agent runtime.

## Case as integration object

A future XINX Case can collect:

- subject/candidate identity;
- scope and authorization;
- Observed evidence;
- Inferred analysis;
- Enriched interpretations;
- unresolved questions;
- experiment history.

A Case may propose a discriminating experiment when a new measurement could reduce uncertainty.

## Experiment boundary

A future portable request might resemble:

```json
{
  "schema": "xinx/experiment-request/v1",
  "case": "example-case",
  "question": "Does observed field X vary with state Y?",
  "authority": {
    "basis": "owned_device",
    "targets": ["example-target"]
  },
  "allowed": [
    "observe",
    "connect",
    "subscribe"
  ],
  "prohibited": [
    "write",
    "unrelated_targets"
  ],
  "evidence_required": [
    "raw_before",
    "raw_during",
    "raw_after"
  ]
}
```

This is a design sketch, not an accepted schema.

BEAVR may compile an approved experiment into the smallest runtime/tool projection needed to execute it. Resulting actions and measurements return to XINX as attributable evidence; BEAVR does not overwrite XINX evidence or identity state.

## When BEAVR is warranted

Do **not** use BEAVR merely because AI is available.

Direct XINX/instrument execution is preferable for deterministic operations such as:

- passive capture;
- explicit evidence import;
- fixed parser execution;
- simple approved before/after measurement.

BEAVR becomes useful when execution is genuinely adaptive, for example:

```text
observe
→ inspect result
→ choose one of several authorized next measurements
→ measure
→ compare
→ stop when the question is resolved or authority is exhausted
```

## Security progression

A future experiment taxonomy may distinguish:

```text
OBSERVE      passive acquisition
PROBE        bounded non-destructive interaction
INTERACT     state-changing but reversible test
ADVERSARIAL  explicitly authorized security simulation
```

Escalating experiment class changes the authority/capability envelope, not trust in the model.

The model remains untrusted. Deterministic policy and instrument adapters enforce executable scope.

## UX implication

Do not add an AI or BEAVR destination to XINX.

Within Inspect, the useful conceptual surface is:

```text
Evidence
Hypotheses
Questions
Next experiments
```

A user prepares/approves an experiment based on the question it answers. Whether execution is direct, through BT FIELD/Kite/Bruce, or through BEAVR is an implementation detail below that intent-level interface.

## Architectural rule

> XINX owns evidence and experimental understanding. Weavr owns durable authority and orchestration. BEAVR owns bounded adaptive execution. Instruments own measurement and physical interaction.

This boundary should remain true even if the components are deployed together.
