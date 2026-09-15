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

**Hooks (correct, short) — recording 65 mixed these up:**

- `useState` — local UI state (selected row, form fields).
- `useReducer` — related state updates (board add/remove, multi-field form). **Not a cache.** You dispatch actions; React holds the next state in memory for that plugin tree.
- `useContext` — avoid prop drilling across plugin tree.
- `useMemo` — **cache an expensive computed value** (`const sorted = useMemo(() => [...rows].sort(...), [rows])`). It does **not** memorize a function, and it does **not** stop page reloads.
- `useCallback` — cache a **function identity** so memoized children do not see a new prop every render.
- `React.memo(Component)` — skip **re-rendering a component** when props are shallow-equal. Different from `useMemo`. Learn one sentence: “memo wraps a component; useMemo wraps a value; useCallback wraps a function.”
- `useEffect` — run **side effects after paint** (subscribe, set document title). It does **not** reload the page. Empty deps `[]` → run once after mount. Missing deps → stale closures. Do **not** fetch in a chain of effects if Apollo/RTK Query already owns the query.

**If they ask “purpose of useReducer” (rec 65):** “When the next state depends on the previous state through a few action types — add/remove/reset — I use useReducer instead of several useState setters. API data still lives in Apollo/RTK Query or a fetch hook; the reducer does not replace the server cache.”

**Redux Toolkit setup (rec 65 blank):** Amar asked which function/components you write to configure RTK. Minimum:

```js
import { configureStore, createSlice } from "@reduxjs/toolkit";
import { Provider } from "react-redux";

const boardSlice = createSlice({
  name: "board",
  initialState: { items: [] },
  reducers: {
    add: (state, action) => { state.items.push(action.payload); },
    remove: (state, action) => {
      state.items = state.items.filter((x) => x.id !== action.payload);
    },
  },
});

const store = configureStore({ reducer: { board: boardSlice.reducer } });
// <Provider store={store}><App /></Provider>
```

Async: `createAsyncThunk` or RTK Query `createApi`. Resume line: SUBS-UI used RTK Query; QuickBooks plugin uses Context + `useReducer`.

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

**Recording 67 (Shravan):** Discover **merchant boarding**, not “Discord.” **`ngModel` is two-way binding**, not the state architecture — he cut you off. State: injectable **service** + RxJS. Perf: pagination, `async` pipe, **OnPush**, `trackBy`. Angular is often described as **MVVM**, not “EMVG.” You said you have not used Angular in ~4 years and mentioned **v19** — say you would ramp; production memory is **Angular 8**. Do not mix in React “prompt.”

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
