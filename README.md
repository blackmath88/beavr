# BEAVR

**BEAVR is Weavr's bounded execution harness.**

Weavr owns intent, mission authority, policy and durable orchestration. BEAVR executes a bounded run inside that envelope, potentially splitting work between a frontier reasoning model and a local worker such as Morrow.

## Concept

The core experiment is:

```text
Weavr
  mission / authority / capability projection
            ↓
          BEAVR
     bounded runtime harness
       ┌───────────────┐
       ↓               ↓
 frontier lead       local worker
 judgment            mechanical work
 Claude / GPT        Morrow / Ollama
       └────── evidence ──────┘
            ↓
          Weavr
```

BEAVR is **not** a second semantic control plane and should not duplicate Weavr's project, mission, Need or policy model.

Inside one run it may coordinate model handoffs, tool loops and evidence gathering. Durable orchestration remains outside it.

## Operating principle

> Frontier decides what and why. Local executes work whose semantic uncertainty has already been reduced.

Frontier models are for framing, decomposition, difficult judgment, critique and synthesis.

Local models are for bounded extraction, classification, routine transformation, repetitive tool work and other tasks that can be reduced to a narrow contract.

If a step still needs substantial judgment, keep it with the frontier model.

## First spike

Use a safe, read-only repository analysis task.

### Runtime lead

- strong frontier model;
- receives the run goal;
- decides which evidence is needed;
- delegates bounded evidence requests;
- reviews worker results;
- asks for another bounded pass only when necessary;
- writes the final interpretation.

### Local worker

Initial tool surface:

- `list_files`
- `read_file`
- `grep`

The worker should not initially be asked to “understand the repo”. It should return structured evidence such as:

- top-level tree;
- detected languages/frameworks;
- relevant README excerpts;
- likely entry points;
- build/test files;
- files most likely to explain architecture;
- evidence for every factual claim.

The frontier model performs the semantic interpretation.

## Benchmark

Run the same task in two modes.

### A. Frontier-only control

```text
goal → frontier → read-only tools → answer
```

### B. BEAVR split

```text
goal
 ↓
frontier runtime lead
 ↓ bounded evidence request
local worker
 ↓ read-only tools
structured evidence
 ↓
frontier review / synthesis
 ↓
answer
```

Capture at least:

- frontier tokens;
- local tokens;
- tool calls;
- wall time;
- malformed tool calls;
- handoff count;
- frontier corrections;
- task completion;
- final answer completeness.

The question is not merely whether handoff works. It is:

> Can semantic compression before delegation reduce expensive frontier work without materially degrading the result?

## Local runtime

For the first spike, run BEAVR on Nebuchadnezzar so local-model access does not introduce networking as another variable.

A later test can move the BEAVR process to Blackbird and access Morrow remotely over the existing governed path.

Morrow should remain the preferred semantic boundary for local execution. Direct Ollama access is acceptable only for a deliberately isolated plumbing test.

## Typed execution

BEAVR should be model-agnostic and treat model output as untrusted proposals.

A future Python implementation can use Pydantic/PydanticAI-style typed contracts such as:

```python
class MissionAuthority(BaseModel):
    environment: Literal["local_lab", "ctf", "owned_target"]
    targets: list[str]
    allowed_capabilities: set[Capability]
    prohibited_capabilities: set[Capability]

class ProposedAction(BaseModel):
    capability: Capability
    target: str
    tool: str
    arguments: dict
    rationale: str

class ActionDecision(BaseModel):
    decision: Literal["allow", "deny", "human_required"]
    reason: str
```

Execution flow:

```text
model
  ↓
typed ProposedAction
  ↓
schema validation
  ↓
deterministic authority / capability gate
  ↓
ALLOW | DENY | HUMAN_REQUIRED
  ↓
tool adapter
  ↓
evidence
```

The model is never the security boundary.

## Security / red-team direction

A later BEAVR profile may support authorized pentest and adversarial simulation against:

- local labs;
- CTF environments;
- deliberately vulnerable containers or VMs;
- explicitly owned or authorized targets.

The design principle is **not** “use a local model to bypass frontier-model safeguards.”

Instead:

> Assume the model is permissive or compromised. Keep authorization, target scope and executable capability enforcement outside the model.

For security runs, Weavr should project explicit machine-readable authority into BEAVR, for example:

```yaml
environment: local_lab
targets:
  - demo-vm-01
allowed:
  - observe
  - enumerate
  - protocol_analysis
prohibited:
  - unrelated_targets
  - destructive_actions
  - credential_exfiltration
```

A local or minimally constrained model may propose actions freely inside the experiment; BEAVR's deterministic gate determines which proposals may actually execute.

## Isolation requirements for offensive simulation

Before active security tooling is introduced, provide:

- an isolated lab network;
- explicit target manifests;
- no route to ordinary home/work networks by default;
- resettable VM/container snapshots;
- bounded tool adapters instead of arbitrary shell access where possible;
- complete action/evidence logging;
- human-required escalation for capabilities outside the mission envelope.

## Relationship to Weavr

BEAVR should eventually appear to Weavr as one replaceable runtime adapter alongside other execution surfaces.

```text
Weavr
├── Claude adapter
├── Codex adapter
├── Herdr adapter
├── pure-Morrow adapter
└── BEAVR split-runtime adapter
    ├── frontier runtime lead
    └── local worker
```

Mission definitions remain provider-neutral. BEAVR receives only the smallest context, tools and authority required for the current run and returns normalized evidence.

## Non-goals

For the initial project:

- no second project/mission state machine;
- no generalized agent platform;
- no unrestricted shell;
- no network/exploitation tooling;
- no autonomous mission widening;
- no premature configuration framework;
- no attempt to hide activity from frontier providers or circumvent their policies.

## Immediate next step

Build one tiny read-only spike using an existing agent framework such as CAI as a dependency:

1. frontier runtime lead;
2. Morrow/local worker;
3. three read-only repository tools;
4. one handoff out and back;
5. trace and metrics;
6. frontier-only control run for comparison.

If that works, treat the resulting traces as evidence for whether BEAVR deserves a durable runtime adapter in Weavr.
