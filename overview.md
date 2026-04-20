# Design Document: Git-Backed YAML Storage with Indexed Query Layer and Runtime Overlay

## 1. Overview

This document describes the architecture for an interactive web application that:

* Stores **user-owned data as YAML files in GitHub repositories**
* Supports **collaborative, auditable updates via pull requests (PRs)**
* Provides **efficient querying and indexing of YAML data**
* Enables **low-latency, real-time interactions** via an in-memory runtime layer

The system separates concerns across three layers:

| Layer                       | Purpose                  | Characteristics                    |
| --------------------------- | ------------------------ | ---------------------------------- |
| Git (YAML)                  | Canonical data store     | Versioned, auditable, user-owned   |
| Index (PostgreSQL or BaseX) | Queryable representation | Derived, read-optimized            |
| Runtime (Redis)             | Ephemeral state          | Fast, transient, non-authoritative |

---

## 2. Goals and Non-Goals

### 2.1 Goals

* Maintain **human-readable, version-controlled source of truth**
* Enable **granular querying across YAML datasets**
* Support **multi-user concurrent access (10–50 users)**
* Provide **low-latency interactive UX**
* Ensure **clear separation between persistent and transient data**

### 2.2 Non-Goals

* Using a database as the canonical data store
* Supporting arbitrary concurrent writes directly to YAML
* Full ACID guarantees across Git and runtime state

---

## 3. High-Level Architecture

```
           +----------------------+
           |     GitHub Repo      |
           |   (YAML files)       |
           +----------+-----------+
                      |
                      | Webhooks / Sync
                      v
        +-----------------------------+
        |   Index Layer               |
        | (PostgreSQL or BaseX)      |
        +-------------+---------------+
                      |
                      v
              Application Layer
                      |
          +-----------+-----------+
          |                       |
          v                       v
   +-------------+        +--------------+
   |   Redis     |        |   Clients    |
   | (runtime)   |        | (web users)  |
   +-------------+        +--------------+
```

---

## 4. Data Model

### 4.1 Canonical Data (Git-backed YAML)

* Stored in user-controlled GitHub repositories
* Organized as **small, modular YAML files**
* Each file represents a logical entity or configuration unit

#### Example structure:

```
/configs/
  service-a.yaml
  service-b.yaml
/users/
  user-123.yaml
```

### 4.2 Indexed Data (Derived)

* YAML is converted to JSON or structured representation

* Stored in:

  * PostgreSQL (JSONB), or
  * BaseX (XML or XQuery maps)

* Optimized for:

  * filtering
  * searching
  * aggregation

### 4.3 Runtime Data (Redis)

* Ephemeral, fast-changing state
* Examples:

  * user sessions
  * UI state
  * temporary overrides
  * in-progress edits

---

## 5. Data Flow

### 5.1 Ingestion Pipeline

1. GitHub event (push / merge / PR)
2. Webhook triggers ingestion service
3. YAML files are:

   * parsed
   * validated
   * transformed (YAML → JSON/XML)
4. Data is updated in the index layer
5. Relevant Redis keys are invalidated

---

### 5.2 Read Flow

1. Client requests data
2. Application:

   * queries index layer (PostgreSQL/BaseX)
   * fetches runtime state from Redis
3. Results are merged
4. Response returned to client

---

### 5.3 Write Flows

#### A. Persistent Updates (PR-based)

1. User edits data via UI
2. Application:

   * creates branch/fork
   * writes YAML changes
   * opens PR
3. PR is reviewed and merged
4. Webhook triggers re-indexing

**Properties:**

* auditable
* versioned
* authoritative

---

#### B. Runtime Updates (Redis)

1. User performs interactive action
2. Application writes directly to Redis
3. UI reflects change immediately

**Properties:**

* low latency
* not persisted
* not part of canonical data

---

## 6. Technology Choices

### 6.1 Canonical Storage

* GitHub repositories
* YAML format

**Rationale:**

* human-readable
* diffable
* version-controlled
* user-owned

---

### 6.2 Index Layer Options

#### Option A: PostgreSQL (JSONB)

**Strengths:**

* strong concurrency model
* mature ecosystem
* efficient indexing (GIN)
* easy incremental updates

**Weaknesses:**

* less expressive for hierarchical queries
* JSON querying syntax can be verbose

---

#### Option B: BaseX (XQuery)

**Strengths:**

* powerful hierarchical querying
* natural fit for structured data
* expressive XQuery language

**Weaknesses:**

* weaker concurrency model
* more complex integration
* less common in web stacks

---

### Recommendation

* Use **PostgreSQL** for general-purpose systems
* Consider **BaseX** if:

  * queries are complex and hierarchical
  * team has strong XQuery expertise
  * write frequency is low (Git-driven updates only)

---

### 6.3 Runtime Layer

* Redis

**Use cases:**

* caching
* session storage
* temporary state
* real-time updates

**Important constraint:**

> Redis is not a source of truth

---

## 7. Consistency Model

### 7.1 Canonical Consistency

* Guaranteed by Git
* All persistent data changes go through commits

---

### 7.2 Index Consistency

* Eventually consistent with Git
* Updated via webhook-driven ingestion

---

### 7.3 Runtime Consistency

* Ephemeral
* May temporarily diverge from canonical state
* Must not be relied on for persistence

---

## 8. Key Design Principles

### 8.1 Single Source of Truth

* Git (YAML) is the only authoritative store

---

### 8.2 Separation of Concerns

| Concern          | Layer              |
| ---------------- | ------------------ |
| Persistence      | Git                |
| Querying         | PostgreSQL / BaseX |
| Performance / UX | Redis              |

---

### 8.3 Immutable vs Mutable Data

* YAML (Git): immutable history via commits
* Redis: mutable, transient state

---

### 8.4 Small Units of Data

* Prefer many small YAML files over large monolithic ones
* Benefits:

  * better diffs
  * fewer merge conflicts
  * faster indexing

---

## 9. Scalability Considerations

### 9.1 Data Size

* Few GB of YAML:

  * trivial for Git + PostgreSQL/BaseX
  * manageable in Redis (if selective)

---

### 9.2 Concurrency

* Git handles serialized writes via PRs
* PostgreSQL handles concurrent reads efficiently
* Redis handles high-frequency updates

---

### 9.3 Bottlenecks

Potential bottlenecks:

* webhook ingestion lag
* large repo sync times
* inefficient indexing queries

Mitigations:

* incremental updates
* file-level diff processing
* caching hot queries

---

## 10. Risks and Mitigations

| Risk                             | Mitigation                    |
| -------------------------------- | ----------------------------- |
| Drift between index and Git      | webhook + reconciliation jobs |
| Large YAML files                 | enforce modular structure     |
| Redis misuse as persistent store | strict separation of concerns |
| Complex Git workflows            | provide UI abstractions       |

---

## 11. Future Extensions

* Add search indexing (e.g. full-text)
* Support schema validation (JSON Schema)
* Implement conflict detection tools
* Provide visual diff tools for YAML changes

---

## 12. Summary

This architecture combines:

* **GitHub (YAML)** for canonical, versioned data
* **PostgreSQL or BaseX** for efficient querying
* **Redis** for real-time interaction

The key idea is:

> Treat Git as the source of truth, and everything else as derived or ephemeral.

This approach balances:

* transparency (Git)
* performance (Redis)
* scalability and queryability (index layer)

while avoiding the pitfalls of using any single system for all concerns.

---
