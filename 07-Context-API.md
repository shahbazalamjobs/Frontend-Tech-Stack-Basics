If you're preparing for **fresher to intermediate React interviews**, you only need to know about **5 concepts** in Context API:

1. Why Context API?
2. Create Context
3. Provide Context
4. Consume Context
5. Update Context

---

# What is Context API?

Context API lets you **share data between components without passing props manually (prop drilling).**

### Without Context

```jsx
<App>
  <Navbar user={user} />
```

```jsx
function Navbar({ user }) {
  return <Profile user={user} />;
}
```

```jsx
function Profile({ user }) {
  return <h1>{user.name}</h1>;
}
```

The `user` prop is passed through every component.

This is called **Prop Drilling**.

---

# With Context

```
App
 │
 ├── Navbar
 │
 └── Profile
       │
       └── useContext(UserContext)
```

Any component can directly access the user.

---

# Step 1: Create Context

Create a file.

**UserContext.jsx**

```jsx
import { createContext } from "react";

export const UserContext = createContext();
```

`createContext()` creates a global context.

---

# Step 2: Provide Context

Wrap your app with the Provider.

```jsx
import { UserContext } from "./UserContext";

function App() {
  const user = {
    name: "Shahbaz",
    age: 22,
  };

  return (
    <UserContext.Provider value={user}>
      <Home />
    </UserContext.Provider>
  );
}

export default App;
```

The `value` prop contains the data you want to share.

---

# Step 3: Consume Context

```jsx
import { useContext } from "react";
import { UserContext } from "./UserContext";

function Home() {
  const user = useContext(UserContext);

  return <h1>{user.name}</h1>;
}

export default Home;
```

Output

```
Shahbaz
```

No props needed.

---

# Passing State

Usually, we share state instead of static data.

```jsx
import { createContext, useState } from "react";

export const UserContext = createContext();

function App() {
  const [user, setUser] = useState("Shahbaz");

  return (
    <UserContext.Provider value={{ user, setUser }}>
      <Home />
    </UserContext.Provider>
  );
}

export default App;
```

Notice we pass both `user` and `setUser`.

---

## Reading and Updating

```jsx
import { useContext } from "react";
import { UserContext } from "./UserContext";

function Home() {
  const { user, setUser } = useContext(UserContext);

  return (
    <>
      <h2>{user}</h2>

      <button onClick={() => setUser("Ali")}>
        Change Name
      </button>
    </>
  );
}

export default Home;
```

Output

```
Shahbaz

[Change Name]

↓

Ali
```

---

# Example: Dark Mode

## ThemeContext.jsx

```jsx
import { createContext } from "react";

export const ThemeContext = createContext();
```

---

## App.jsx

```jsx
import { useState } from "react";
import { ThemeContext } from "./ThemeContext";
import Home from "./Home";

function App() {
  const [theme, setTheme] = useState("light");

  return (
    <ThemeContext.Provider value={{ theme, setTheme }}>
      <Home />
    </ThemeContext.Provider>
  );
}

export default App;
```

---

## Home.jsx

```jsx
import { useContext } from "react";
import { ThemeContext } from "./ThemeContext";

function Home() {
  const { theme, setTheme } = useContext(ThemeContext);

  return (
    <div
      style={{
        background: theme === "light" ? "white" : "black",
        color: theme === "light" ? "black" : "white",
        padding: "20px",
      }}
    >
      <h1>{theme}</h1>

      <button
        onClick={() =>
          setTheme(theme === "light" ? "dark" : "light")
        }
      >
        Toggle Theme
      </button>
    </div>
  );
}

export default Home;
```

---

# When Should You Use Context API?

Good for:

* ✅ Logged-in user
* ✅ Dark/Light theme
* ✅ Language (English/Hindi)
* ✅ Authentication status
* ✅ Shopping cart (small apps)

Avoid for:

* ❌ Large, frequently changing global state (consider Zustand or Redux Toolkit)
* ❌ Data fetched from APIs (TanStack Query is a better choice)

---

This is actually how **Context API is used in real-world React applications**. Instead of calling `useContext()` everywhere, we usually wrap it inside a **custom hook**.

The pattern looks like this:

```
Context
   ↓
Provider
   ↓
Custom Hook (useAuth, useTheme, useCart)
   ↓
Components
```

This keeps your code cleaner and makes it easier to maintain.

---

# Example: Theme Context (Production Style)

## Folder Structure

```
src
│
├── context
│   └── ThemeContext.jsx
│
├── hooks
│   └── useTheme.js
│
├── components
│   ├── Navbar.jsx
│   ├── Home.jsx
│   └── Settings.jsx
│
└── App.jsx
```

---

# Step 1: Create Context

## context/ThemeContext.jsx

```jsx
import { createContext, useState } from "react";

export const ThemeContext = createContext();

export function ThemeProvider({ children }) {
  const [theme, setTheme] = useState("light");

  const toggleTheme = () => {
    setTheme((prev) => (prev === "light" ? "dark" : "light"));
  };

  return (
    <ThemeContext.Provider
      value={{
        theme,
        toggleTheme,
      }}
    >
      {children}
    </ThemeContext.Provider>
  );
}
```

Notice we export **ThemeProvider**, not just the context.

---

# Step 2: Create Custom Hook

## hooks/useTheme.js

```jsx
import { useContext } from "react";
import { ThemeContext } from "../context/ThemeContext";

export function useTheme() {
  return useContext(ThemeContext);
}
```

Now every component only imports:

```jsx
const { theme } = useTheme();
```

instead of

```jsx
const { theme } = useContext(ThemeContext);
```

---

# Step 3: Wrap App

```jsx
import { ThemeProvider } from "./context/ThemeContext";
import Home from "./components/Home";

function App() {
  return (
    <ThemeProvider>
      <Home />
    </ThemeProvider>
  );
}

export default App;
```

---

# Step 4: Use Anywhere

## Navbar.jsx

```jsx
import { useTheme } from "../hooks/useTheme";

function Navbar() {
  const { theme } = useTheme();

  return <h2>Theme : {theme}</h2>;
}

export default Navbar;
```

---

## Settings.jsx

```jsx
import { useTheme } from "../hooks/useTheme";

function Settings() {
  const { toggleTheme } = useTheme();

  return (
    <button onClick={toggleTheme}>
      Change Theme
    </button>
  );
}

export default Settings;
```

---

## Home.jsx

```jsx
import Navbar from "./Navbar";
import Settings from "./Settings";

function Home() {
  return (
    <>
      <Navbar />
      <Settings />
    </>
  );
}

export default Home;
```

Clicking the button updates every component automatically.

---

# Even Better (Production)

Instead of:

```jsx
return useContext(ThemeContext);
```

do this:

```jsx
import { useContext } from "react";
import { ThemeContext } from "../context/ThemeContext";

export function useTheme() {
  const context = useContext(ThemeContext);

  if (!context) {
    throw new Error("useTheme must be used inside ThemeProvider");
  }

  return context;
}
```

Now if someone forgets the provider, React throws a clear error instead of failing silently.

---

# Why Use a Custom Hook?

Without it:

```jsx
import { useContext } from "react";
import { ThemeContext } from "../context/ThemeContext";

const { theme } = useContext(ThemeContext);
```

Every component repeats this code.

With a hook:

```jsx
const { theme } = useTheme();
```

Benefits:

* Cleaner imports
* Less repetition
* Easier refactoring
* Centralized error handling
* Better developer experience

---

# Real Projects Use Context For

## 1. Authentication

```jsx
const { user } = useAuth();
```

Examples:

* logged-in user
* token
* login
* logout
* role

---

## 2. Theme

```jsx
const { theme } = useTheme();
```

Light/Dark mode.

---

## 3. Language

```jsx
const { language } = useLanguage();
```

English, Hindi, Arabic, etc.

---

## 4. Shopping Cart (Small Apps)

```jsx
const { cart, addToCart } = useCart();
```

---

## 5. Notifications

```jsx
const { showToast } = useToast();
```

---

## 6. Sidebar State

```jsx
const { isOpen } = useSidebar();
```

---

## 7. User Preferences

```jsx
const { currency } = useSettings();
```

---

## 8. Permissions

```jsx
const { hasPermission } = usePermissions();
```

Useful for role-based UI.

---

## 9. Modal Management

```jsx
const { openModal } = useModal();
```

---

# When Should You Use Context API?

Use Context when:

* The data is needed by many components.
* The data changes occasionally, not constantly.
* Passing props through many layers becomes messy.

Examples:

* Theme
* Auth
* Language
* User preferences
* Sidebar state
* Feature flags

---

# When Should You NOT Use Context?

Don't use Context for:

### API Server State

❌

```jsx
const users = useContext(UserContext);
```

Better:

```jsx
useQuery()
```

TanStack Query handles caching, refetching, retries, loading states, and more.

---

### Large Complex State

Instead of putting dozens of unrelated values into one context, use Zustand or Redux Toolkit.

---

### Frequently Changing Values

For example:

```
Mouse position

Timer

Animation frame

Scroll position
```

If these change many times per second, every consumer re-renders, which hurts performance.

---

# Production Folder Structure

```
src

context
   AuthContext.jsx
   ThemeContext.jsx
   CartContext.jsx

hooks
   useAuth.js
   useTheme.js
   useCart.js

components
pages
services
utils
```

Each context has its own provider and custom hook.

---

# Best Practices

### ✅ One Context Per Concern

Good:

```
ThemeContext
AuthContext
CartContext
```

Avoid one giant context like:

```
AppContext

theme
user
cart
products
notifications
language
settings
```

---

### ✅ Export a Provider Component

Instead of exposing the provider directly everywhere:

```jsx
export function ThemeProvider({ children }) {
  ...
}
```

---

### ✅ Use a Custom Hook

```jsx
useAuth()
useTheme()
useCart()
```

---

### ✅ Memoize Context Value

If the value is an object, its reference changes on every render. Memoize it to avoid unnecessary re-renders.

```jsx
import { useMemo } from "react";

const value = useMemo(
  () => ({
    theme,
    toggleTheme,
  }),
  [theme]
);

return (
  <ThemeContext.Provider value={value}>
    {children}
  </ThemeContext.Provider>
);
```

---

### ✅ Keep Business Logic in the Provider

Instead of:

```jsx
const { setTheme } = useTheme();
setTheme("dark");
```

Prefer exposing actions:

```jsx
const { toggleTheme } = useTheme();
```

This keeps update logic centralized.

---

### ✅ Throw Helpful Errors in Custom Hooks

```jsx
if (!context) {
  throw new Error("useTheme must be used inside ThemeProvider");
}
```

---

# Context API vs Zustand vs TanStack Query

| Use Case                          | Best Choice             |
| --------------------------------- | ----------------------- |
| Theme                             | Context API             |
| Authentication                    | Context API             |
| Language                          | Context API             |
| Sidebar                           | Context API             |
| Shopping Cart (small app)         | Context API             |
| Shopping Cart (large app)         | Zustand / Redux Toolkit |
| API data (users, posts, products) | TanStack Query          |
| CRUD operations                   | TanStack Query          |
| Cached server data                | TanStack Query          |
| Large client-side app state       | Zustand / Redux Toolkit |

## A common production combination

In many React applications, you'll see all three used together because they solve different problems:

* **Context API** → Global UI state (theme, auth, locale).
* **TanStack Query** → Server state (API data, caching, mutations).
* **Zustand or Redux Toolkit** → Complex client-side state shared across many features.

This separation of responsibilities keeps the application easier to scale and maintain.
