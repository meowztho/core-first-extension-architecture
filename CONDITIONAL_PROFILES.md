# Conditional Architecture Profiles

These profiles are NOT universal requirements. Apply them only when the project characteristic is materially present.

## 1. Persistent / Versioned Definitions

Trigger:
- saved definitions;
- external packages;
- durable user-authored configuration;
- long-lived schemas/contracts.

Define as needed:
- schema/contract version;
- deterministic migrations;
- preservation of valid old values;
- defaults for new fields;
- removed/renamed reference behavior;
- unsupported future-version behavior;
- rollback/recovery when material;
- migration tests and idempotence where relevant.

## 2. Distributed / Asynchronous Boundary

Trigger:
- process/network boundary;
- queues, streams, webhooks, remote services;
- delayed/retried operations.

Define as needed:
- command/event/query contract;
- timeout;
- retry;
- idempotency;
- duplicate delivery handling;
- ordering requirements;
- failure propagation;
- correlation/trace identity;
- consistency/transaction strategy where material.

Do not require a universal event bus.

## 3. Dynamic / External Plugin Ecosystem

Trigger:
- runtime discovery/loading;
- independently versioned extension packages;
- enable/disable/update/uninstall.

Define as needed:
- identity/namespace;
- manifest/schema;
- discovery;
- required/optional dependencies;
- version constraints;
- conflict resolution;
- deterministic provider selection;
- activation/deactivation;
- update/uninstall behavior;
- failure/quarantine behavior;
- compatibility evidence.

### Boundary note

A statically configured external API provider is normally an adapter/provider, not automatically a dynamic plugin ecosystem. Activate this profile only when independent discovery/loading/version/lifecycle behavior is actually required.

## 4. Untrusted Executable Extension

Trigger:
- third-party or untrusted executable code.

Prefer data/content extensions when sufficient.

Define:
- trust class;
- explicit permissions;
- filesystem/network/process authority;
- foreign-state write prohibition;
- sandbox/process isolation where appropriate;
- credential scope;
- audit trail;
- failure containment.

Policy alone is not enforcement. Use platform mechanisms when a boundary must be guaranteed.

## 5. Deterministic / Reproducible Execution

Trigger:
- simulation;
- replay;
- rollback;
- deterministic testing;
- reproducible workflows.

Define:
- deterministic execution ordering where material;
- authoritative state boundary;
- time/random sources;
- side-effect boundary;
- snapshot/replay semantics if needed.

## 6. Performance-Critical Runtime

Trigger:
- latency SLA;
- frame budget;
- high-throughput processing;
- constrained memory/CPU/energy.

Define:
- relevant budgets;
- allowed blocking/async behavior;
- scheduling/ordering constraints where material;
- measurement/profiling evidence;
- failure/degradation behavior.

## 7. Multi-Tenant / Contextual Configuration

Trigger:
- tenant-, environment-, region-, user-, or deployment-specific variation.

Prefer explicit scoped configuration/profile data over duplicated systems.

Define:
- configuration precedence;
- scope;
- secret handling;
- feature availability;
- tenant isolation where material.

## 8. Polyglot / Cross-Runtime Contracts

Trigger:
- multiple languages/processes/runtimes implementing the same durable capability contract.

Define a language-neutral contract when needed:
- OpenAPI;
- Protobuf/gRPC;
- JSON Schema;
- AsyncAPI;
- another suitable IDL.

Do not impose an IDL on single-process internal code that does not need it.
