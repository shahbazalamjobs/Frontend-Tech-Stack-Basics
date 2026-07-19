# Axios Basics Tutorial (React + TypeScript)

Axios is a popular HTTP client used to communicate with REST APIs. It simplifies making GET, POST, PUT, DELETE requests compared to the built-in `fetch()` API.

---

# 1. Install Axios

```bash
npm install axios
```

---

# 2. Import Axios

```tsx
import axios from "axios";
```

---

# 3. Your First GET Request

Example API

```
https://dummyjson.com/users
```

```tsx
import { useEffect } from "react";
import axios from "axios";

function App() {
  useEffect(() => {
    axios.get("https://dummyjson.com/users")
      .then((response) => {
        console.log(response.data);
      })
      .catch((error) => {
        console.log(error);
      });
  }, []);

  return <h1>Axios Example</h1>;
}

export default App;
```

---

# 4. Using async/await

Instead of `.then()`

```tsx
import { useEffect } from "react";
import axios from "axios";

function App() {

  const fetchUsers = async () => {
    try {
      const response = await axios.get("https://dummyjson.com/users");

      console.log(response.data);

    } catch (error) {
      console.log(error);
    }
  };

  useEffect(() => {
    fetchUsers();
  }, []);

  return <h1>Axios</h1>;
}
```

This is the preferred approach.

---

# 5. Axios Response Object

```tsx
const response = await axios.get(url);

console.log(response);
```

Response contains

```tsx
response.data
response.status
response.statusText
response.headers
response.config
```

Most of the time you'll use

```tsx
response.data
```

---

# 6. Display Data

```tsx
import { useEffect, useState } from "react";
import axios from "axios";

function App() {

  const [users, setUsers] = useState([]);

  useEffect(() => {

    const fetchUsers = async () => {
      const response = await axios.get(
        "https://dummyjson.com/users"
      );

      setUsers(response.data.users);
    };

    fetchUsers();

  }, []);

  return (
    <>
      {users.map((user: any) => (
        <p key={user.id}>
          {user.firstName}
        </p>
      ))}
    </>
  );
}
```

---

# 7. POST Request

Creating data

```tsx
const addUser = async () => {

  const response = await axios.post(
    "https://dummyjson.com/users/add",
    {
      firstName: "John",
      age: 25,
    }
  );

  console.log(response.data);
};
```

---

# 8. PUT Request

Update an entire resource.

```tsx
const updateUser = async () => {

  const response = await axios.put(
    "https://dummyjson.com/users/1",
    {
      firstName: "Alex",
    }
  );

  console.log(response.data);
};
```

---

# 9. PATCH Request

Update only some fields.

```tsx
const updateUser = async () => {

  const response = await axios.patch(
    "https://dummyjson.com/users/1",
    {
      age: 30,
    }
  );

  console.log(response.data);
};
```

---

# 10. DELETE Request

```tsx
const deleteUser = async () => {

  const response = await axios.delete(
    "https://dummyjson.com/users/1"
  );

  console.log(response.data);
};
```

---

# 11. Sending Query Parameters

Instead of writing

```
/users?limit=10&skip=20
```

Use

```tsx
axios.get("https://dummyjson.com/users", {
  params: {
    limit: 10,
    skip: 20,
  },
});
```

Axios automatically creates

```
/users?limit=10&skip=20
```

---

# 12. URL Parameters

```
/users/5
```

```tsx
const id = 5;

axios.get(`https://dummyjson.com/users/${id}`);
```

---

# 13. Request Headers

```tsx
axios.get(url, {
  headers: {
    Authorization: "Bearer token",
  },
});
```

Example with JWT

```tsx
const token = localStorage.getItem("token");

axios.get("/profile", {
  headers: {
    Authorization: `Bearer ${token}`,
  },
});
```

---

# 14. Base URL

Instead of writing

```tsx
https://dummyjson.com/users
```

everywhere

Create

```tsx
// api.ts

import axios from "axios";

export const api = axios.create({
  baseURL: "https://dummyjson.com",
});
```

Now

```tsx
import { api } from "./api";

const response = await api.get("/users");
```

Much cleaner.

---

# 15. Axios Instance

```tsx
import axios from "axios";

export const api = axios.create({
  baseURL: "https://dummyjson.com",
  timeout: 5000,
});
```

Benefits

* Base URL
* Timeout
* Default Headers
* Interceptors
* Cleaner imports

---

# 16. Default Headers

```tsx
const api = axios.create({
  baseURL: "https://dummyjson.com",
  headers: {
    "Content-Type": "application/json",
  },
});
```

---

# 17. Request Interceptor

Runs before every request.

```tsx
api.interceptors.request.use((config) => {

  const token = localStorage.getItem("token");

  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }

  return config;
});
```

Useful for JWT authentication.

---

# 18. Response Interceptor

Runs after every response.

```tsx
api.interceptors.response.use(
  (response) => {
    return response;
  },
  (error) => {

    console.log(error);

    return Promise.reject(error);
  }
);
```

Useful for

* Token expired
* Logging
* Global error handling

---

# 19. Loading State

```tsx
const [loading, setLoading] = useState(false);

const fetchUsers = async () => {

  setLoading(true);

  try {

    const response = await axios.get(url);

    setUsers(response.data.users);

  } finally {

    setLoading(false);

  }
};
```

Render

```tsx
if (loading) return <p>Loading...</p>;
```

---

# 20. Error State

```tsx
const [error, setError] = useState("");

try {

  const response = await axios.get(url);

} catch (err) {

  setError("Something went wrong");

}
```

Render

```tsx
if (error) return <p>{error}</p>;
```

---

# 21. Cancellation (AbortController)

Useful when a component unmounts before a request finishes.

```tsx
useEffect(() => {
  const controller = new AbortController();

  const fetchUsers = async () => {
    try {
      const response = await axios.get(
        "https://dummyjson.com/users",
        {
          signal: controller.signal,
        }
      );

      console.log(response.data);
    } catch (error) {
      console.log(error);
    }
  };

  fetchUsers();

  return () => controller.abort();
}, []);
```

---

# 22. TypeScript Types

Instead of using `any`, define interfaces.

```tsx
interface User {
  id: number;
  firstName: string;
  age: number;
}
```

Use with Axios:

```tsx
const response = await axios.get<{ users: User[] }>(
  "https://dummyjson.com/users"
);

setUsers(response.data.users);
```

---

# 23. Folder Structure

```
src/
│
├── api/
│   ├── axios.ts
│   └── userApi.ts
│
├── hooks/
│
├── pages/
│
├── components/
│
└── App.tsx
```

**`axios.ts`**

```tsx
import axios from "axios";

export const api = axios.create({
  baseURL: "https://dummyjson.com",
  timeout: 5000,
  headers: {
    "Content-Type": "application/json",
  },
});

api.interceptors.request.use((config) => {
  const token = localStorage.getItem("token");

  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }

  return config;
});
```

**`userApi.ts`**

```tsx
import { api } from "./axios";

export const getUsers = () => api.get("/users");

export const getUser = (id: number) => api.get(`/users/${id}`);

export const createUser = (data: object) =>
  api.post("/users/add", data);

export const updateUser = (id: number, data: object) =>
  api.put(`/users/${id}`, data);

export const deleteUser = (id: number) =>
  api.delete(`/users/${id}`);
```

Then in your component:

```tsx
const response = await getUsers();
```

---

# 24. Axios vs Fetch

| Feature                     | Axios                       | Fetch                      |
| --------------------------- | --------------------------- | -------------------------- |
| JSON parsing                | Automatic (`response.data`) | Manual (`response.json()`) |
| Base URL                    | ✅                           | ❌                          |
| Interceptors                | ✅                           | ❌                          |
| Timeout support             | ✅                           | ❌ (manual)                 |
| Request cancellation        | ✅                           | ✅                          |
| Better error handling       | ✅                           | Basic                      |
| Request/Response transforms | ✅                           | ❌                          |

---

# 25. Best Practices

* Create a single Axios instance (`axios.create()`).
* Keep API calls in separate service files (e.g., `userApi.ts`).
* Use `async/await` with `try/catch`.
* Use TypeScript interfaces instead of `any`.
* Use interceptors to automatically attach JWT tokens.
* Show loading and error states in the UI.
* Cancel in-flight requests on component unmount when appropriate.
* Use **TanStack Query** for server-state management instead of calling Axios directly in every component.

## What to Learn Next

After mastering Axios, move on to:

1. Public Backend API Integration
2. TanStack Query (React Query)

   * Queries
   * Mutations
   * Caching
   * Query Keys
   * Invalidation
   * Prefetching
   * Infinite Queries
3. JWT Authentication with Axios Interceptors
4. Protected Routes and Role-Based Access Control (RBAC)

Once you understand these topics, you'll have the API and data-fetching skills commonly expected in modern React frontend projects.
