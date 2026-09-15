# Distributed microservices

This is the gap cluster: REST vs async, Kafka, retry, acks, circuit breaker, Saga, Cron overlap, cache events, gateway.

## Design patterns at the edge (Fulcrum)

**API Gateway** — single entry: **routing** (path → service), **cross-cutting** (TLS, rate limit, correlation id), sometimes authn. It is **BFF/gateway pattern**, not “the auth microservice.” Identity/token issuance is a **separate** identity service. Gateway **validates** JWT and routes; services still authorize.

Other names they may want: Database-per-service, Saga, Circuit breaker, Outbox (if you ever dual-write events — do not claim unless true), Sidecar/service mesh (only if you used Istio; Intuit IKS may just be K8s services).

## Sync vs async (ASTON Q6)

| | Synchronous | Asynchronous |
| --- | --- | --- |
| Mechanism | HTTP/REST, gRPC, Feign | Kafka (or RabbitMQ on older resume lines) |
| Caller | Blocks until response | Publishes and continues |
| Coupling | Runtime: callee must be up | Time-decoupled; consumer lag possible |
| Use in your story | Plugin queries; TES create customer in Saga step | Cache invalidation / event consumers — **not** tax-exemption steps |

**Cron is not Kafka.** Cron is “time triggered.” Kafka is “event triggered.”

Recording 67 called the tax-exemption Saga “asynchronous communication.” Correct split: **201 to the user immediately**; **Cron later**; **each Saga step is still a sync HTTP call**. Kafka would be if TES published events and a consumer reacted.

## Why Cron jobs (ASTON Q7)

Manual care-agent processing did not scale and was error-prone. A CronJob polls **pending** exemption requests and runs the pipeline so customers self-serve and we control load on TES. Also: overnight reconciliation, statement generation — pick real examples only.

## Overlapping Cron (HLD §7)

Kubernetes CronJob `concurrencyPolicy`:

- `Forbid` — skip this tick if last Job still running.
- `Replace` — kill old, start new (usually wrong for money/tax).
- `Allow` — overlap (dangerous).

Plus **per-record lock**. Talk both layers.

## Saga (orchestration) — your strongest story

**Why not 2PC:** TES and IFS are external; no shared XA.

**Why orchestration not choreography:** strict sequence; one pipeline owns order and compensation. Choreography = each service reacts to events; harder to see the flow; better when many independent reactions.

**Compensation vs DB rollback (HLD §10):** rollback undoes uncommitted work in one ACID transaction. Compensation undoes **already committed** business effects (e.g. cancel TES customer you created). Best-effort; not always a perfect inverse.

**Idempotency (HLD §16):** stable business key (exemption request id) on TES create; check-before-create or idempotency header; unique constraint locally. Say which your system actually uses.

**If customer create succeeds and DB update fails:** compensate create **or** retry DB with the same id until consistent; never create a second TES customer. Context tells you the stage.

## Retry (ASTON Q9–Q10)

Retry **transient** failures: 502, timeout, `429`. Do **not** retry 400 validation or 403.

Pattern: exponential backoff + jitter + max attempts. At the HTTP client or Resilience4j `Retry`.

**Acknowledgement (recording 67 mixed this with tracing):**

- **HTTP:** success status is the ack; on timeout you **do not know** if TES committed → must be idempotent on retry.
- **Kafka:** consumer `commit` offset after processing. Commit too early → lost message. Commit too late + crash → duplicate → idempotent handler.
- **Kafka producer acks:** `acks=0` fire-forget; `acks=1` leader only; `acks=all` (or `-1`) ISR — durability vs latency. For payments-like data, `acks=all` + idempotent producer.
- **Correlation / tracing id** (transcript “tides”): gateway header so Splunk can stitch hops. **Not** the Kafka ack, and you do not persist it in Mongo “because the app is small.” We already log it.

## Circuit breaker (fix half-open)

Resilience4j / similar. Protects **the caller** from a sick dependency.

| State | Behavior |
| --- | --- |
| **Closed** | Calls pass. Failures counted. |
| **Open** | Calls fail fast (or fallback). No stampede to TES. |
| **Half-open** | After wait duration, **limited** trial calls. Success → closed. Failure → open again. |

Fallback: stale cache, default message, queue for later — **never** “approved.” Retry inside an open breaker makes outages worse. Order: timeout → retry a few times → breaker counts failures.

You mixed “gates” and “half-state” (rec 63 and 67). Use **Closed / Open / Half-open** only. `@Retry` is Resilience4j/Spring Retry — **separate** from the breaker. Do not retry while **open**.

## Kafka (ASTON Q8, high performance)

- **Topic** — named stream of records.
- **Partition** — ordered log; parallelism unit. Key (e.g. `customerId`) hashes to a partition so that customer’s events stay ordered.
- **Producer** — appends to a partition.
- **Consumer group** — each partition assigned to **one** consumer in the group (competing consumers). More partitions than consumers for scale.
- **Offset** — position in the partition.
- **Throughput:** more partitions, batching, compression, enough consumers, avoid hot keys.
- **Poison message:** max retries then dead-letter topic; do not block the partition forever.

You: “I have worked on the consumer side — processing records, committing offsets, keeping handlers idempotent.” Do not design a 50-partition cluster if you did not.

**Recording 67 wrong picture:** a producer does **not** “split a large file upload into topics.” One **topic** has **partitions**; the producer sends **records** (optionally keyed). The broker does not “look up consumers and push a job.” Consumers **poll**; the group coordinator assigns partitions. Retry + DLQ is for poison messages, not “the circuit breaker gate.”

## Redis strategy (recording 65 — stay honest)

Do **not** call Redis “the frontend cache” or mix it with `useReducer`.

Typical uses you can defend:

- **TTL cache-aside:** read DB on miss; set key with TTL; on write, **delete** the key (or publish a cache event — Virtusa).
- **Session store:** gateway/auth session id in Redis with TTL so pods stay stateless.
- **Lock:** exemption record lock with TTL (HLD overlap).

**LRU** is Redis `maxmemory-policy` (`allkeys-lru` / `volatile-lru`) when memory is full. Only claim LRU if you or platform set that policy. TTL is the policy you should always mention for sessions.

Hibernate **second-level cache** is a specific JPA integration. Do not say “Redis is our L2 cache” unless the `RegionFactory` was actually Redis.

Retry, fallback, and circuit breaker are **not** “callback patterns.” Retry = try again; fallback = degraded response; CB = stop calling. See the table below.

## Cache events (Virtusa L2)

Typical pattern: write DB, publish `CustomerUpdated`, cache consumer **evicts or refreshes Redis**. Risk: DB commit vs publish (outbox if they go deep). Simpler answer: TTL + explicit eviction on writes you own. Tax exemption status: do not cache “approved” without TTL if downstream can reverse.

## Correlation ID / tracing

Gateway generates `X-Correlation-Id` if missing; every service logs MDC; Feign/Kafka header propagation; Splunk `correlationId=`. Same id from plugin click to TES error. Wavefront/Prometheus for latency; Splunk for the message.

## Resilience summary table

| Tool | Problem it solves |
| --- | --- |
| Timeout | Bound wait |
| Retry | Blips |
| Idempotency | Duplicate side effects |
| Circuit breaker | Cascading slowness |
| Bulkhead | Thread pool isolation |
| Saga compensation | Multi-step business undo |
| DLQ | Poison events |
