# Question bank — model answers (45–90 seconds)

Speak these. If a topic needs depth, the chapter link is the study source. Do not read annotations aloud.

---

## Fulcrum

**DI and how it helps testing**  
Spring constructor-injects services. In tests I pass a stub TES client and assert the Saga retries without a network. `@WebMvcTest` plus a mock service proves the controller mapping. See [04](04-backend-java-spring.md).

**Guide and mentor juniors**  
Pair on the first ticket, PR review for design and tests, Splunk RCA together. Comfortable with 8–10 person teams (Discover lead, Intuit squad). See [01](01-intro-and-accuracy.md).

**Spring Batch, large data, exceptions**  
Chunk reader/processor/writer, transaction per chunk, skip poison rows, retry transient DB, job repository for restart. My tax-exemption job is a scheduled pipeline with per-record try/catch rather than Batch — I would use Batch if we needed restartable chunks at millions of rows. See [04](04-backend-java-spring.md).

**Microservice pattern for auth and routing**  
**API Gateway** for routing and edge concerns; **identity service** issues/validates tokens; **each service** enforces authorization. That is gateway + token-based security, not “gateway is the only auth.” See [05](05-distributed-microservices.md).

---

## LTIMindTree

**Servlet MCQ**  
Servlet is the old Java HTTP worker (`service`/`doGet`). Spring MVC `DispatcherServlet` fronts `@RestController`. Filters/interceptors wrap cross-cutting concerns.

**React MCQ**  
One-way data, state vs props, keys in lists, hooks only at top level.

**Java 8 Stream API**  
Laziness, intermediate vs terminal, `filter`/`map`/`collect`, `groupingBy`. Drills in [10](10-live-coding-drills.md).

**PostgreSQL**  
If the job is Postgres and you used MySQL/Oracle: “same SQL fundamentals, `LIMIT`/`OFFSET`, window functions, differences in UPSERT (`ON CONFLICT`). I can ramp.”

**Coding skeletons**  
Implement only what they asked. Exception handling: catch specific, wrap in domain exception, do not swallow. Spring Boot CRUD: entity, repo, service, controller, validation, 404. React: controlled inputs + list keyed by id.

---

## ASTON / Recording 67 (2026-09-11, ~53 min)

Same question list as “most technical questions.” ASR names you **Puevo Patel**; tools came out **Cloud Board / Cursory**. Speak **Vaibhav**, **Claude**, **Cursor**.

**1. Yourself and stack** — 90s intro in [01](01-intro-and-accuracy.md). Do not ramble MCP in the intro.

**2. Cloud AWS/Azure/GCP** — AWS-hosted / CloudWatch / S3; **not Azure**. Day to day is K8s. [07](07-docker-k8s-cicd-aws.md).

**3. AI tools** — Cursor + Claude for coding and Java 21 refresh; MCP only if you used it; no LLM training; paid tools if that is policy. [01](01-intro-and-accuracy.md).

**4. Scalable backend challenges**  
Keep: Java 8→21, records/sealed, React class→function + TS, GraphQL instead of fat REST, Splunk + Chrome perf, indexes/native SQL, smaller bundles.  
Drop unless true: virtual threads in prod, GraalVM made Boot 3 faster.  
Also valid: HPA, CB, Cron overlap, TES idempotency.

**5. REST + communication** — `POST /exemption-requests`, 201, GET by id, Feign timeouts. Then gateway, rate limit, Redis, LB. Rec 67 skipped the resource design. [02](02-request-path-ui-to-k8s.md).

**6. Sync vs async** — HTTP waits. User create = 201. Cron + orchestration Saga = deferred work. **Not** Kafka for those six steps. [05](05-distributed-microservices.md).

**7. Why Cron** — care-agent manual work did not scale; batch to TES. Optional **only if true**: email + Avalara cert window + one open request. [02](02-request-path-ui-to-k8s.md).

**8. Kafka** — topic / partition / consumer group / offset / poll. Rec 67 “split file into topics, broker finds consumers” is wrong. [05](05-distributed-microservices.md).

**9. Retry** — `@Retry` / Resilience4j on **transient** errors. Circuit breaker is **Closed/Open/Half-open**, not gates. Do not retry in **open**. [05](05-distributed-microservices.md).

**10. Ack** — HTTP status or Kafka commit. Correlation id is tracing (Splunk). Not Mongo key-value “because we are small.” [05](05-distributed-microservices.md), [08](08-observability-prod.md).

**11–12. Docker** — Dockerfile, same digest, pods scale. Not “Docker YAML” unless Compose local. [07](07-docker-k8s-cicd-aws.md).

**13. AWS** — S3, CloudWatch, K8s reliability story. Kinesis/Lambda/Spring Cloud AWS only if shipped. [07](07-docker-k8s-cicd-aws.md).

**14. React or Angular** — React ~4 years Intuit; Angular 8 Discover.

**15. Angular state + perf** — services + RxJS, pagination, `async` pipe. Not “engine model.” [03](03-frontend.md).

**16. Able to write lots of code?** — Yes, but live syntax without an assistant is something I drill. Then do the exercise.

**17. Streams grouping** — you narrated the four steps well. Type the gold in [10](10-live-coding-drills.md). Prefer `BigDecimal`, not `summingDouble`.

**18. Stored proc vs function + exceptions** — table in [06](06-data-sql-acid-streams.md). Do **not** answer with `@ControllerAdvice`.

**19–20. SQL top 3** — `GROUP BY`, `SUM`, `ORDER BY total DESC LIMIT 3`. `DENSE_RANK` if they want ties. You had this.

**21. ACID** — fund transfer. Durability = commit/WAL, not “East and Central regions” (that is HA). [06](06-data-sql-acid-streams.md).

**22. AI besides programming** — adoption and review bar; Cypress-from-Claude is trial; not fraud ML. [01](01-intro-and-accuracy.md).

**23–24. Position / customer-facing** — JD is Java + Angular lead-ish; your history is customer-facing finance UIs plus backend + agile.

**25. Mentor juniors** — yes. [01](01-intro-and-accuracy.md).

**Also extract list:** Saga, circuit breaker, correlation ID, K8s scaling — [05](05-distributed-microservices.md), [07](07-docker-k8s-cicd-aws.md), [08](08-observability-prod.md).

---

## Virtusa L2 (VCB)

**Project workflow** — analysis → plugin UI + API → Jenkins version → pre-prod → IXP flag → prod sanity → Splunk. Backend image → Argo/K8s.

**Develop microservice end to end** — API contract, Spring service, DB, Docker, deploy, actuator health, Splunk dashboard.

**Cache event** — write then evict Redis or consume an update event; TTL safety net. [05](05-distributed-microservices.md).

**Maker-checker** — two-person approve before money moves. [04](04-backend-java-spring.md).

**Payment domain** — idempotency, retry only technical failures, reversal vs refund. Exemption is adjacent finance, not card auth.

**Criteria API / Predicate / Records** — [04](04-backend-java-spring.md), [06](06-data-sql-acid-streams.md).

**Authentication** — JWT/OAuth, identity service, service-level authZ. [02](02-request-path-ui-to-k8s.md).

**DB tuning** — indexes, explain, pagination. [06](06-data-sql-acid-streams.md).

**Resilience** — timeout, retry, CB, bulkhead, Saga. [05](05-distributed-microservices.md).

**Docker image** — build once, tag SHA, run as pod. [07](07-docker-k8s-cicd-aws.md).

---

## Recording 63 — corrected short answers

**Java 21 features** — records (immutable, no setters), sealed types, virtual threads only if you used them (do not invent).

**Migration 8 → 21** — mostly compatible; we chose records/sealed; AI-assisted mechanical edits; tests and review.

**Functional vs interface** — SAM vs many abstracts; `@FunctionalInterface` enforces.

**Two `accept` methods** — if both abstract, it is not a functional interface; annotation fails compile.

**Default methods** — interface evolution without breaking implementors.

**HashMap** — hash, bucket, equals, replace same key, list then treeify at 8. Not thread-safe.

**Same key content** — `equals` true → overwrite value.

**ConcurrentHashMap** — concurrent bins/CAS, **not** “16 segments,” iterators do not fail-fast the same way.

**`@SpringBootApplication`** — configuration + auto-config + component scan; no magic Maven download.

**Skip packages** — exclude filters / `exclude` auto-config.

**Global exceptions** — `@RestControllerAdvice`.

**Log monitoring** — Splunk queries, Wavefront latency, CloudWatch where AWS logs landed.

**`@Transactional`** — proxy, public, no self-invoke, rollback unchecked by default.

**Microservice patterns used** — orchestration Saga, circuit breaker, gateway, plugin MFE — not every textbook pattern.

**Circuit breaker why** — fail fast + fallback, protect threads.

**Half-open** — trial calls after cooldown; success closes, failure opens. [05](05-distributed-microservices.md).

**Second highest salary** — `DENSE_RANK` or `LIMIT 1 OFFSET 1` on distinct. [06](06-data-sql-acid-streams.md).

**React years** — four; Angular before.

**MFE** — plugins/widgets, Jenkins versions, portal pin, IXP flags.

**Hooks** — state/reducer/context; memo/callback for referential stability, not magic.

**10k rows** — server pagination, not only `useMemo`.

**Live React task** — see [10](10-live-coding-drills.md). If stuck, narrate filter-sort-map-useState.

**UT** — RTL, Jest, Cypress, Selenium legacy, regression, RCA.

---

## Recording 64 (2026-09-11) — Java 21, immutability, live Streams + SQL

~28 minutes. Live coding: **least-repeated character** and **third-highest salary**.

**Java version** — Java 21 now; came from 8. Spring Boot 2 → 3 needed 17; platform chose 21. Records + sealed where they fit. [04](04-backend-java-spring.md).

**Record benefits** — drop POJO boilerplate; canonical constructor; accessors; `equals`/`hashCode`/`toString`; **immutable references**. Not “Java generates setters.”

**Are records 100% immutable?** — **No.** If a component is a mutable `List`, callers can mutate it. Defensive copy in the compact constructor, or use unmodifiable lists.

**What immutable means** — object state cannot change after construction. Security example: a leaked mutable connection config could be altered; an immutable value cannot.

**Custom immutable class** — `final` class, `private final` fields, constructor that assigns them, getters only, **defensive copies** of collections. Constructor is usually **public** (or a public factory). Private constructor without a factory is a trap. [04](04-backend-java-spring.md).

**`@Bean` vs `@Component`** — scan vs factory method; you do not put both on one class. [04](04-backend-java-spring.md).

**`@Transactional` on private** — **no**. Proxy + public method. Default rollback = unchecked; checked needs `rollbackFor`. You already had this right — keep it tight. [04](04-backend-java-spring.md).

**Least-repeated character (live)** — confirm: lowest count, not first unique. `groupingBy` + `LinkedHashMap` + `min(comparingByValue)`. Gold in [10](10-live-coding-drills.md).

**Secure REST** — JWT/OAuth2; token in cookie or `Authorization`; Spring Security validates; roles in payload; **service still authorizes**. [02](02-request-path-ui-to-k8s.md).

**API Gateway vs load balancer** — gateway = **entry + routing + cross-cutting** across *different* services. Load balancer = **spread traffic across instances of the same service**. You can have both (LB in front of gateway, or service mesh).

**Pagination 2000 rows** — **always backend** `limit`/`offset` (or cursor). UI-only pagination still downloads 2000 rows. You answered this well — keep it.

**Third-highest salary** — `DENSE_RANK() = 3`. [06](06-data-sql-acid-streams.md).

---

## Recording 65 (2026-09-15) — serialization, scopes, Redis, CI/CD, React hooks, JWT

~40 minutes. Several answers were directionally right and **worded wrong**. Speak the lines below, not the transcript.

**Intro / years / structure** — 90s script in [01](01-intro-and-accuracy.md). Frontend: React ~4 years, Angular ~3. Backend 12. Tax exemption = plugin + K8s Cron + orchestration Saga. S3 (or equivalent object store) for **certificates**.

**Title** — Senior Lead on paper; **IC** on Intuit. [01](01-intro-and-accuracy.md).

**Serialization** — Java `Serializable` for object streams; `transient` hides a field. REST APIs: Jackson `@JsonIgnore`. [04](04-backend-java-spring.md).

**A serializable → B → C** — **yes**, C serializes.

**Prevent C** — `writeObject`/`readObject` throw `NotSerializableException`. Not “override serializable.”

**Bean scopes** — default **singleton**; also prototype, request, session. [04](04-backend-java-spring.md).

**Caching** — Redis with **TTL** (sessions or cache-aside). LRU only if `maxmemory-policy` is LRU. **`useReducer` is not caching.** [05](05-distributed-microservices.md), [03](03-frontend.md).

**Microservice patterns** — Saga, API gateway, circuit breaker, retry, fallback. Do not bundle retry/fallback as “callbacks.” [05](05-distributed-microservices.md).

**Docker / K8s / AWS** — ~5 years containers; AWS shallow: **S3 certificates**, CloudWatch if used. [07](07-docker-k8s-cicd-aws.md).

**CI/CD** — Git → Jenkins (exploring GHA) → test → **Dockerfile** image → registry → K8s (Argo CD GitOps). Ingress routes host/path to a Service. Horizontal scale = more replicas, not “ports deploy containers.”

**Validate image** — CI tests + successful image build (+ scan). Argo health is **deploy** validation, not build validation.

**Argo CD** — GitOps sync + UI for health/rollback. You may only use the dashboard; still say GitOps.

**K8s files** — Deployment, Service, Ingress, CronJob. Honest: platform did one-time setup.

**Local** — Spring Boot run, compose, Swagger. Good — keep.

**Why Docker** — parity across env. Good — keep.

**React process** — plugins, app fabric pin, React 16→18, TS, GraphQL fields, drop dead deps. [03](03-frontend.md).

**`useReducer`** — action-driven UI state, not “which component to trigger after API.”

**Redux** — know it; daily plugin is Context + reducer; RTK on SUBS-UI. [01](01-intro-and-accuracy.md).

**`useEffect`** — after-render side effect; **not** a page reload. `[]` = mount once.

**`useMemo` vs `React.memo`** — value vs component. You had only used `useMemo` — learn the one-liner in [03](03-frontend.md).

**Vault** — secret store for the **workload**. K8s/AppRole into Vault. Not “OAuth2 link to the Vault container.” User JWT is separate. [07](07-docker-k8s-cicd-aws.md).

**OAuth2 + JWT** — login → JWT (payload + signature) → filter validates → roles. Good skeleton; do not say the filter throws to `@ExceptionHandler`.

**Exceptions** — `@RestControllerAdvice` for controllers. JWT layer: **AuthenticationEntryPoint 401**. [04](04-backend-java-spring.md).

**`@RestControllerAdvice` for JWT** — usually **no**. Entry point / access-denied handler. Resolver-from-filter only if they push.

---

## HLD PDF §20 — keep using your existing guide

Orchestration vs choreography, create-then-DB-fail, duplicate customer, retryable errors, reverse compensate, context contents, Cron overlap, Splunk Saga, introducing Kafka later, compensation vs rollback, Redis vs DB, CB/fallback, UI hide vs API authZ.

---

## If they ask about the personal banking capstone

“Design exercise: Spring Cloud Gateway, JWT, Eureka, Resilience4j, targets like p95 200 ms. It is not production. My production depth is Intuit QuickBooks and Discover boarding.”
