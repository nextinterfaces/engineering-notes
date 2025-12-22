# Modern React + TypeScript

---

## 1. Modern React + TypeScript Essentials

### Function Components + Hooks
Prefer function components and hooks over class components.

```ts
type UserCardProps = {
  user: { id: string; name: string };
  onSelect?: (id: string) => void;
};

export function UserCard({ user, onSelect }: UserCardProps) {
  return (
    <button onClick={() => onSelect?.(user.id)}>
      {user.name}
    </button>
  );
}
```

### TypeScript Best Practices
- Prefer **type inference** for local state
- Avoid `any`; use unions and discriminated unions
- Use `unknown` and narrow when necessary

```ts
const [count, setCount] = useState(0); // inferred as number
```

---

## 2. State Management Strategy

### Local State (`useState`, `useReducer`)
Use for component-scoped state.

```ts
type State = { query: string; page: number };
type Action =
  | { type: "setQuery"; query: string }
  | { type: "nextPage" }
  | { type: "reset" };

function reducer(state: State, action: Action): State {
  switch (action.type) {
    case "setQuery":
      return { ...state, query: action.query, page: 1 };
    case "nextPage":
      return { ...state, page: state.page + 1 };
    case "reset":
      return { query: "", page: 1 };
  }
}
```

### Server State (React Query)
- Caching, retries, background refresh
- **Do not store server state in global stores**

> Server state is not UI state.

### Global Client State
- Context: low-frequency, stable data (theme, auth)
- Zustand/Jotai/Redux Toolkit: frequently changing shared state

Rule of thumb:
- If it changes often and many components subscribe → store
- If it’s mostly static → context

---

## 3. Performance Considerations

### Measure First
- React DevTools Profiler
- Identify unnecessary re-renders
- Use JS Performance API
- Use browser recording

### Prevent Re-renders
- `React.memo` for expensive components, only re-render when props change 
- `useCallback` only when passing callbacks to memoized children
- `useMemo` for expensive derived values

```ts
const filtered = useMemo(
  () => users.filter(u => u.name.includes(query)),
  [users, query]
);
```

### Lists virtualization
- Virtualize long lists (react-window / react-virtual), For long lists, render only what's visible
- Use stable keys (never array index)

### Concurrent Rendering
Use `startTransition` for non-urgent updates:

```ts
startTransition(() => {
  setResults(expensiveSearch(query));
});
```
### Code Splitting - Load Less Code

```react
import { lazy, Suspense } from 'react';

// ❌ Loads everything upfront
import Dashboard from './Dashboard';

// ✅ Loads only when needed
const Dashboard = lazy(() => import('./Dashboard'));
```
---

## 4. Code Quality Practices

- Keep **components pure**
- Extract side effects into **hooks/services**
- Use React **Hook Form** for forms
- Handle loading & error states explicitly
- Prioritize accessibility (keyboard, semantics)


### What typically makes React apps slow?

- Blocking the Main ThreadProblem: Heavy synchronous work freezes UI
- Unnecessary re-renders
- Large lists (no virtualization)
- Expensive calculations
- Large bundle size
- too many nested DOM
- Heavy images
- nested API calls
