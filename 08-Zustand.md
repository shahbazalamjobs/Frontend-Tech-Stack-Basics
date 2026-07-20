If you're preparing for **fresher to intermediate React interviews**, you don't need to know every Zustand feature. Focus on these concepts:

1. Why Zustand?
2. Create a Store
3. Read State
4. Update State
5. Global State vs Context API

---

# What is Zustand?

Zustand is a **small and simple state management library** for React.

It lets multiple components share the same state **without prop drilling** and **without wrapping your app in a Provider**.

Unlike Context API, Zustand only re-renders components that use the changed state.

Install:

```bash
npm install zustand
```

---

# Why do we need Zustand?

Suppose several components need the logged-in user.

Without Zustand:

```
App
 ├── Navbar
 │     └── Profile
 └── Dashboard
```

You pass props through every component.

```jsx
<App user={user} />

↓

<Navbar user={user} />

↓

<Profile user={user} />
```

This is called **prop drilling**.

With Zustand:

```
Store
   │
 ├── Navbar
 ├── Profile
 └── Dashboard
```

Every component gets data directly from the store.

---

# Creating Your First Store

Create:

```
src/store/useCounterStore.js
```

```jsx
import { create } from "zustand";

const useCounterStore = create((set) => ({
  count: 0,

  increment: () =>
    set((state) => ({
      count: state.count + 1,
    })),

  decrement: () =>
    set((state) => ({
      count: state.count - 1,
    })),
}));

export default useCounterStore;
```

### Explanation

`create()` creates the store.

```jsx
create((set) => ({
```

`set()` updates the state.

Initial state:

```jsx
count: 0
```

Action:

```jsx
increment: () =>
  set((state) => ({
    count: state.count + 1,
  }))
```

---

# Using the Store

```jsx
import useCounterStore from "./store/useCounterStore";

function Counter() {
  const count = useCounterStore((state) => state.count);
  const increment = useCounterStore((state) => state.increment);

  return (
    <>
      <h1>{count}</h1>
      <button onClick={increment}>+</button>
    </>
  );
}
```

Output

```
0
[+]

↓

1
↓

2
↓

3
```

---

# Reading Multiple Values

Instead of:

```jsx
const count = useCounterStore((state) => state.count);
const increment = useCounterStore((state) => state.increment);
const decrement = useCounterStore((state) => state.decrement);
```

You can do:

```jsx
const { count, increment, decrement } = useCounterStore();
```

Although this works, **it subscribes to the entire store**, so the component re-renders whenever *any* state changes.

A better practice is to select only what you need:

```jsx
const count = useCounterStore((state) => state.count);
const increment = useCounterStore((state) => state.increment);
const decrement = useCounterStore((state) => state.decrement);
```

---

# Updating State

You can update state using `set()`.

```jsx
set({
  count: 10,
});
```

Or based on previous state:

```jsx
set((state) => ({
  count: state.count + 1,
}));
```

---

# Another Example (Theme)

```jsx
import { create } from "zustand";

const useThemeStore = create((set) => ({
  dark: false,

  toggleTheme: () =>
    set((state) => ({
      dark: !state.dark,
    })),
}));

export default useThemeStore;
```

Component:

```jsx
import useThemeStore from "./store/useThemeStore";

function ThemeButton() {
  const dark = useThemeStore((state) => state.dark);
  const toggleTheme = useThemeStore((state) => state.toggleTheme);

  return (
    <button onClick={toggleTheme}>
      {dark ? "Dark" : "Light"}
    </button>
  );
}
```

---

# Real Production Example (Auth)

Store

```jsx
import { create } from "zustand";

const useAuthStore = create((set) => ({
  user: null,

  login: (user) =>
    set({
      user,
    }),

  logout: () =>
    set({
      user: null,
    }),
}));

export default useAuthStore;
```

Login

```jsx
const login = useAuthStore((state) => state.login);

login({
  id: 1,
  name: "John",
});
```

Navbar

```jsx
const user = useAuthStore((state) => state.user);

return <h2>{user?.name}</h2>;
```

---

# Where Should Zustand Be Used?

Use Zustand for global state shared by many components.

Examples:

* Authentication (logged-in user)
* Theme (dark/light)
* Shopping cart
* Sidebar open/close
* Notifications
* Language selection
* Wishlist
* Filters used across pages
* User preferences

---

# When NOT to Use Zustand

Don't use it for:

* Input field values
* Small local component state
* Form state (use React Hook Form)
* Server data (use TanStack Query)

Example:

```jsx
const [name, setName] = useState("");
```

`useState` is the right choice here.

---

# Zustand vs Context API

| Feature         | Context API                  | Zustand              |
| --------------- | ---------------------------- | -------------------- |
| Prop Drilling   | Solves                       | Solves               |
| Provider Needed | ✅ Yes                        | ❌ No                 |
| Easy to Learn   | ✅                            | ✅                    |
| Performance     | Can re-render many consumers | Selective re-renders |
| Boilerplate     | More                         | Less                 |
| Best For        | Small apps                   | Medium to large apps |

---

# Folder Structure

```
src
│
├── store
│     ├── useAuthStore.js
│     ├── useThemeStore.js
│     └── useCartStore.js
│
├── pages
├── components
└── App.jsx
```

---

# Best Practices

* Create **one store per feature** (Auth, Theme, Cart) instead of one huge store.
* Select only the state you need:

```jsx
const user = useAuthStore((state) => state.user);
```

* Keep API fetching in **TanStack Query**, not Zustand.
* Use Zustand for **client/UI state**, not server state.
* Keep actions (e.g., `login`, `logout`, `toggleTheme`) inside the store.

---

# Context API vs Zustand vs TanStack Query

| Tool           | Use For                                      |
| -------------- | -------------------------------------------- |
| `useState`     | Local component state                        |
| Context API    | Small amount of global state                 |
| Zustand        | Complex client-side global state             |
| TanStack Query | Server state (API data, caching, refetching) |

---

# Interview Questions

### 1. What is Zustand?

A lightweight React state management library for managing global client-side state without prop drilling.

---

### 2. Why use Zustand instead of Context API?

* No Provider required.
* Less boilerplate.
* Better performance with selective subscriptions.
* Easier to organize large applications.

---

### 3. What is `create()`?

It creates a Zustand store.

```jsx
const useStore = create((set) => ({
  count: 0,
}));
```

---

### 4. What is `set()`?

It updates the store state.

```jsx
set((state) => ({
  count: state.count + 1,
}));
```

---

### 5. Can Zustand replace Redux?

Yes, for many applications. Redux is still useful for very large applications that need advanced tooling, middleware, or strict state management patterns.

---

### 6. Can Zustand replace TanStack Query?

No.

* **Zustand** → Client/UI state.
* **TanStack Query** → Server/API state.

They are often used together in production applications.
