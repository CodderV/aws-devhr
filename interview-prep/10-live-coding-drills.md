# Live-coding drills

Rules: 15 minutes each, **no Cursor**. Say the algorithm first. If syntax slips, narrate and keep typing.

---

## Drill 1 — First non-repeating character (recording 63)

**Problem:** Given a string, return the first character that appears exactly once. If none, return `null`.

**Say aloud:** Count in insertion order; then first entry with count 1. `LinkedHashMap`.

**Gold:**

```java
import java.util.LinkedHashMap;
import java.util.Map;
import java.util.stream.Collectors;

class FirstUnique {
    static Character firstNonRepeating(String s) {
        if (s == null || s.isEmpty()) return null;
        Map<Character, Long> counts = s.chars()
                .mapToObj(c -> (char) c)
                .collect(Collectors.groupingBy(c -> c, LinkedHashMap::new, Collectors.counting()));
        return counts.entrySet().stream()
                .filter(e -> e.getValue() == 1L)
                .map(Map.Entry::getKey)
                .findFirst()
                .orElse(null);
    }

    public static void main(String[] args) {
        System.out.println(firstNonRepeating("swiss")); // w
        System.out.println(firstNonRepeating("aabb"));  // null
    }
}
```

**If `groupingBy` blanks:**

```java
Map<Character, Integer> counts = new LinkedHashMap<>();
for (char c : s.toCharArray()) {
    counts.merge(c, 1, Integer::sum);
}
for (var e : counts.entrySet()) {
    if (e.getValue() == 1) return e.getKey();
}
return null;
```

---

## Drill 2 — Successful transaction totals (ASTON Q17)

**Problem:** `customerId`, `date`, `amount`, `status`. Ignore failed. Group by customer. Sum. Keep totals **greater than 1**.

**Gold:**

```java
import java.math.BigDecimal;
import java.time.LocalDate;
import java.util.List;
import java.util.Map;
import java.util.stream.Collectors;

record Tx(String customerId, LocalDate date, BigDecimal amount, String status) {}

class TxReport {
    static Map<String, BigDecimal> customersOverOne(List<Tx> txs) {
        return txs.stream()
                .filter(t -> !"FAILED".equalsIgnoreCase(t.status()))
                .collect(Collectors.groupingBy(
                        Tx::customerId,
                        Collectors.reducing(BigDecimal.ZERO, Tx::amount, BigDecimal::add)))
                .entrySet().stream()
                .filter(e -> e.getValue().compareTo(BigDecimal.ONE) > 0)
                .collect(Collectors.toMap(Map.Entry::getKey, Map.Entry::getValue));
    }
}
```

---

## Drill 3 — Drivers and vehicles board (recording 63)

**Problem:** JSON lists of drivers and vehicles. Show drivers **age ≥ 18** sorted by age ascending. Vehicles sorted by **premium** ascending. Button to **add** or **remove** from a board.

**Say aloud:** filter, sort with `(a,b) => a.age - b.age`, `map` to JSX with `key`, `useState` for board, copy array when updating.

**Gold (single file, no router):**

```jsx
import { useMemo, useState } from "react";

const drivers = [
  { id: "d1", name: "Ann", age: 17 },
  { id: "d2", name: "Bo", age: 34 },
  { id: "d3", name: "Cy", age: 21 },
];

const vehicles = [
  { id: "v1", name: "Sedan", premium: 900 },
  { id: "v2", name: "SUV", premium: 400 },
];

export default function App() {
  const [board, setBoard] = useState([]);

  const adultDrivers = useMemo(
    () => drivers.filter((d) => d.age >= 18).sort((a, b) => a.age - b.age),
    []
  );
  const sortedVehicles = useMemo(
    () => [...vehicles].sort((a, b) => a.premium - b.premium),
    []
  );

  const add = (item) =>
    setBoard((prev) => (prev.some((x) => x.id === item.id) ? prev : [...prev, item]));
  const remove = (id) => setBoard((prev) => prev.filter((x) => x.id !== id));

  return (
    <div>
      <h2>Drivers</h2>
      {adultDrivers.map((d) => (
        <div key={d.id}>
          <span>{d.name} {d.age}</span>
          <button onClick={() => add(d)}>Add</button>
        </div>
      ))}
      <h2>Vehicles</h2>
      {sortedVehicles.map((v) => (
        <div key={v.id}>
          <span>{v.name} {v.premium}</span>
          <button onClick={() => add(v)}>Add</button>
        </div>
      ))}
      <h2>Board</h2>
      {board.map((item) => (
        <div key={item.id}>
          <span>{item.name}</span>
          <button onClick={() => remove(item.id)}>Remove</button>
        </div>
      ))}
    </div>
  );
}
```

Here `useMemo` is optional (tiny arrays). **Required:** `filter`, `sort`, `map`, `key`, functional `setState`.

**If stuck script:** “I will put data in constants. Filter age. Sort a minus b on the field. Map to divs. Board is useState array.”

---

## Drill 4 — SQL second-highest salary

```sql
SELECT salary
FROM (
  SELECT salary, DENSE_RANK() OVER (ORDER BY salary DESC) AS rnk
  FROM employee
) t
WHERE rnk = 2;
```

Alternate: `SELECT DISTINCT salary FROM employee ORDER BY salary DESC LIMIT 1 OFFSET 1;`

---

## Drill 5 — Top 3 customers by order amount

```sql
SELECT customer_id, SUM(amount) AS total
FROM orders
GROUP BY customer_id
ORDER BY total DESC
LIMIT 3;
```

---

## Drill 6 — Spring Boot tiny CRUD (LTI style)

They give a skeleton. You fill:

- `POST /games` body `{ name, score }` → 201
- `GET /games/{id}` → 200 or 404
- `@Valid`, `@RestControllerAdvice` for `NotFoundException` → 404
- Tests with `@WebMvcTest` if time

Keep services transactional; repository `JpaRepository`.

---

## Drill 7 — Exception handling (LTI Java)

Do not catch `Exception` and ignore. Catch `IOException`, wrap `new ReportException("read failed", e)`, or declare throws. `@Transactional(rollbackFor = Exception.class)` if the method throws checked.

---

## Whiteboard HashMap 60-second sketch

Array of buckets → hash & mask → equals → collide list → tree if long. Resize at 0.75. Same key replaces. ConcurrentHashMap for threads.

---

## Daily scorecard

| Drill | Time | Done without hints |
| --- | --- | --- |
| 1 Streams unique char | /10 min | |
| 2 Tx grouping | /10 min | |
| 3 React board | /15 min | |
| 4–5 SQL | /5 min | |
| Recite CB states | /1 min | |
| Recite Path A + Path B | /3 min | |
