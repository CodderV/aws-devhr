# Data: SQL, ACID, Java Streams

Live SQL and Streams showed up at ASTON, LTI, and recording 63. Memorize these until you can write them on a whiteboard.

## ACID with a fund-transfer use case (ASTON Q21)

Debit A and credit B in **one** database transaction.

- **Atomicity** — both accounts update or neither. No lost money.
- **Consistency** — constraints: non-negative balance, `debit + credit = 0` in the ledger pair.
- **Isolation** — concurrent transfers do not overdraw; use row locks or `SELECT FOR UPDATE` on account rows.
- **Durability** — after commit, a crash does not lose the transfer (WAL/commit). Multi-AZ / another region is **high availability**, a way to keep durability after a datacenter loss — not the definition of D.

**Saga is not ACID across TES + your DB.** Only each local transaction is ACID.

## Stored procedure vs function (ASTON Q18)

| | Stored procedure | Function |
| --- | --- | --- |
| Return | May return nothing, multiple result sets, out params | Returns a value (and in MySQL cannot freely do the same side effects in all contexts) |
| Use in SQL | `CALL proc()` | `SELECT fn(x)` in a query |
| Typical | Multi-statement business workflow | Computation, reuse in SELECT |
| Exceptions (MySQL) | `DECLARE EXIT HANDLER FOR SQLEXCEPTION` then rollback | Functions are more limited; prefer procs for transactional workflows |

Oracle: functions can be used in SQL if they obey purity rules; procs for DML batches.

**Recording 67:** you answered **Spring** `@ControllerAdvice` instead of SQL. If they ask MySQL exceptions, say `DECLARE EXIT HANDLER FOR SQLEXCEPTION` then `ROLLBACK` in the proc. Spring advice is a **different** question.

## Second-highest salary (recording 63)

They corrected offset: `LIMIT 1 OFFSET 1` after `ORDER BY salary DESC` (offset **1**, not 2). Duplicates: `DENSE_RANK()` is the right interview answer.

```sql
-- ties get the same rank
SELECT salary FROM (
  SELECT salary, DENSE_RANK() OVER (ORDER BY salary DESC) AS r
  FROM employee
) t WHERE r = 2;

-- max + subquery
SELECT MAX(salary) FROM employee
WHERE salary < (SELECT MAX(salary) FROM employee);

-- MySQL limit (wrong if duplicate salaries)
SELECT DISTINCT salary FROM employee
ORDER BY salary DESC
LIMIT 1 OFFSET 1;
```

## Third-highest salary (recording 64)

Same pattern as second-highest; filter `r = 3`. Ties: `DENSE_RANK` (two people with the same top salary both rank 1, next is 2). `ROW_NUMBER` splits ties. `LIMIT 1 OFFSET 2` is wrong when duplicates exist.

```sql
SELECT salary FROM (
  SELECT salary, DENSE_RANK() OVER (ORDER BY salary DESC) AS r
  FROM employee
) t WHERE r = 3;
```

## Top three customers by order amount (ASTON Q20)

```sql
SELECT customer_id, SUM(amount) AS total
FROM orders
GROUP BY customer_id
ORDER BY total DESC
LIMIT 3;
```

## DB tuning talking points (Virtusa — stay honest)

Indexes on filter/join columns (`customer_id`, `status`, `created_at`). Avoid `SELECT *`. Explain plans (`EXPLAIN`). Pagination. Connection pool sizing. You have used Oracle, MySQL, Informix — speak to **indexing and explain plans**, not “I rewrote the optimizer.”

## Java Streams — first non-repeating character (recording 63)

They hinted **LinkedHashMap** so insertion order is kept.

```java
static Character firstNonRepeating(String s) {
    Map<Character, Long> counts = s.chars()
        .mapToObj(c -> (char) c)
        .collect(Collectors.groupingBy(c -> c, LinkedHashMap::new, Collectors.counting()));
    return counts.entrySet().stream()
        .filter(e -> e.getValue() == 1)
        .map(Map.Entry::getKey)
        .findFirst()
        .orElse(null);
}
```

If you forget `groupingBy` downstream: loop once into `LinkedHashMap<Character,Integer>`, then stream entries.

## Least-repeated character (recording 64)

They asked for the character with the **lowest frequency** (not “first unique”). Ties: pick insertion order or the first min — **state the tie rule** before coding.

```java
static Character leastRepeated(String s) {
    Map<Character, Long> counts = s.chars()
        .mapToObj(c -> (char) c)
        .collect(Collectors.groupingBy(c -> c, LinkedHashMap::new, Collectors.counting()));
    return counts.entrySet().stream()
        .min(Map.Entry.comparingByValue())
        .map(Map.Entry::getKey)
        .orElse(null);
}
```

If they wanted first **non-repeating**, use the unique-char snippet above (`count == 1`). Confirm the problem in one sentence.

## Customer transactions (ASTON Q17)

Ignore failed; group by customer; sum successful; keep customers whose total > 1.

```java
record Tx(String customerId, LocalDate date, BigDecimal amount, String status) {}

Map<String, BigDecimal> totalsOverOne(List<Tx> txs) {
    return txs.stream()
        .filter(t -> !"FAILED".equalsIgnoreCase(t.status()))
        .collect(Collectors.groupingBy(
            Tx::customerId,
            Collectors.reducing(BigDecimal.ZERO, Tx::amount, BigDecimal::add)))
        .entrySet().stream()
        .filter(e -> e.getValue().compareTo(BigDecimal.ONE) > 0)
        .collect(Collectors.toMap(Map.Entry::getKey, Map.Entry::getValue));
}
```

## Stream `Predicate` (Virtusa)

```java
Predicate<Tx> success = t -> "SUCCESS".equalsIgnoreCase(t.status());
txs.stream().filter(success.and(t -> t.amount().compareTo(BigDecimal.ONE) > 0));
```

## Practice

Write all four SQL/Stream snippets from a blank file daily until day 7.
