---
name: core-first-extension-architecture
description: Design, extend, review, refactor, or debug modular software by decomposing responsibilities before resolving canonical owners, distinguishing owners from implementation roles, classifying the smallest semantic change, reusing before extending, composing consumers instead of duplicating core behavior, and converging multiple producers onto one canonical owner path. Use for reusable features, ownership ambiguity/conflicts/gaps, provider/adapter additions, import/build paths, configurable variants, repeated local implementations, extension points, plugin/mod systems, architecture repair, or Greenfield system decomposition. Do not use for trivial isolated edits where reuse, ownership, or system boundaries are not material.
---

# Core-First Extension Architecture

Use the repository's own authorities as project truth. This Skill teaches a provider- and domain-neutral decision procedure; it does not replace project architecture.

## Core principle

Do not start from "what new class/plugin should I add?"

Start from:

```text
Requirement
→ Responsibility
→ Canonical Responsibility Owner / Master System
→ Capability Contract
→ Module / Implementation / Provider
→ Consumer Composition
→ Data / Modifier / Substitution
```

The hierarchy is semantic, not a required class tree or folder layout.

A universal rule or authoritative state transition has one canonical owner. Extensions may add capabilities, implementations, providers, data, or composition through declared seams; they do not gain permission to create parallel owners or mutate foreign authoritative state directly.

## Phase 0 — Responsibility discovery

For Greenfield work or unclear architecture, first discover durable responsibility boundaries.

Inspect only what is needed, using suitable evidence such as:
- primary user/business use cases;
- authoritative state and decisions;
- domain events and workflows;
- external systems and I/O boundaries;
- data flows and lifecycle boundaries;
- existing APIs, call graphs, persistence boundaries, and tests.

Group responsibilities only where they genuinely belong to one durable owner. Do not manufacture subsystems merely to fit this method.

## Structural roles

Distinguish these roles before editing:

```text
Master System / Responsibility Owner
  one durable owner of a responsibility, rule set, or authoritative state

Capability Contract
  reusable semantic ability or contract owned by a system

Module / Implementation
  reusable concrete behavior that realizes or specializes a capability

Provider / Adapter
  implementation behind a capability boundary, often for external systems

Consumer / Composition Host
  composes capabilities/modules/providers and supplies identity/configuration

Definition / Profile
  durable declarative description or configuration

Runtime Instance / Scoped State
  live execution state created from definitions/configuration
```

A module is not automatically a system. A provider is not automatically a new owner. A definition is not runtime state.

## Responsibility-relative ownership resolution

`Owner` is always relative to a **named responsibility**. Before declaring/replacing an owner, distinguish the canonical owner from the current carrier/decision path, implementation anchor, capability/module/provider, consumer/composition host, data/profile, migration target, or an unresolved gap.

Current code location, class/file boundaries, size, and names do not prove ownership. If the same responsibility is independently decided or authoritatively written through multiple active paths, treat that as a parallel-owner conflict; controlled provider/module variants behind one canonical capability/owner are not parallel owners.

If evidence is insufficient, keep ownership **unresolved** and test existing owner/capability, data/configuration, composition, module/provider, or capability extension before creating a new owner. Centralized data does not centralize shared behavior if consumers still execute the rule independently. Capability identity comes from its reusable semantic contract, not the number of implementations.

**Role by semantics, not by name.**

### Project Authority Artifact

A normative file/contract records project truth about an owner, capability, rule, or decision. It is not itself the runtime/domain owner. Keep documentation authority and semantic/runtime ownership distinct.

## Interaction semantics

When owners communicate, identify the semantic form:

- **Command** — request an authoritative owner to perform a state-changing action.
- **Query** — request information without transferring ownership.
- **Event** — report that an authoritative fact already occurred.

Do not introduce a global event bus, RPC layer, or message broker merely because these semantics exist. Use the project's smallest suitable transport.

## Change classification

Classify a requested architecture/behavior delta before implementation. Prefer the earliest sufficient form.

1. `DATA_OR_ASSET`
2. `VALUE_MODIFIER`
3. `CONFIG_OVERRIDE`
4. `SLOT_SUBSTITUTION`
5. `PROVIDER_SUBSTITUTION`
6. `COMPOSE_EXISTING_CAPABILITY`
7. `ADD_REUSABLE_MODULE`
8. `EXTEND_EXISTING_CAPABILITY`
9. `NEW_REUSABLE_CAPABILITY`
10. `NEW_ADAPTER_OR_PROVIDER`
11. `NEW_EXTENSION_POINT`
12. `EXTERNAL_CONTENT_PACKAGE`
13. `EXTERNAL_EXECUTABLE_EXTENSION`
14. `CORE_RULE_CHANGE`
15. `EXPLICIT_ONE_OFF_EXCEPTION`

`CORE_OWNER_REPLACEMENT` is not a normal extension class. Treat it as an architecture migration requiring explicit impact analysis.

Debugging, discovery, review, migration assessment, and reuse-proof tasks are not additional change classes. If no product/architecture delta is being classified, leave the change classification unset rather than inventing one.

## Decision procedure

1. Identify repository root, applicable instructions, Git state, current requirement, and relevant canonical authorities.
2. Name the exact responsibility, then resolve its current carrier/effective decision path, implementation anchor, and canonical owner status before changing behavior. Do not infer ownership from class/file names or from the mere presence/size of an implementation.
3. Ask whether the request is only data/configuration, a relative modifier, or a substitution at an existing slot.
4. Reuse or compose an existing capability/module/provider before extending architecture.
5. Add a reusable module beneath an existing capability before creating a new capability.
6. Extend an existing capability before creating another system owner.
7. Add a new capability only when a distinct reusable semantic contract is genuinely missing.
8. Add an adapter/provider when the variation is implementation or external integration behind an existing owner.
9. Add a new extension point only when multiple future producers/consumers genuinely need a declared seam.
10. Keep authoritative rules/state at one owner. Consumers provide composition, profiles, identity, and declared variation. External origin grants no extra authority: third-party code/content must use the same owner contracts as first-party producers.
11. Keep definitions/configuration separate from runtime instances and scoped mutable state.
12. Make dependencies explicit. A module/capability asks another owner through its contract rather than reaching into foreign state.
13. If multiple producers exist, converge them before domain behavior:

```text
manual / code / agent / builder / importer / API / external package
→ canonical semantic representation or interface
→ validation
→ canonical responsibility-owner path
→ domain behavior
```

Equivalent content or intent must not gain a producer-specific business-logic path without explicit product justification.

14. For a bug, reverse the same graph:

```text
failing observable
→ runtime/data flow
→ consumer / module / provider
→ capability contract
→ canonical responsibility owner
→ implementation anchor
→ root-cause fix
→ forward re-test
```

## Capability granularity

A capability should be a coherent reusable semantic contract that can be independently understood, implemented, tested, or consumed.

Use these tests:

- If only values differ, prefer data/profile.
- If one concrete behavior differs behind the same contract, prefer module/provider.
- If multiple implementations satisfy the same semantic contract, prefer provider/module variants.
- Do not require a second implementation before recognizing a real reusable capability; implementation count is evidence, not the definition.
- If a genuinely new reusable semantic ability is missing, add a capability.
- Avoid both god-capabilities and capability-sprawl.

## Variation semantics

Prefer explicit variation over copied resolved values.

- Data/configuration: direct declared values.
- Modifier: relative change intended to preserve base propagation.
- Slot substitution: replace one selected module/action/definition at a declared slot.
- Provider substitution: select another implementation behind the same contract.
- Core owner replacement: architecture migration, not ordinary customization.

If multiple modifiers can compose, define deterministic operation semantics, ordering/phases, precedence, scope, lifetime, and conflict behavior. Do not invent a universal modifier engine when the project does not need one.

## Governance for high-risk classes

### CORE_RULE_CHANGE

Require:
- canonical owner identified;
- affected contracts/consumers identified;
- compatibility/migration impact assessed;
- acceptance behavior updated;
- canonical authority artifact updated where applicable;
- focused evidence/tests.

### EXPLICIT_ONE_OFF_EXCEPTION

Require:
- explicit reason existing data/configuration/composition cannot express the need;
- exact owner and scope;
- reason it should remain isolated;
- regression evidence preventing accidental spread;
- revisit condition: if a second meaningful consumer needs similar behavior, reclassify it as a reusable capability/module candidate.

## Public extension-point contract

When a declared extension seam is material, record at least:

- stable ID/name;
- owner system and capability path;
- accepted definitions/interfaces/providers;
- registration/lookup mechanism;
- validation rules;
- required/optional dependencies;
- allowed variation/substitution semantics;
- forbidden foreign writes/bypasses;
- runtime/data trace;
- version/compatibility/migration needs where relevant;
- representative consumers/providers;
- acceptance evidence.

The extension point owns the seam, not all behavior behind it.

## Conditional architecture profiles

Do not force advanced machinery into every project. Activate only when the requirement makes it material.

### Persistent/versioned definitions
Define schema/contract evolution, migration, compatibility, missing-reference behavior, and rollback/recovery where material.

### Distributed/asynchronous boundaries
Define message/contract semantics, timeout/retry behavior, idempotency, delivery guarantees where material, failure propagation, and correlation/trace identity.

### External/dynamic plugin ecosystems
Define discovery, version constraints, dependency/conflict resolution, activation/deactivation, update/uninstall semantics, and failure isolation.

### Untrusted executable extensions
Define trust class, permissions, filesystem/network authority, sandbox/process isolation where appropriate, and failure containment.

### Deterministic/reproducible systems
Define deterministic ordering, authoritative state boundary, time/random sources, and side-effect handling.

### Performance-sensitive systems
Define relevant latency/frame/resource budgets, scheduling constraints where material, and profiling evidence.

### Multi-tenant/contextual configuration
Define scoped configuration, precedence, secrets, feature availability, and tenant isolation where material.

### Polyglot/cross-runtime contracts
Define a language-neutral contract/IDL only when multiple languages/processes/runtimes genuinely share a durable capability boundary.

Use `references/CONDITIONAL_PROFILES.md` whenever any conditional profile is materially present.

## Producer convergence

Different producers may have different ingestion adapters, but equivalent intent should converge on one semantic representation and canonical responsibility-owner path.

Warning signs:
- builder-only runtime logic;
- import-only business logic;
- provider-specific rules duplicated in consumers;
- plugin-local copies of canonical state;
- AI-authored content bypassing normal validation.

## Acceptance evidence

Use the smallest relevant proof set:

- second-consumer/provider reuse;
- producer convergence;
- no parallel state/decision owner;
- forbidden foreign-write rejection;
- dependency rejection where applicable;
- modifier/base propagation where applicable;
- contract/schema compatibility where applicable;
- definition/runtime-state isolation where applicable;
- reverse-debug path;
- actual user-surface or externally observable behavior.

The second consumer/provider should be meaningfully different enough to prove the seam is semantic, not merely named after the first case. Do not implement speculative production features just to satisfy this proof; when no real second consumer exists, use an adversarial design scenario, contract test, isolated fixture, or fresh-agent architecture challenge.

## Do not overbuild

Do not create a plugin loader, registry, interface layer, service container, event bus, manifest format, dependency resolver, sandbox, or migration framework merely because extensibility could theoretically be useful.

A 150-line script can still follow canonical ownership and reuse without becoming a platform.

Prefer:
1. reuse,
2. configuration,
3. correction,
4. simplification,
5. removal,
before new architecture.

## Project artifacts

When the repository already has canonical architecture contracts, update them. Otherwise use the smallest durable representation that the project needs, such as:
- responsibility/system map;
- capability/contract map;
- extension-point registry;
- definition/profile schema;
- producer-to-owner trace;
- version/migration contract;
- focused acceptance evidence.

Do not duplicate exact project truth inside this Skill.

## Read references JIT

Use:
- `references/EXTENSION_ARCHITECTURE_METHOD.md` for the full decision method and domain-neutral examples.
- `references/CONDITIONAL_PROFILES.md` whenever any conditional architecture profile is materially present.
