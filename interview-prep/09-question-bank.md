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

## ASTON / Recording 67 (2026-09-11, ~53 min) — TurboScribe (~30 min) + rest

Interviewer **Shravan** (architect). Coordinator **Nishant**. ASR: **Vibro / Koivo**, **cloud port**, **Gravel VM**, **Alvara**, **RedJX**, **Discord card**, **EMVG**, **FedEase**. Speak **Vaibhav Patil**, **Claude**, **Avalara**, **RxJS**, **Discover**, **GraalVM**.

Free TurboScribe stopped at the **CustomerTransaction** Streams prompt. Q18–25 still use the notes below.

**1. Intro** — 90s script in [01](01-intro-and-accuracy.md). Employer: **Virtusa** (Plug and Play), client **Intuit**, product **QuickBooks**. Do not open with MCP.

**2. Cloud** — AWS yes, Azure no. Then stay on K8s. [07](07-docker-k8s-cicd-aws.md).

**3. AI** — **Claude** and **Cursor**. Cursor **skills** + **MCP** only if true. Cypress is tests, not an LLM. No model training. Paid tools. You **shared the whole screen** with an editor — close secrets first. [01](01-intro-and-accuracy.md).

**4. Challenges**  
Keep: Java 8→21 and Boot 2→3 (needs 17), records/sealed, class→function + TS, GraphQL vs fat REST, Splunk + Chrome, native SQL, smaller bundles.  
“Trying virtual threads” is OK. **GraalVM made Boot 3 faster** — drop unless native-image shipped.

**5. REST + communication** — Shravan asked **how you design the endpoint**. Lead with `POST /exemption-requests` → 201, GET by id, idempotency, Feign timeouts. SOLID/Docker/gateway/LB/Redis are not the API design. [02](02-request-path-ui-to-k8s.md).

**6–7. Sync / Cron / Avalara** — Create → **201 CREATED**. Cron ~30 min. Email → **Avalara** upload, **30 days**, **one open request**. Status created → in progress → completed after vendor verify. **Keep:** this workflow is **not Kafka**; small scheduled Saga. [02](02-request-path-ui-to-k8s.md), [05](05-distributed-microservices.md).

**8. Kafka** — topic / **partition** / consumer group / offset / **poll**. Not “split a file into topics.” [05](05-distributed-microservices.md).

**9. Retry** — `@Retry` (ASR “accurate retry”) on transients. CB: Closed / Open / Half-open — not gates, not “that port.” [05](05-distributed-microservices.md).

**10. Ack** — HTTP status or Kafka commit. **TID / correlation id** is tracing. ASR **FedEase**: do not invent Mongo as an ack store. [08](08-observability-prod.md).

**11–12. Docker** — same image across env. **Dockerfile**, not Docker.yaml. Helm charts ≠ JVM image. Scale pods. [07](07-docker-k8s-cicd-aws.md).

**13. AWS** — S3 + CloudWatch. Kinesis/Lambda/Spring Cloud AWS only if shipped. [07](07-docker-k8s-cicd-aws.md).

**14–15. Angular (Discover merchant boarding)** — Shravan rejected **ngModel** as state (that is **data binding**). Say services + **RxJS**, pagination, `async` pipe, **OnPush**. Not “EMVG.” You were ~4 years off Angular and guessed **v19** — do not claim 19 in prod; Discover was **Angular 8**. [03](03-frontend.md).

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

## Recording 65 (2026-09-15) — TurboScribe (first ~30 min) + rest of panel

Panel: recruiter **Anjali**, interviewer **Amar**. ~40 min total; free TurboScribe cut at `useEffect` deps. ASR also wrote **Fitbook**, **SADA**, **RBCD**, **DTL**, **LUR**.

**Intro** — 90s script in [01](01-intro-and-accuracy.md). Say **Vaibhav Patil**, **full-stack**, **QuickBooks**, **sales tax** exemption. Stop after Cron + Saga; wait.

**Years (critical)** — React **four**, Angular **three**. Backend/Java **twelve**. TurboScribe has “on the back-end I have **2** years.” If you said two, that contradicts the intro. Speak **twelve** slowly. Do not invent a 2-year backend career.

**Serialization** — data over the wire / `ObjectOutputStream`; `transient` for Java-serializable fields. REST: `@JsonIgnore`. A→B→C: **yes**, C is serializable. Block C: `private void writeObject` / `readObject` throw **`NotSerializableException`**. You stalled (“not sure, don’t want to give false information”) until Amar said throw. Memorize the exception name. [04](04-backend-java-spring.md).

**Bean scopes** — singleton (default), prototype, request, session. This part was clean.

**Redis** — session store **or** cache-aside with **TTL**. Interviewer wanted **TTL + LRU**. Say those words; not DTL/LUR. Do not call Redis Hibernate L2 unless wired. Do not mention `useReducer` in the backend caching answer. [05](05-distributed-microservices.md).

**Patterns** — **Saga** (not SADA), API gateway, circuit breaker, retry, compensate. Retry and fallback are not the same as the breaker. [05](05-distributed-microservices.md).

**AWS honesty** — S3 for **tax certificates**, CloudWatch. “Not a cloud architect.” Good — keep.

**CI/CD** — merge to main → **Jenkins** (exploring GitHub Actions) → test → **Dockerfile** (not Docker YAML) → image → registry → cluster. Secrets: Vault/K8s Secret, not in the image. [07](07-docker-k8s-cicd-aws.md).

**Validate image** — unit/integration tests in Jenkins, then image build, then optional scan (Trivy/Aqua if the org has it). **Argo CD is not the image validator.** You pointed at an “internal portal / Argo” — Amar was asking CI.

**Argo CD** — say **Argo CD** (not RBCD). GitOps: Git is source of truth. You use the **dashboard** for sync/health/rollback/replicas; you did not write the Application manifest. That is an honest senior answer if you add GitOps in one sentence.

**Never say** “I don’t remember Kubernetes” / “we only use the dashboard.” Name four objects: **Deployment** (pods, image, probes), **Service** (stable DNS — Amar asked this and you missed it), **Ingress** (HTTP host/path → Service, TLS), **CronJob** (exemption). Ingress is not a load balancer and not “an engine.” LB is often cloud/NLB in front or `Service type: LoadBalancer`. Rate limit is usually gateway/mesh, not the Ingress file you wrote.

**Local** — `spring-boot:run`, Docker Compose, Swagger. Good.

**Why Docker** — same artifact, same JDK/libs, no “works in pre-prod.” Good.

**React delivery** — plugins/widgets, app fabric, pin version, local override, class → function, TS, React 16→18, drop deps, GraphQL for fat REST. Good. [03](03-frontend.md).

**`useReducer`** — Amar asked purpose. Do **not** say it picks which component after an API. Dispatch + reducer for related UI state. Data fetching stays Apollo/RTK Query.

**Redux Toolkit (you blanked)** — even if the plugin is Context: “On SUBS-UI we used RTK. You `configureStore`, `createSlice` for reducers, wrap with `Provider`. Async: `createAsyncThunk` or **RTK Query** `createApi`.” Do not say you never implemented it if the resume lists RTK Query.

**`useEffect`** — after paint, **not** page reload. Deps array: values that must re-run the effect; `[]` once on mount. Transcript ended here.

**Second half (earlier notes, still drill)** — `useMemo` vs `React.memo`; Vault vs user OAuth; JWT filter **401 entry point**, not `@ExceptionHandler`. [03](03-frontend.md), [04](04-backend-java-spring.md), [07](07-docker-k8s-cicd-aws.md).

---

## HLD PDF §20 — keep using your existing guide

Orchestration vs choreography, create-then-DB-fail, duplicate customer, retryable errors, reverse compensate, context contents, Cron overlap, Splunk Saga, introducing Kafka later, compensation vs rollback, Redis vs DB, CB/fallback, UI hide vs API authZ.

---

## If they ask about the personal banking capstone

“Design exercise: Spring Cloud Gateway, JWT, Eureka, Resilience4j, targets like p95 200 ms. It is not production. My production depth is Intuit QuickBooks and Discover boarding.”
