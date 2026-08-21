# Core-First Extension Architecture

A provider- and domain-neutral Agent Skill for designing, extending, reviewing, refactoring, and debugging modular software without turning every new requirement into a local special case.

The method focuses on four questions:

1. **Who owns this responsibility?**
2. **What is the smallest semantic change that satisfies the requirement?**
3. **Can existing capabilities, modules, providers, or configuration be reused before adding architecture?**
4. **Do all producers converge on the same canonical responsibility-owner path?**

The goal is to reduce long-term architecture erosion and repeated work, especially in projects maintained by changing AI agents or teams.

---

## Core model

```text
Requirement
→ Responsibility
→ Canonical Responsibility Owner / Master System
→ Capability Contract
→ Module / Implementation / Provider
→ Consumer Composition
→ Data / Modifier / Substitution
```

This hierarchy is **semantic**. It is not a required class hierarchy, folder layout, dependency-injection container, plugin framework, or event bus.

A universal rule or authoritative state transition should have one canonical owner. Extensions may add capabilities, implementations, providers, data, or composition through declared seams, but they do not gain permission to create parallel owners or mutate foreign authoritative state directly.

---

## Why this Skill exists

Extensible software often decays in predictable ways:

- a second payment provider introduces provider-specific checkout rules;
- an importer creates a second business-logic path;
- a builder writes a separate runtime format;
- a new screen duplicates an existing workflow;
- a plugin mutates state owned by another subsystem;
- a new variant copies resolved values instead of preserving base + modifier semantics;
- a shared definition accidentally stores runtime-specific mutable state;
- an AI agent creates a new local implementation because it did not discover the canonical owner.

Core-First Extension Architecture provides a repeatable decision procedure for avoiding those failures.

---

## Key principles

### Responsibility before structure

For Greenfield work or unclear architecture, discover durable responsibility boundaries from:

- user and business outcomes;
- authoritative state and decisions;
- workflows and domain events;
- external systems and I/O boundaries;
- data, lifecycle, and persistence boundaries;
- APIs, call graphs, and tests.

Do not manufacture subsystems merely to fit a template.

### One canonical responsibility owner

A durable rule, policy, or authoritative state transition belongs to one responsibility owner.

Consumers may configure or compose behavior. They should not silently become parallel owners.

### Contract is not implementation

```text
Capability Contract
≠ Module / Implementation
≠ Provider / Adapter
```

A capability defines reusable semantic behavior. Modules and providers realize or specialize it.

### Definition is not runtime state

```text
WorkflowDefinition ≠ WorkflowRun
PaymentProviderConfig ≠ PaymentTransaction
TemplateDefinition ≠ RenderExecution
```

Shared definitions or profiles should not accidentally contain per-run or per-consumer mutable state.

### Reuse before extending

Prefer, in order:

1. reuse;
2. configuration;
3. correction;
4. simplification;
5. removal;

before introducing new architecture.

### Producer convergence

Different producers may use different ingestion mechanisms, but equivalent intent should converge before domain behavior:

```text
manual / code / agent / builder / importer / API / external package
→ canonical semantic representation or interface
→ validation
→ canonical responsibility-owner path
→ domain behavior
```

This does **not** mean every provider executes the same code. Stripe and Adyen can have different adapters while still sharing canonical payment semantics and ownership.

### External origin grants no extra authority

Third-party content or executable extensions must use the same owner contracts and boundaries as first-party code.

External code does not automatically gain permission to:

- write foreign authoritative state;
- create parallel owners;
- bypass validation;
- monkey-patch unrelated systems;
- take over responsibilities outside its declared seam.

### Reverse the graph for debugging

```text
observable failure
→ runtime/data flow
→ consumer/module/provider
→ capability contract
→ canonical responsibility owner
→ implementation anchor
→ root cause
→ forward re-test
```

Fix the canonical cause, not an upstream symptom.

---

## Change taxonomy

Actual architecture or behavior deltas are classified using the smallest sufficient semantic form:

| # | Classification | Typical meaning |
|---:|---|---|
| 1 | `DATA_OR_ASSET` | Pure data, text, asset, or declarative value |
| 2 | `VALUE_MODIFIER` | Relative change that should preserve base propagation |
| 3 | `CONFIG_OVERRIDE` | Scoped configuration override |
| 4 | `SLOT_SUBSTITUTION` | Replace a module/action/definition at an existing declared slot |
| 5 | `PROVIDER_SUBSTITUTION` | Select another implementation behind the same contract |
| 6 | `COMPOSE_EXISTING_CAPABILITY` | Attach/reuse an existing capability |
| 7 | `ADD_REUSABLE_MODULE` | Add reusable behavior beneath an existing capability |
| 8 | `EXTEND_EXISTING_CAPABILITY` | Expand an existing semantic contract |
| 9 | `NEW_REUSABLE_CAPABILITY` | Add a genuinely new reusable semantic ability |
| 10 | `NEW_ADAPTER_OR_PROVIDER` | Add an implementation/integration behind an existing owner |
| 11 | `NEW_EXTENSION_POINT` | Introduce a new declared seam for repeated/future extension |
| 12 | `EXTERNAL_CONTENT_PACKAGE` | Add externally supplied data/content through declared seams |
| 13 | `EXTERNAL_EXECUTABLE_EXTENSION` | Add third-party executable code behind controlled contracts |
| 14 | `CORE_RULE_CHANGE` | Change a universal rule owned by the core/responsibility owner |
| 15 | `EXPLICIT_ONE_OFF_EXCEPTION` | Deliberately isolated exception that cannot be expressed otherwise |

`CORE_OWNER_REPLACEMENT` is **not** a normal extension class. Treat it as an architecture migration.

Debugging, discovery, review, migration assessment, and reuse-proof work are **task types**, not additional change classifications.

---

## Interaction semantics

The Skill distinguishes three universal meanings:

- **Command** — request an authoritative owner to perform a state-changing action.
- **Query** — request information.
- **Event** — report that an authoritative fact already occurred.

Transport is project-specific. A project may use direct calls, callbacks, signals, queues, RPC, brokers, or process boundaries.

The Skill does **not** mandate a global event bus.

---

## Conditional architecture profiles

Advanced machinery is activated only when the project actually needs it.

| Profile | Activate when |
|---|---|
| Persistent / Versioned Definitions | Saved definitions, durable schemas, migrations, external packages |
| Distributed / Asynchronous Boundary | Network/process boundaries, queues, retries, webhooks |
| Dynamic / External Plugin Ecosystem | Runtime discovery/loading, independent package lifecycle/versioning |
| Untrusted Executable Extension | Third-party executable code requires permissions or isolation |
| Deterministic / Reproducible Execution | Replay, simulation, rollback, deterministic tests |
| Performance-Critical Runtime | Latency, frame, throughput, memory, CPU, or energy budgets matter |
| Multi-Tenant / Contextual Configuration | Tenant/environment/region/user-specific variation |
| Polyglot / Cross-Runtime Contracts | Multiple languages/processes implement the same durable contract |

A normal statically configured external API provider is usually an **adapter/provider**, not automatically a dynamic plugin ecosystem.

See [`references/CONDITIONAL_PROFILES.md`](./references/CONDITIONAL_PROFILES.md).

---

## Second-consumer proof

A reusable seam should survive a meaningfully different second case.

Preferred proof order:

```text
real existing second consumer/provider
→ real planned second case already required
→ adversarial design scenario
→ isolated contract/test fixture
→ fresh-agent architecture challenge
```

Do not ship a fake production feature solely to prove an abstraction.

---

## When to use this Skill

Use it when work involves questions such as:

- Where should a reusable feature live?
- Should this be configuration, a module, a capability, or a new system?
- How do I add another provider without branching business logic everywhere?
- How should UI authoring, import, API creation, and external packages converge?
- How do I expose safe extension points?
- How do I prevent plugins from bypassing canonical ownership?
- How should a Greenfield product be decomposed into durable responsibility owners?
- Why do multiple local implementations keep drifting apart?
- Where should an extension-related bug be fixed?

Typical domains include SaaS, backend/API systems, desktop applications, CLI tools, libraries, data pipelines, AI systems, integrations, embedded software, and games.

---

## When *not* to use advanced extension machinery

A small or local change should remain small.

Examples:

- rename a label;
- change one constant;
- fix a local parser bug;
- add a simple command-line flag;
- update a static asset.

Do not add a plugin loader, registry, service container, event bus, manifest format, dependency resolver, sandbox, or migration framework merely because future extensibility is imaginable.

---

## Package structure

```text
core-first-extension-architecture_skill_v1.2_FINAL/
├── README.md
├── SKILL.md
├── references/
│   ├── EXTENSION_ARCHITECTURE_METHOD.md
│   └── CONDITIONAL_PROFILES.md
└── evals/
    └── evals.json
```

- [`SKILL.md`](./SKILL.md) — compact procedure loaded by the agent.
- [`EXTENSION_ARCHITECTURE_METHOD.md`](./references/EXTENSION_ARCHITECTURE_METHOD.md) — full method and domain-neutral examples.
- [`CONDITIONAL_PROFILES.md`](./references/CONDITIONAL_PROFILES.md) — JIT guidance for advanced runtime and deployment concerns.
- [`evals.json`](./evals/evals.json) — structured architecture evaluation cases.

---

## Installation

This repository is designed for Agent-Skills-style environments that discover a directory containing `SKILL.md`.

General installation:

1. Copy or clone this repository into a Skill directory recognized by your agent or coding harness.
2. Keep `SKILL.md` at the root of the Skill directory.
3. Preserve the `references/` and `evals/` directories.
4. Let the agent load references just in time when a relevant concern is present.

Exact discovery paths and installation commands vary by provider or coding harness. The Skill intentionally avoids depending on one provider.

---

## Example requests

### Add a provider

```text
Checkout already supports Stripe.
Add Adyen and leave room for more providers.
```

Expected direction:

```text
Payments responsibility owner
→ existing payment capability contract
→ new Adyen provider/adapter
→ canonical payment result
→ existing checkout behavior
```

### Converge producers

```text
Users can create report templates in the UI.
We also want JSON import.
Both must behave exactly like built-in templates.
```

Expected direction:

```text
UI / JSON / built-in producer
→ canonical TemplateDefinition
→ validation
→ canonical template owner path
→ same domain behavior
```

### Keep a trivial change trivial

```text
Change the label "Save" to "Save file".
```

Expected direction:

```text
local data/text change
→ no new capability
→ no plugin framework
```

---

## Evaluation suite

Version 1.2 includes **21 structured eval scenarios** using `schema_version: 3`.

The assertion model separates task type from change classification:

```text
task_type
expected_classification
allowed_classifications
forbidden_classifications
expected_owner
must_reuse
must_not_create
conditional_profiles
required_evidence
```

Coverage includes:

- Greenfield responsibility discovery;
- payment/provider extension;
- producer convergence;
- configuration vs. capability;
- module vs. system;
- duplicate-rule repair;
- core-rule changes;
- one-off abuse;
- distributed failures;
- executable extensions;
- definition/runtime-state isolation;
- new reusable capabilities;
- composition;
- modifier propagation;
- slot/provider substitution;
- non-speculative second-consumer proof;
- reverse debugging;
- external-origin authority boundaries;
- polyglot contracts;
- trivial negative cases.

The goal is to test architecture decisions, not keyword matching.

---

## Project truth vs. Skill guidance

The Skill is a **procedure**.

Your repository's canonical architecture documents, contracts, code, schemas, and acceptance criteria remain the source of project truth.

A project should remain understandable and correct even when this Skill is unavailable.

---

## Design philosophy

This method intentionally rejects the idea that all software should become a plugin platform.

It does **not** universally require:

- dynamic plugins;
- manifests;
- lifecycle hooks;
- dependency injection;
- service containers;
- event buses;
- persistence frameworks;
- sandboxing;
- migration systems;
- SaaS-style system maps;
- "everything is a plugin".

Add such machinery only when concrete requirements justify it.

---

## Origin

The method was originally discovered while working through a highly modular game architecture. It was then generalized and cross-reviewed against non-game domains such as SaaS, provider integrations, import/export pipelines, AI systems, libraries, and distributed software.

The game is now only one case study. The Skill itself is domain-neutral.

---

## Version

**Core-First Extension Architecture Skill v1.2 FINAL**

Key properties of this release:

- 15 canonical change classifications;
- Responsibility Owner vs. Project Authority Artifact separation;
- Capability Contract vs. implementation/provider separation;
- Definition/Profile vs. Runtime Instance/Scoped State separation;
- producer convergence based on canonical semantic ownership rather than literal "same runtime";
- conditional architecture profiles instead of mandatory infrastructure;
- structured eval schema v3 with 21 scenarios;
- debugging/review/discovery task types kept separate from change classifications.

---

## Contributing

When proposing changes, apply the method to the Skill itself:

1. Identify the concrete failure mode.
2. Check whether existing guidance already covers it.
3. Prefer clarification or correction over adding a new concept.
4. Add a new universal rule only if it is broadly reusable.
5. Keep domain-specific concerns conditional.
6. Add or update an eval that demonstrates the failure and expected behavior.
7. Avoid increasing architecture surface without evidence.

---

## License

No license file is included in the current package.

Before publishing the repository for public reuse or accepting external contributions, add an explicit license appropriate for your intended distribution.
