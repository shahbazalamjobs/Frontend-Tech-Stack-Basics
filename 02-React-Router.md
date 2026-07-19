These are the core routing concepts you'll use in almost every React application. Below is a practical tutorial using **React Router v7** (the API is very similar to v6).

---

# 1. Routing

Routing lets you display different pages without reloading the browser.

Example:

```
/               → Home
/about          → About
/contact        → Contact
/products       → Products
/products/1     → Product Details
/login          → Login
```

Instead of reloading HTML pages, React changes the displayed component.

---

# 2. Install React Router

```bash
npm install react-router
```

---

# 3. Folder Structure

```
src/

pages/
    Home.jsx
    About.jsx
    Contact.jsx
    Login.jsx

App.jsx
main.jsx
```

---

# 4. BrowserRouter

Wrap your application.

```jsx
import { BrowserRouter } from "react-router";
import ReactDOM from "react-dom/client";
import App from "./App";

ReactDOM.createRoot(document.getElementById("root")).render(
  <BrowserRouter>
    <App />
  </BrowserRouter>
);
```

---

# 5. React Router Basics

```jsx
import { Routes, Route } from "react-router";

import Home from "./pages/Home";
import About from "./pages/About";
import Contact from "./pages/Contact";

function App() {
  return (
    <Routes>

      <Route path="/" element={<Home />} />

      <Route path="/about" element={<About />} />

      <Route path="/contact" element={<Contact />} />

    </Routes>
  );
}

export default App;
```

Visit

```
localhost:5173/
localhost:5173/about
localhost:5173/contact
```

---

# 6. Navigation

Don't use

```jsx
<a href="/about">About</a>
```

Use

```jsx
import { Link } from "react-router";

<Link to="/">Home</Link>

<Link to="/about">About</Link>

<Link to="/contact">Contact</Link>
```

No page refresh.

---

# 7. NavLink

Useful for highlighting the active page.

```jsx
import { NavLink } from "react-router";

<NavLink to="/">
    Home
</NavLink>

<NavLink to="/about">
    About
</NavLink>
```

Example with active styles:

```jsx
<NavLink
  to="/about"
  className={({ isActive }) =>
    isActive ? "active" : ""
  }
>
  About
</NavLink>
```

---

# 8. Nested Routes

Used when pages share a common layout.

Example

```
Dashboard

    Home

    Users

    Settings
```

URL

```
/dashboard
/dashboard/users
/dashboard/settings
```

### Dashboard Layout

```jsx
import { Outlet } from "react-router";

function Dashboard() {
    return (
        <>
            <h1>Dashboard</h1>

            <Outlet />
        </>
    );
}
```

---

### Routes

```jsx
<Routes>

<Route path="/dashboard" element={<Dashboard />}>

    <Route index element={<DashboardHome />} />

    <Route path="users" element={<Users />} />

    <Route path="settings" element={<Settings />} />

</Route>

</Routes>
```

Result

```
/dashboard
```

Shows

```
Dashboard

Dashboard Home
```

---

```
/dashboard/users
```

Shows

```
Dashboard

Users
```

---

# 9. Dynamic Routes

Suppose you have

```
Product 1

Product 2

Product 3
```

Instead of

```
/product1

/product2

/product3
```

Use

```
/products/:id
```

Route

```jsx
<Route
path="/products/:id"
element={<ProductDetails />}
/>
```

---

# 10. Route Parameters

Read the dynamic value.

```jsx
import { useParams } from "react-router";

function ProductDetails() {

    const { id } = useParams();

    return <h1>{id}</h1>;
}
```

Visit

```
/products/5
```

Output

```
5
```

---

Example

```jsx
const { id } = useParams();

const product = products.find(
    p => p.id === Number(id)
);
```

---

# 11. useNavigate

Navigate using JavaScript.

```jsx
import { useNavigate } from "react-router";

const navigate = useNavigate();

<button
onClick={() => navigate("/login")}
>
Login
</button>
```

Back

```jsx
navigate(-1);
```

Forward

```jsx
navigate(1);
```

---

# 12. Protected Routes

Only logged-in users should access

```
Dashboard

Profile

Settings
```

---

### Fake Login

```jsx
const isLoggedIn = true;
```

---

### ProtectedRoute.jsx

```jsx
import {
Navigate
} from "react-router";

function ProtectedRoute({ children }) {

    const isLoggedIn = true;

    if (!isLoggedIn) {
        return <Navigate to="/login" replace />;
    }

    return children;
}

export default ProtectedRoute;
```

---

Usage

```jsx
<Route

path="/dashboard"

element={

<ProtectedRoute>

<Dashboard />

</ProtectedRoute>

}

/>
```

If logged in

```
Dashboard
```

Else

```
Redirect to Login
```

---

## Role-Based Access (RBAC)

```jsx
function ProtectedRoute({
    children,
    role
}) {

    const user = {
        role: "admin"
    };

    if (user.role !== role) {
        return <Navigate to="/" replace />;
    }

    return children;
}
```

Usage

```jsx
<Route

path="/admin"

element={

<ProtectedRoute role="admin">

<Admin />

</ProtectedRoute>

}

/>
```

---

# 13. Lazy Loading

Loads components only when needed.

Without lazy loading

```
Home
About
Dashboard
Products
Profile

All downloaded initially
```

With lazy loading

```
Download only when user visits page
```

---

Import

```jsx
import {
lazy,
Suspense
} from "react";
```

---

Lazy Component

```jsx
const Dashboard =
lazy(() => import("./Dashboard"));
```

---

Wrap

```jsx
<Suspense
fallback={<p>Loading...</p>}
>

<Dashboard />

</Suspense>
```

---

With Routes

```jsx
const Home = lazy(() => import("./Home"));
const About = lazy(() => import("./About"));

function App() {
  return (
    <Suspense fallback={<h2>Loading...</h2>}>

      <Routes>

        <Route
          path="/"
          element={<Home />}
        />

        <Route
          path="/about"
          element={<About />}
        />

      </Routes>

    </Suspense>
  );
}
```

Only the page the user visits is downloaded.

---

# Complete Route Example

```jsx
<Routes>

  <Route path="/" element={<Home />} />

  <Route path="/about" element={<About />} />

  <Route path="/login" element={<Login />} />

  <Route path="/products" element={<Products />} />

  <Route
    path="/products/:id"
    element={<ProductDetails />}
  />

  <Route
    path="/dashboard"
    element={
      <ProtectedRoute>
        <Dashboard />
      </ProtectedRoute>
    }
  >
    <Route index element={<DashboardHome />} />
    <Route path="users" element={<Users />} />
    <Route path="settings" element={<Settings />} />
  </Route>

  <Route path="*" element={<NotFound />} />

</Routes>
```

---

# Interview Questions

* What is client-side routing?
* Difference between `Link` and `NavLink`?
* What is `Outlet` used for?
* Difference between nested routes and child components?
* How does `useParams()` work?
* Difference between `useNavigate()` and `Link`?
* How do you implement protected routes with JWT?
* How do you implement role-based routes (RBAC)?
* What is lazy loading and why is it important?
* Difference between `React.lazy()` and dynamic `import()`?
* What does `Suspense` do?
* What is a catch-all (`*`) route and when should you use it?

## Practice Project

Build an **Employee Management System** with these routes:

```
/
├── login
├── employees
├── employees/:id
├── profile
├── dashboard
│   ├── (index) Dashboard Home
│   ├── users
│   └── settings
└── admin
```

Implement:

* Public login page
* Protected dashboard and profile
* Admin-only route using RBAC
* Employee details with dynamic route parameters
* Nested dashboard layout with `Outlet`
* Lazy-load dashboard, admin, and employee detail pages
* A `404 Not Found` route (`path="*"`)
