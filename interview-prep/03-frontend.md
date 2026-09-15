# Frontend: React plugins, GraphQL, performance, Angular

You froze in recording 63 on a simple table: filter, sort, `.map()` to JSX, then add/remove. Syntax must be automatic. Do **not** mention Angular pipes or `useMemo` as a substitute for `filter`.

## How to describe the Intuit UI (60s)

We use **micro-frontends as plugins/widgets**. App fabric / portal composes independently versioned plugins. I implement a feature in a plugin, push to Git, Jenkins builds a **plugin version**, we pin that version on E2E/pre-prod, then production. Other teams own other plugins so we are not blocked on a monolith release. We still run Jest + React Testing Library, Cypress, and prod sanity. New behavior goes behind **IXP feature flags** with percentage rollout.

## State management — honest matrix

| App | State | Data loading |
| --- | --- | --- |
| QuickBooks plugin (what you described live) | React Context + `useReducer` | Apollo Client (GraphQL), Axios for some REST |
| SUBS-UI subscription work | Redux Toolkit + RTK Query | RTK Query cache |
| Discover merchant boarding | Angular 8 services + component state (NgRx only if you actually used it — if not, say services/RxJS) | HTTP client |

**Hooks (correct, short):**

- `useState` — local UI state (selected row, form fields).
- `useReducer` — related state updates (cart/board add/remove).
- `useContext` — avoid prop drilling across plugin tree.
- `useMemo` — **expensive derived values**, not “stop reload.”
- `useCallback` — stable function identity for memoized children, **not** “parent to nth child communication.”
- `useEffect` — sync with external systems (subscription, document title). Do **not** fetch in a chain of effects if Apollo/RTK Query already owns the query. You told them some devs overused `useEffect`; the fix is query libraries or a single effect with a proper dependency array — not replacing fetch with `useMemo`.

## Performance talking points (ASTON Q15 + recording)

- **Pagination** from the API (`limit`/`offset` or cursor) for thousands of rows. Do not render 10,000 DOM nodes.
- Virtualization (react-window) if they push “one page, huge list.”
- GraphQL: request only fields the view needs.
- CDN for static assets; drop unused dependencies; smaller bundles.
- `React.memo` + stable callbacks for heavy lists.
- Chrome Performance panel: you already mentioned measuring API latency from the UI.
- Async UX: do not block the whole page; notify by email when a long job (exemption) finishes.

## Micro-frontend interview extras

- Independent deploy vs shared design system / duplication of auth headers.
- Version pinning in portal = rollback by pinning the previous plugin version.
- Contract: GraphQL schema or BFF must stay compatible.

## Angular 8 (Discover) if they insist

Class components/services, `*ngIf`/`*ngFor`, `async` pipe for observables, change detection. State: lift to a service with `BehaviorSubject` unless you truly used NgRx. Performance: `OnPush`, trackBy, pagination. Do not mix Angular vocabulary into a React coding round.

## Timed drill (also in file 10)

Drivers age ≥ 18, sort by age ascending, vehicles by premium ascending, buttons add/remove from a board. Gold pattern:

```jsx
const adults = drivers
  .filter((d) => d.age >= 18)
  .sort((a, b) => a.age - b.age);

return adults.map((d) => (
  <div key={d.id}>
    <span>{d.name} {d.age}</span>
    <button onClick={() => addToBoard(d)}>Add</button>
  </div>
));
```

Board state:

```jsx
const [board, setBoard] = useState([]);
const addToBoard = (item) =>
  setBoard((prev) => prev.some((x) => x.id === item.id) ? prev : [...prev, item]);
const removeFromBoard = (id) =>
  setBoard((prev) => prev.filter((x) => x.id !== id));
```

If you go blank: say the steps out loud — “filter, sort, map to elements, key, useState for board” — then type. Never say “I don’t remember JSX so I’ll use pipes.”

## Testing answer (end of recording 63)

Jest + React Testing Library for units; Cypress for integration; Selenium where the org still has it; weekly regression on pre-prod; feature flags + Splunk after prod. That answer was already good — keep it, just make the live coding match it.
