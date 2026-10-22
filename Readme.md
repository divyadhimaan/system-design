<div align="center">

# System Design Notes — High Level Design (HLD) Interview Prep

**Free, structured system design notes for interviews: core concepts, 25+ worked design problems, diagrams, and chapter-wise notes from Alex Xu's *System Design Interview* Vol I & II.**

[![GitHub stars](https://img.shields.io/github/stars/divyadhimaan/high-level-design?style=social)](https://github.com/divyadhimaan/high-level-design/stargazers)
[![GitHub forks](https://img.shields.io/github/forks/divyadhimaan/high-level-design?style=social)](https://github.com/divyadhimaan/high-level-design/network/members)
[![Last commit](https://img.shields.io/github/last-commit/divyadhimaan/high-level-design)](https://github.com/divyadhimaan/high-level-design/commits/main)
[![PRs welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](#contributing)

[Concepts](#concepts) •
[Design Problems](#system-design-problems) •
[Alex Xu Vol II](#system-design-interview-vol-ii---alex-xu) •
[Study Roadmap](#study-roadmap) •
[Glossary](./concepts/glossary.md) •
[Resources](#resources)

</div>

---

## Why this repo?

These are my personal notes from preparing for **system design interviews** (SDE-2 / Senior / Staff). Each design problem follows the same interview framework so you can revise quickly before an interview:

1. **Understand the problem & scope**: clarifying questions, functional and non-functional requirements, back-of-the-envelope estimation
2. **High-level design**: APIs, data model, architecture diagram
3. **Deep dive**: scaling, bottlenecks, trade-offs
4. **Wrap up**: key takeaways

> ⭐ If these notes help you, **star the repo** so others preparing for system design interviews can find it too.

---

## Study Roadmap

New to system design? Go in this order:

| Step | Read | Then practice |
|------|------|---------------|
| 1 | [Scalability](./concepts/scalability.md), [Networks](./concepts/networks.md), [Load Balancing](./concepts/load-balancing.md) | [URL Shortener](./design-problems/url-shortener/main.md) |
| 2 | [Databases](./concepts/databases.md), [Caching](./concepts/caching.md), [CDN](./concepts/cdn.md) | [Unique ID Generator](./design-problems/unique-id-generator/main.md), [Key-Value Store](./design-problems/key-value-store/main.md) |
| 3 | [Consistency](./concepts/consistency.md), [Replication](./concepts/data-replication-and-migration.md), [Consensus](./concepts/distributed-consensus.md) | [Consistent Hashing](./design-problems/consistent-hashing/main.md), [Rate Limiter](./design-problems/rate-limiter/main.md) |
| 4 | [Kafka](./concepts/interview-specific/kafka.md), [Message Queues](./concepts/interview-specific/message-queue.md), [Idempotency](./concepts/interview-specific/idempotency.md) | [Notification System](./design-problems/notification-system/main.md), [Payment System](./design-problems/payment-gateway/main.md) |
| 5 | [Trade-offs](./concepts/tradeoffs.md), [Database Selection](./concepts/interview-specific/database-selection.md) | [Chat App](./design-problems/whatsapp-design/main.md), [News Feed](./design-problems/news-feed-system/main.md), [YouTube](./design-problems/video-streaming-service/main.md) |

---

## Concepts

### Fundamentals

| Topic | What you'll learn |
|-------|-------------------|
| [Scalability: Basics](./concepts/scalability.md) | Bottlenecks, components that help scaling, latency vs throughput |
| [Load Balancing](./concepts/load-balancing.md) | Hardware vs software, L4 vs L7, routing algorithms, health checks |
| [Networks](./concepts/networks.md) | OSI model, TCP/IP, IP & MAC addresses |
| [Content Delivery Network (CDN)](./concepts/cdn.md) | How a CDN works, benefits, challenges, providers |
| [Databases](./concepts/databases.md) | How databases store and retrieve data, in-memory vs disk |
| [Caching](./concepts/caching.md) | Cache types, placement, read/write caching strategies |
| [Consistency in Distributed Systems](./concepts/consistency.md) | Linearizable vs eventual consistency levels |
| [Data Replication and Migration](./concepts/data-replication-and-migration.md) | Primary-replica, WAL, CDC, split brain, migrations |
| [Distributed Consensus](./concepts/distributed-consensus.md) | Two-phase commit, Paxos |
| [Rate Limiting](./concepts/rate-limiting.md) | Cascading failures, timer wheels, gateway architecture |
| [Security in Distributed Systems](./concepts/security.md) | TLS, OAuth, SSO, token auth, ACLs |
| [Observability](./concepts/observability.md) | Logging, monitoring, anomaly detection, root cause analysis |
| [Trade-offs](./concepts/tradeoffs.md) | Pull vs push, consistency vs availability, latency vs accuracy |

### Interview Specific

| Topic | What you'll learn |
|-------|-------------------|
| [API Design](./concepts/api-design.md) | Goals of good API design: scalability, extensibility, ease of use |
| [Kafka](./concepts/interview-specific/kafka.md) | Architecture, scalability, real-world use cases |
| [Message Queues in Event-Driven Architectures](./concepts/interview-specific/message-queue.md) | Core components, trade-offs, edge cases |
| [Idempotency in Distributed Systems](./concepts/interview-specific/idempotency.md) | Idempotent APIs and message consumers |
| [Redis & Memcached](./concepts/interview-specific/redis-memcached.md) | Architecture, failure scenarios, when to pick which |
| [Database Selection](./concepts/interview-specific/database-selection.md) | Decision tree, SQL vs NoSQL, NoSQL types |
| [Microservices vs Monoliths](./concepts/Differences/microservices-vs-monoliths.md) | Key differences and when to use each |
| [Glossary](./concepts/glossary.md) | CAP, ACID, sharding, HLS/DASH, VoIP/SIP, NTP, Redis Lua scripts and more |

---

## System Design Problems

### Basic / Distributed Building Blocks

| Problem | Key concepts |
|---------|--------------|
| [Design a URL Shortener (TinyURL)](./design-problems/url-shortener/main.md) | Hash functions, base62, URL redirecting |
| [Design a Rate Limiter](./design-problems/rate-limiter/main.md) | Token bucket, Redis, distributed counters |
| [Design a Key-Value Store](./design-problems/key-value-store/main.md) | CAP, quorum, replication |
| [Design Consistent Hashing](./design-problems/consistent-hashing/main.md) | Hash ring, virtual nodes |
| [Design a Unique ID Generator](./design-problems/unique-id-generator/main.md) | Snowflake, UUID, ticket server |
| [Design a Load Balancer](./design-problems/load-balancer/main.md) | Estimations, control plane APIs, data plane |
| [Design a Web Crawler](./design-problems/web-crawler/main.md) | URL frontier, politeness, deduplication |

### Real-Time Communication

| Problem | Key concepts |
|---------|--------------|
| [Design a Chat Application (WhatsApp)](./design-problems/whatsapp-design/main.md) | Service discovery, message flows, presence, E2E encryption |
| [WhatsApp Calling](./design-problems/whatsapp-calling-app/main.md) | VoIP, SIP, WebRTC |
| [Google Docs: Collaborative Editor](./design-problems/google-docs/main.md) | Metadata, document storage, version history |
| [Design a Notification System](./design-problems/notification-system/main.md) | Push / SMS / email flows, reliability |

### Social Media

| Problem | Key concepts |
|---------|--------------|
| [Design a News Feed System](./design-problems/news-feed-system/main.md) | Feed publishing, fanout service, feed cache |
| [Tinder: Recommendation System](./design-problems/tinder/main.md) | Image storage, chat, matching, recommendations |

### Media Streaming & Storage

| Problem | Key concepts |
|---------|--------------|
| [Design YouTube / Video Streaming](./design-problems/video-streaming-service/main.md) | Upload & streaming flows, transcoding, CDN cost |
| [Hotstar: Live Streaming](./design-problems/hotstar/main.md) | RTMP ingest, multiple resolutions, video delivery |
| [Design Google Drive / Dropbox](./design-problems/google-drive/main.md) | Block servers, sync conflicts, metadata DB |
| [Email Service](./design-problems/emailing-service/main.md) | Services, APIs, DB schemas, search, spam detection |

### Location Based Services

| Problem | Key concepts |
|---------|--------------|
| [Design a Proximity Service (Yelp)](./design-problems/proximity-service/main.md) | Geohash, Quadtree, Google S2 |
| [Design Google Maps](./design-problems/google-maps/main.md) | Graph routing, A*, ETA |
| [Uber: Cab Aggregator](./design-problems/uber/main.md) | Use cases, pricing |
| [Design a Local Delivery Service](./design-problems/delivery-service/main.md) | Item availability, ordering, drive-time lookups |

### Fintech & Workflows

| Problem | Key concepts |
|---------|--------------|
| [Design a Payment System](./design-problems/payment-gateway/main.md) | Design scope, PSP integration, pay-in flow |
| [Workflow Management & Recommendation](./design-problems/workflow-management/main.md) | Processing, fault tolerance, real-time analytics |

---

## System Design Interview Vol II - Alex Xu

Chapter-wise notes from *System Design Interview – An Insider's Guide: Volume 2* by Alex Xu & Sahn Lam.

| #  | Chapter | Notes |
|----|---------|-------|
| 1  | Proximity Service | ✅ [Notes](./design-problems/proximity-service/main.md) |
| 2  | Nearby Friends | ⏳ Coming soon |
| 3  | Google Maps | 🚧 [In progress](./design-problems/google-maps/main.md) |
| 4  | Distributed Message Queue | ⏳ Coming soon |
| 5  | Metrics Monitoring and Alerting System | ⏳ Coming soon |
| 6  | Ad Click Event Aggregation | ⏳ Coming soon |
| 7  | Hotel Reservation System | ⏳ Coming soon |
| 8  | Distributed Email Service | ⏳ Coming soon |
| 9  | S3-like Object Storage | ⏳ Coming soon |
| 10 | Real-time Gaming Leaderboard | ⏳ Coming soon |
| 11 | Payment System | 🚧 [In progress](./design-problems/payment-gateway/main.md) |
| 12 | Digital Wallet | ⏳ Coming soon |
| 13 | Stock Exchange | ⏳ Coming soon |

> 👀 **Watch** the repo to get notified when new chapters land.

---

## Resources

Books & courses referred to while learning:

- *System Design Interview – An Insider's Guide* (Vol I & II): Alex Xu
- [Designing Data-Intensive Applications](https://dataintensive.net/): Martin Kleppmann
- [System Design Primer](https://github.com/donnemartin/system-design-primer)
- [Hello Interview](https://www.hellointerview.com)
- System Design course by Gaurav Sen

### Articles

- [Optimizing the Netflix API](https://netflixtechblog.com/optimizing-the-netflix-api-5c9ac715cf19)
- [Service Discovery in a Microservices Architecture](https://www.f5.com/company/blog/nginx/service-discovery-in-a-microservices-architecture)
- [Numbers Everyone Should Know (Estimations)](https://highscalability.com/numbers-everyone-should-know/)

---

## How to Use This Repo

- **Preparing for an interview?** Follow the [Study Roadmap](#study-roadmap), then pick problems by category.
- **Quick revision?** Each problem's notes end with a wrap-up / key takeaways section.
- **Diagrams** live in [`/diagrams`](./diagrams) and [`/images`](./images), referenced from the notes.

## Contributing

This is a personal learning repo, but contributions are welcome!

- Spotted a mistake or have a better explanation? [Open an issue](https://github.com/divyadhimaan/high-level-design/issues) or a PR.
- Want a problem covered? Open an issue with the problem name.

If this repo helped you, consider giving it a ⭐. It helps others discover it.

---

<sub>Keywords: system design, system design interview, high level design, HLD, distributed systems, scalability, system design notes, Alex Xu system design interview volume 2, interview preparation, software architecture</sub>
