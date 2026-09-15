# Backend: Java 21 and Spring Boot

Fix inaccurate answers from recordings 63–65, then keep banking/Spring patterns Virtusa and Fulcrum asked.

## `@SpringBootApplication` (they asked what it contains)

It is a composed annotation:

- `@SpringBootConfiguration` (`@Configuration`) — this class can define beans.
- `@EnableAutoConfiguration` — **if** a starter is on the classpath (Maven/Gradle already pulled it), auto-configure a `DataSource`, dispatcher, etc. It does **not** download JARs.
- `@ComponentScan` — scan the package of this class and below for `@Component` / `@Service` / `@Controller`.

Exclude auto-config: `@SpringBootApplication(exclude = {RedisAutoConfiguration.class})` or `excludeName`. Skip scan: `scanBasePackages` or `@ComponentScan(excludeFilters=...)`.

## Dependency injection and testing (Fulcrum)

Spring injects collaborators through constructors (preferred). In tests you pass **fakes**:

```java
@Test
void totalsIgnoreFailed() {
  var service = new ReportService(new StubLedger()); // no Spring needed
  assertEquals(10, service.total("C1"));
}
```

`@WebMvcTest` slices the web layer and `@MockBean` the service. DI makes the controller not call `new TesClient()` internally, so you can assert retries without hitting TES.

## Bean scopes (recording 65)

| Scope | Meaning |
| --- | --- |
| **singleton** (default) | One instance per Spring container |
| **prototype** | New instance every injection |
| **request** | One per HTTP request (web) |
| **session** | One per HTTP session (web) |
| application / websocket | Servlet context / WS session |

Stateless services stay **singleton**. Prototype for a mutable helper that must not be shared. Do not make a singleton hold request-specific fields.

## `@Component` vs `@Bean` (recording 64)

Both produce Spring beans. You do **not** need both on the same class.

- **`@Component`** (and `@Service`, `@Repository`, `@Controller`) — Spring **scans** *your* class and constructs it. Prefer constructor injection.
- **`@Bean`** — a method on a `@Configuration` class that **returns** an object. Use it for types you cannot annotate (third-party clients, `RestClient`, `ObjectMapper` tweaks) or when construction needs custom code.

## Global exception handling

`@RestControllerAdvice` + `@ExceptionHandler`. Map domain exceptions to 404/409/422; unexpected to 500 with correlation id, never a stack trace to the client.

## JWT / filter exceptions (recording 65 — do not send these to `@ExceptionHandler`)

The JWT filter runs in the **servlet filter chain**, before `DispatcherServlet`. `@RestControllerAdvice` **does not** see those exceptions unless you forward them on purpose.

Say this:

1. Invalid or missing token → **`AuthenticationEntryPoint`** → **401**.
2. Authenticated but forbidden → **`AccessDeniedHandler`** → **403**.
3. Expired token is still an authentication failure (401), not a controller exception.
4. Controller/service exceptions (business 404/409) stay on `@RestControllerAdvice`.

If they insist on advice: you can call `HandlerExceptionResolver.resolveException(...)` from the filter, but the standard Spring Security answer is entry point + denied handler. `@PreAuthorize` failures are method security, also translated by `ExceptionTranslationFilter`, not by a custom exception class thrown from the filter.

## Java serialization vs JSON (recording 65)

Interviewers often mean **Java `Serializable`**, while production APIs use **Jackson JSON**.

**Java serialization:** `implements Serializable` + `ObjectOutputStream`. `transient` skips a field (password). Add `serialVersionUID`.

**Inheritance:** if class A implements `Serializable`, B and C that extend it **are serializable** even if they do not declare it.

**Stop C from serializing:** you cannot “un-implement” `Serializable`. In C, implement `private void writeObject(ObjectOutputStream out)` and `readObject` and **throw `NotSerializableException`**. `transient` on fields is not enough to block the whole type. Do not say “override the serializable method.”

**REST/JSON (what you actually ship):** `@JsonIgnore` / `@JsonIgnoreProperties` on password hashes; never return secrets in DTOs. That is **not** the `transient` keyword (Jackson ignores `transient` only if configured).

## Custom immutable class (recording 64)

“Cannot change after construction.” Recipe:

1. `final` class (no subclass mutating state).
2. `private final` fields.
3. **Public** constructor or static factory that assigns fields (private constructor only if you also expose `of(...)`).
4. **No setters.**
5. If a field is a collection, store an **unmodifiable defensive copy**.
6. Getters return unmodifiable views or copies of mutable internals.

Records are immutable **for the references they hold**. A `record Box(List<String> items)` is not deep-immutable if callers mutate the list — copy in the compact constructor.

## `@Transactional` (you were close — tighten it)

- Spring wraps a **public** method on a **Spring bean** with a proxy.
- Self-invocation (`this.save()` inside the same class) **skips** the proxy — no transaction.
- Default rollback: **unchecked** (`RuntimeException`) and `Error`. Checked exceptions need `rollbackFor = Exception.class`.
- Isolation/propagation if they go deep: `REQUIRED` is default; `REQUIRES_NEW` for the Saga’s “update retry count even if the outer work failed” — only if you actually use it.

ACID is the **database transaction**, not the whole Saga. See [06-data-sql-acid-streams.md](06-data-sql-acid-streams.md) and HLD §10.

## Java 21 features they quizzed

**Records** — immutable named tuples. Compiler generates constructor, accessors (`amount()`, not `getAmount()` unless you customize), `equals`/`hashCode`/`toString`. **No setters.** Recording 63 wrong: “Java creates getters and setters.” Recording 64 right: records drop boilerplate and are not deep-immutable if a component is a mutable `ArrayList`. Use records for DTOs and event payloads, not for JPA entities (mutable + no-arg constructor needs).

**Sealed classes** — `sealed interface Payment permits CardPayment, AchPayment`. Exhaustive `switch`. Problem solved: uncontrolled subclasses of a domain type.

**Functional interface** — exactly one abstract method; `@FunctionalInterface` makes the compiler enforce it. Default/static methods allowed. Lambdas target that SAM type.

**Default methods** — evolve interfaces (e.g. `List.sort`) without breaking every implementor. Java 8 Streams APIs needed this.

**Virtual threads (recording 67)** — Java 21 can run many blocking tasks cheaply (`Executors.newVirtualThreadPerTaskExecutor()`). Spring Boot 3.2+ can opt in. Say “we evaluated / not enabled on my services” unless you actually flipped the property. Do not pair this with GraalVM as the reason Boot 3 is faster.

## HashMap internals (fix this)

1. `hashCode` of key, mixed, then index into table (`(n - 1) & hash`).
2. If bucket empty, put node. If collision, compare `hash` then `equals`.
3. Same key (`equals` true) → **replace value**.
4. Different keys, same bucket → linked list.
5. Java 8+: if list length ≥ **8** and table capacity ≥ 64, convert bucket to **red-black tree** (treeify). Shrink back if it gets small.
6. Resize when load factor (0.75) exceeded.

**Thread safety:** `HashMap` is not safe. Structural modification during iteration → `ConcurrentModificationException` (fail-fast iterator, not a lock). Options: `Collections.synchronizedMap` (coarse), or **`ConcurrentHashMap`**.

**ConcurrentHashMap (modern Java):** not “16 segments” (that was Java 7). It uses bins + CAS; reads are weakly consistent; iterators **do not** throw CME. Do not say “it is synchronized by default” as if every method locks the whole map.

## REST in a microservice

Controller thin; service owns transactions; repository persistence. Idempotent PUT/DELETE. Validation with `@Valid`. Pagination for lists. OpenAPI if they ask how clients discover APIs.

**Sync communication:** OpenFeign / `RestClient` with connect/read timeouts.

## Criteria API and Predicates (Virtusa L2)

JPA Criteria: type-safe queries when filters are dynamic (maker-checker queues: status, date, amount range).

```java
var cb = em.getCriteriaBuilder();
var q = cb.createQuery(Payment.class);
var root = q.from(Payment.class);
List<Predicate> predicates = new ArrayList<>();
if (status != null) predicates.add(cb.equal(root.get("status"), status));
q.where(predicates.toArray(Predicate[]::new));
```

Java Stream `Predicate<T>` is a different type — functional filter `t -> t.status() == SUCCESS`. If they mix both, separate “JPA Predicate” vs “java.util.function.Predicate.”

## Spring Batch (Fulcrum — large data)

Chunk-oriented: `ItemReader` → `Processor` → `Writer` of size N, transaction per chunk. Skip policy for poison rows; retry for transient DB; listeners for metrics. Tax exemption is **closer to a scheduled pipeline than Spring Batch** — only claim Batch if you used it. For “how would you,” describe chunking + skip + job repository so a restart does not redo committed chunks.

## Maker-checker (Virtusa, payments)

Two-person integrity: maker creates a payment/beneficiary; record is `PENDING`; checker approves; only then settlement. Same user cannot both. Store audit (who, when). Maps to banking controls you should mention with Discover/Intuit finance, even if QuickBooks exemption is not literally maker-checker.

## Payment domain vocabulary (do not bluff numbers)

Idempotency key, clearing vs settlement, reversal vs refund, insufficient funds vs technical failure (retry only technical). Tax exemption is **certificate + downstream customer create**, not a card authorization — keep the domains distinct.
