# Core-First Extension Architecture — Method

## 1. Purpose

This method is a domain-neutral software-engineering discipline for preventing local special-case implementations from eroding canonical ownership and reuse.

It is applicable to SaaS, APIs/backend, desktop software, CLI tools, libraries/SDKs, data pipelines, AI systems, internal tools, embedded systems, games, and structured software-supported workflows.

It is not a mandate to build plugin infrastructure.

## 2. Core model

```text
Requirement
→ Responsibility
→ Canonical Responsibility Owner / Master System
→ Capability Contract
→ Module / Implementation / Provider
→ Consumer Composition
→ Data / Modifier / Substitution
```

Orthogonal producer path:

```text
manual / code / agent / builder / importer / API / package
→ normalize
→ validate
→ canonical semantic representation
→ canonical responsibility-owner path
```

Runtime distinction:

```text
Definition / Profile
→ Runtime Instance
→ Scoped Mutable State
```

## 3. Phase 0 — discover responsibilities

For Greenfield work, architecture recovery, or unclear ownership:

1. List primary user/business outcomes.
2. Identify decisions and state that must be authoritative.
3. Trace important inputs, outputs, external systems, and persistence boundaries.
4. Identify policies/invariants that must remain consistent.
5. Group only responsibilities that genuinely belong together.
6. Record exclusions: what each owner explicitly does not own.
7. Identify the first reusable capabilities under each owner.

Useful techniques are optional: use-case analysis, event storming, domain events, user journeys, data-flow analysis, API inspection, call-graph inspection, test/fixture analysis, or bounded-context discovery.

The output is not a fixed number of systems. A small tool may need only a few durable responsibilities.

## 4. Structural roles

### Master System / Responsibility Owner
Owns a durable responsibility, universal policy, or authoritative state.

### Capability Contract
Defines a reusable semantic ability owned by a system.

### Module / Implementation
Reusable concrete behavior under a capability.

### Provider / Adapter
Implementation of a contract, especially across an external boundary.

### Consumer / Composition Host
Uses or composes capabilities/modules/providers while supplying identity/configuration.

### Definition / Profile
Durable declarative intent/configuration.

### Runtime Instance / Scoped State
Live mutable execution state. Never assume a shared definition object is safe for consumer-specific runtime state.

### Project Authority Artifact
A normative document/file records project truth about owners, contracts, rules, and decisions. It is not itself the runtime/domain owner. Generated indexes, Atlas views, summaries, or routing files may point to authorities but do not become semantic owners.

## 5. Interaction semantics

Use only the semantic distinction universally:

- Command = request owner to perform change.
- Query = read/inspect.
- Event = authoritative fact already occurred.

Transport is project-specific. It may be a direct method, message queue, RPC, signal, callback, process boundary, or event system.

Do not introduce a universal event bus merely because events exist.

## 6. Change classification

Use one canonical taxonomy for actual architecture/behavior deltas:

1. DATA_OR_ASSET
2. VALUE_MODIFIER
3. CONFIG_OVERRIDE
4. SLOT_SUBSTITUTION
5. PROVIDER_SUBSTITUTION
6. COMPOSE_EXISTING_CAPABILITY
7. ADD_REUSABLE_MODULE
8. EXTEND_EXISTING_CAPABILITY
9. NEW_REUSABLE_CAPABILITY
10. NEW_ADAPTER_OR_PROVIDER
11. NEW_EXTENSION_POINT
12. EXTERNAL_CONTENT_PACKAGE
13. EXTERNAL_EXECUTABLE_EXTENSION
14. CORE_RULE_CHANGE
15. EXPLICIT_ONE_OFF_EXCEPTION

The classification is a decision aid, not a requirement to create matching classes/files.

`CORE_OWNER_REPLACEMENT` is an architecture migration, not ordinary customization.

Debugging, discovery, review, migration assessment, and reuse-proof work are task types rather than additional change classes. If no product/architecture delta is being classified, leave the change classification unset.

## 7. Extension decision test

Ask in order:

1. Is this only data/configuration/assets?
2. Is this a relative modifier?
3. Is this a substitution at an existing slot/provider boundary?
4. Can existing capabilities/modules be composed?
5. Is a reusable module missing beneath an existing capability?
6. Is the capability incomplete?
7. Is a genuinely new reusable semantic capability missing?
8. Is this only a new external adapter/provider?
9. Do several future producers/consumers actually require a declared extension point?
10. Is external package loading required, or is ordinary configuration sufficient?
11. Is executable third-party code truly required?
12. Does the universal rule itself need to change?
13. Is this genuinely a one-off exception?

Prefer the earliest sufficient answer.

External origin grants no extra authority. Third-party content/code must use the same responsibility-owner contracts and validation boundaries as first-party producers; it does not gain permission to mutate foreign authoritative state or create a parallel owner.

## 8. Capability granularity test

Create a capability when there is a coherent reusable semantic contract.

Do not create one merely because:
- a new data value appears;
- a single consumer needs a different number;
- a class would otherwise be large;
- an implementation detail has a name;
- future extensibility is merely imaginable.

A module/provider should remain beneath an existing capability when the contract is unchanged.

## 9. Producer convergence

Strong systems normalize equivalent producer intent before domain behavior.

### SaaS template example

```text
built-in template ───┐
UI builder ──────────┤
JSON import ─────────┼→ canonical TemplateDefinition → validation → Template owner/runtime
API creation ────────┤
external package ────┘
```

No builder-only execution engine.

### AI provider example

```text
OpenAI ─┐
Claude ─┤
Gemini ─┼→ LLM Provider Contract → Canonical Response / Tool semantics
Kimi ───┤
Local ──┘
```

The providers execute different code, but the surrounding product does not duplicate business rules per provider.

## 10. Domain-neutral examples

### Payment providers
`SYS-PAYMENTS → CAP-PAYMENT-PROVIDER → Stripe/Adyen/PayPal → Checkout`

Providers may call external APIs but do not own order state.

### Export formats
`SYS-EXPORT → CAP-EXPORTER → PDF/CSV/JSON → Report workflow`

### Data ingestion
`manual/CSV/bank sync/partner API → canonical Transaction → validation → Transaction owner`

### CLI tool
`SYS-OUTPUT → CAP-OUTPUT-FORMATTER → Table/JSON/YAML → multiple commands`

### Library
`SYS-STORAGE → CAP-STORAGE-BACKEND → Memory/File/PostgreSQL → same library API`

### AI system
`SYS-INFERENCE → CAP-MODEL-PROVIDER → OpenAI/Anthropic/DeepSeek/MiniMax/Kimi/local`

### Game origin case
Shared game systems → capabilities/modules → content composition → builder/import/mod convergence. This is only one case study.

## 11. Variation and modifier semantics

Prefer source-of-truth values plus declared variation.

If base = 5 and modifier = +8, resolved = 13; if base later becomes 7, resolved becomes 15.

When multiple modifiers can combine, the project must define deterministic semantics such as phases/ordering, precedence, scope, lifetime, and conflict behavior. The method does not impose one universal formula.

## 12. High-risk governance

### Core rule changes
A core rule change must update the canonical owner, assess affected contracts/consumers, assess compatibility/migration impact, update relevant authority artifacts, and produce focused acceptance evidence. Local patches are a failure mode.

### One-off exceptions
A one-off exception must be justified, scoped, and prevented from becoming an undocumented second architecture. If a second meaningful consumer appears, revisit the classification.

## 13. Reverse debugging

```text
observable failure
→ runtime/data flow
→ consumer/module/provider
→ contract
→ capability
→ canonical responsibility owner
→ implementation anchor
→ root cause
→ focused re-test
```

Do not patch an upstream symptom when the canonical owner is wrong.

## 14. Acceptance

Use only relevant tests:
- second-consumer/provider;
- producer convergence;
- no parallel owner;
- foreign-write rejection;
- dependency rejection;
- modifier propagation;
- definition/runtime-state isolation;
- compatibility/migration;
- actual observable behavior.

## 15. When not to use advanced extension machinery

For trivial, local, low-risk changes, apply the ownership/reuse reasoning and stop there.

Examples:
- rename a label;
- change one constant;
- add a local command-line flag;
- fix a small parsing bug;
- update a static text resource.

No registry, manifest, plugin loader, extension point, service container, or event bus is required unless the product genuinely needs it.

## 16. Second-consumer proof without speculative product work

Reuse should be challenged by a meaningfully different second case, but do not add fake production features solely to prove abstraction.

Preferred proof order:

```text
real existing second consumer/provider
→ real planned second case already required
→ adversarial design scenario
→ isolated contract/test fixture
→ fresh-agent architecture challenge
```

The proof tests semantic reuse, not feature count.
