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

# Complete Folder Structure

```
src

App.jsx

UserContext.jsx

Home.jsx
```

---

## UserContext.jsx

```jsx
import { createContext } from "react";

export const UserContext = createContext();
```

---

## Home.jsx

```jsx
import { useContext } from "react";
import { UserContext } from "./UserContext";

function Home() {
  const user = useContext(UserContext);

  return (
    <>
      <h2>{user.name}</h2>
      <p>{user.age}</p>
    </>
  );
}

export default Home;
```

---

## App.jsx

```jsx
import Home from "./Home";
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

# Interview Questions

### Why use Context API?

To avoid **prop drilling** and share data across components.

### What does `createContext()` do?

Creates a context object.

### What does `Provider` do?

Makes data available to all child components.

### What does `useContext()` do?

Reads the nearest context value.

### Can Context update values?

Yes. Pass state and its setter in the provider's `value`.

```jsx
value={{ user, setUser }}
```

### Is Context API a state management library?

Not exactly. It is a way to **share state**, not replace advanced state management solutions.

---

# Fresher Interview Checklist

Make sure you can:

* ✅ Explain prop drilling.
* ✅ Create a context with `createContext()`.
* ✅ Wrap components with `Context.Provider`.
* ✅ Read data using `useContext()`.
* ✅ Share state (`useState`) through context.
* ✅ Update context values from child components.
* ✅ Know when to use Context API vs Zustand/Redux vs TanStack Query.

These are the Context API concepts most commonly expected in React interviews for fresher to intermediate frontend roles.
