# Design a Unique ID Generator in Distributed Systems

> A service or algorithm that generates **globally unique identifiers** across distributed systems.

**Properties can vary:**
- **Globally unique**: no collisions.
- **K-sortable**: IDs roughly increase with time → helps in DB sharding.
- **Compact**: smaller than UUIDs for efficiency.
- **Stateless vs Stateful**: depending on design.

> [!NOTE]
> A naive approach is to use a primary key in a central database with **auto-increment**. But this creates a **bottleneck** and **single point of failure**.

---

## Table of Contents
- [Problem Statement](#problem-statement)
- [Step 1: Understand Requirements and Design Scope](#step-1-understand-requirements-and-design-scope)
  - [Requirements](#requirements)
- [Step 2: High-Level Design](#step-2-high-level-design)
  - [Option 1: Multi-Master Replication](#option-1-multi-master-replication)
  - [Option 2: UUID](#option-2-uuid)
  - [Option 3: Ticket Server (Flickr)](#option-3-ticket-server-flickr)
  - [Option 4: Snowflake (Twitter)](#option-4-snowflake-twitter)
- [Twitter Snowflake Design](#twitter-snowflake-design)
- [Follow-Ups](#follow-ups)
  - [1. Clock Synchronization](#1-clock-synchronization)
  - [2. Section Length Tuning](#2-section-length-tuning)
  - [3. Handling Machine Failures](#3-handling-machine-failures)
  - [4. High Availability](#4-high-availability)
  - [5. Choosing ID Structure Depending on Requirements](#5-choosing-id-structure-depending-on-requirements)
- [References / Articles](#references--articles)

---

## Problem Statement

In large-scale distributed systems, every entity (user, order, transaction, message, etc.) needs a unique identifier.

**Challenges:**

| Challenge | Why it matters |
|-----------|----------------|
| **Collisions** | If multiple nodes generate IDs independently, they might overlap. |
| **Scalability** | Must support billions of IDs efficiently. |
| **Ordering** | Sometimes we need time-ordered IDs (e.g., Kafka offsets, Twitter Snowflake). |
| **Availability** | System should not depend on a single machine or DB auto-increment (bottleneck, SPOF). |
| **Low latency** | ID generation must be very fast, ideally < 1 ms. |

---

## Step 1: Understand Requirements and Design Scope

> **Q.** What are characteristics of the unique IDs?
>
> **A.** IDs must be globally unique and k-sortable (k: number of servers).

> **Q.** For each new record, does ID increment by 1?
>
> **A.** The ID increments by timestamp, not by 1. IDs should be ordered by date.

> **Q.** What is the length of the ID? Is there a maximum length?
>
> **A.** 64 bits, i.e., IDs should fit within a 64-bit integer. Keeping it storage friendly.

> **Q.** Do IDs only contain numbers, or can they include letters/symbols?
>
> **A.** IDs are only numeric.

> **Q.** What is the expected scale of ID generation (e.g., number of IDs per second)?
>
> **A.** The system should handle up to 10,000 ID requests per second.

### Requirements

**Functional**
- IDs must be unique across distributed systems.
- IDs should be k-sortable (roughly ordered by date).
- IDs should be numeric.
- IDs should fit within 64 bits.
- The system should handle up to **10,000 ID requests per second**.

**Non-Functional**
- The system should be **highly available** and **fault-tolerant**.
- The system should have **low latency** (ID generation should be < 1 ms).

---

## Step 2: High-Level Design

We have multiple options to design a unique ID generator.

### Option 1: Multi-Master Replication

- A system where multiple masters (nodes) can generate IDs concurrently.
- Each master has a guaranteed unique range or namespace, avoiding collisions.
- Replication ensures consistency of state if ranges or configurations change.

<p align="center">
  <img src="../../images/uniqueIdGen/multimaster.png" alt="Multi-master replication" width="550"/>
</p>

| ✅ Pros | ❌ Cons |
|--------|--------|
| Solves scalability issue, since IDs can scale with number of nodes. | Hard to scale with multi data centers. |
| | IDs are not time ordered. |

### Option 2: UUID

- 128-bit identifier.
- Generated using algorithms that ensure uniqueness across space and time.
- No central coordination needed.
- Each node can have its own ID generator.

<p align="center">
  <img src="../../images/uniqueIdGen/uuid.png" alt="UUID" width="550"/>
</p>

| ✅ Pros | ❌ Cons |
|--------|--------|
| Simple to implement. | Not k-sortable. |
| No central point of failure. | Larger size (128 bits). |
| ID generator can easily scale with servers. | Not numeric. |

### Option 3: Ticket Server ([Flickr](#references--articles))

- A centralized server that generates IDs.
- Clients request IDs from this server.
- The server ensures uniqueness and ordering.

<p align="center">
  <img src="../../images/uniqueIdGen/ticket-server.png" alt="Ticket server" width="550"/>
</p>

| ✅ Pros | ❌ Cons |
|--------|--------|
| Numeric IDs. | Single point of failure. (Multiple instances can be setup, but data synchronization is a challenge.) |
| IDs are k-sortable. | Scalability issues with high load. |
| Works for small to medium scale applications. | |

### Option 4: Snowflake ([Twitter](#references--articles))

- A distributed ID generation system.
- Instead of generating the ID directly, divide ID into different sections.
- Combines timestamp, datacenter ID, machine ID, and sequence number to create a unique 64-bit ID.
- Each node can generate IDs independently.

<p align="center">
  <img src="../../images/uniqueIdGen/snowflake.png" alt="Twitter Snowflake" width="550"/>
</p>

| ✅ Pros | ❌ Cons |
|--------|--------|
| Numeric IDs. | More complex to implement. |
| IDs are k-sortable. | Requires careful configuration of machine IDs to avoid collisions. |
| Highly scalable and fault-tolerant. | |
| Low latency. | |

---

## Twitter Snowflake Design

Snowflake is a 64-bit unique ID generation algorithm developed by Twitter.

**Provides:**
- Global uniqueness.
- Time ordering.
- High throughput (millions of IDs/sec).

### Components of the 64-bit ID

```
| 1 bit |       41 bits        |    5 bits     |   5 bits   |     12 bits     |
|-------|----------------------|---------------|------------|-----------------|
|   0   |      Timestamp       | Datacenter ID | Machine ID | Sequence Number |
```

| Section | Bits | Description |
|---------|:----:|-------------|
| `Sign Bit` | 1 | Always 0. For future use. |
| `Timestamp` | 41 | Milliseconds since a custom epoch (use Twitter Snowflake default epoch). Gives **~69 years** of IDs. |
| `Datacenter ID` | 5 | Supports up to **32 datacenters**. |
| `Machine ID` | 5 | Supports up to **32 machines per datacenter**. |
| `Sequence Number` | 12 | Supports **4096 IDs per machine per millisecond**. Incremented by 1 and resets to 0 every millisecond. |

### How the sections are assigned

**Fixed at startup** — Datacenter ID & Machine ID
- Configured via environment variables or configuration files, set up at startup time and fixed for the lifetime of the service instance.
- If a machine restarts, it must ensure it uses the **same Machine ID** to avoid collisions.

**Generated at runtime** — Timestamp & Sequence Number
- **Timestamp** is the part that makes IDs sortable by time.
- **Sequence Number** allows multiple IDs to be generated within the same millisecond.
  - In theory a machine can generate up to 4096 IDs per millisecond.
  - If the sequence number exceeds 4095 within the same millisecond, the generator must **wait until the next millisecond** to continue generating IDs.

---

## Follow-Ups

### 1. Clock Synchronization
- In our design, we assume ID generation servers have the same clock.
- This assumption might not be true when a server is running on multiple cores.
- The same challenge exists in multi-machine scenarios.

> **Q.** How to handle clock drift between machines?
>
> **A.** Use **NTP** for clock synchronization. If a machine's clock goes backward, it should wait until the clock catches up to avoid generating duplicate IDs.

### 2. Section Length Tuning
For example, fewer sequence numbers but more timestamp bits are effective for low concurrency and long-term applications.

| | Low concurrency applications | High concurrency applications |
|---|---|---|
| **Examples** | Moderate-traffic SaaS, financial records, IoT logs | Twitter, TikTok, large-scale e-commerce |
| **Need** | Don't need to generate millions of IDs/sec. | Need thousands of IDs/ms per node. |
| **Sequence bits** | Can afford fewer sequence bits. | Need more sequence bits (12–14). |
| **Trade-off** | ✅ More timestamp bits → IDs remain valid for decades/centuries. | ⚠️ Sacrifice timestamp lifespan → maybe only ~50–70 years, but that's acceptable. |

### 3. Handling Machine Failures
- If a machine goes down and comes back up, it must ensure it uses the **same Machine ID** to avoid collisions.
- If a machine is permanently decommissioned, its Machine ID can be reused after a safe period (e.g., after the maximum timestamp duration).

### 4. High Availability
- Since an ID generator is a **mission-critical system**, it must be highly available.
- Deploy multiple instances behind a load balancer.

### 5. Choosing ID Structure Depending on Requirements
Uniqueness only vs time-ordering.

| Criteria | **UUID (v4)** | **Hash-based (MD5/SHA-1)** | **Snowflake** | **ULID** | **DB Auto-Increment (Sharded)** |
|----------|---------------|----------------------------|---------------|----------|---------------------------------|
| **Uniqueness** | ✅ Very high (128-bit, random) | ✅ Deterministic per content | ✅ Guaranteed (via node+seq) | ✅ Very high (time+rand) | ✅ Guaranteed within shard |
| **Time Ordering** | ❌ None | ❌ None | ✅ Strong (timestamp bits) | ✅ Strong (lexicographic) | ✅ Weak (approximate, shard-based) |
| **Size** | 128-bit (16B) | 128–160-bit | 64-bit (8B) | 128-bit (16B) | 64-bit (8B) |
| **Readability (compact)** | ❌ Long string | ❌ Long string | ✅ Compact (int64) | ❌ Long (26 chars) | ✅ Compact (int64) |
| **Coordination Needed** | ❌ None | ❌ None | ⚠️ Yes (unique machine IDs) | ❌ None | ⚠️ Yes (shard offsets) |
| **Collision Risk** | Negligible | None (same input → same ID) | None | Negligible | None within shard |
| **Ordering Stability** | ❌ Random | ❌ Random | ✅ Time-based | ✅ Time-based | ⚠️ Only within shard |
| **Best Use Cases** | Session IDs, cache keys, security tokens | File deduplication, content IDs | Tweet IDs, orders, events, logs | DB keys, logs, distributed systems | Legacy systems, small-scale sharding |

---

## References / Articles

- [Ticket Servers at Flickr](https://code.flickr.net/2010/02/08/ticket-servers-distributed-unique-primary-keys-on-the-cheap/)
- [Twitter Snowflake](https://blog.x.com/engineering/en_us/a/2010/announcing-snowflake)

---

[⬅ Back to README](../../Readme.md)
