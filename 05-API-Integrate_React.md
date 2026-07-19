# Public Backend API Integration in React (Beginner → Intermediate)

Public Backend API Integration means **connecting your React app to an API provided by someone else** to fetch, display, create, update, or delete data.

Examples of public APIs:

* DummyJSON
* JSONPlaceholder
* ReqRes
* GitHub API
* OpenWeather API

---

# Why do we need APIs?

Without API:

```
React
 |
Hardcoded Data
```

Example

```jsx
const users = [
  { id: 1, name: "John" },
  { id: 2, name: "Alice" }
];
```

Not useful for real applications.

---

With API

```
React
    |
Axios / fetch
    |
Backend API
    |
Database
```

Example

```
React
   ↓
GET https://dummyjson.com/users
   ↓
Backend sends JSON
   ↓
React displays users
```

---

# API Response

Request

```
GET https://dummyjson.com/users
```

Response

```json
{
  "users": [
    {
      "id": 1,
      "firstName": "Emily",
      "lastName": "Johnson"
    },
    {
      "id": 2,
      "firstName": "Michael",
      "lastName": "Williams"
    }
  ],
  "total": 208,
  "skip": 0,
  "limit": 30
}
```

React receives this JSON.

---

# Installing Axios

```bash
npm install axios
```

---

# Folder Structure

```
src/

components/
pages/
services/
App.jsx
```

Create

```
services/api.js
```

---

# Creating Axios Instance

```javascript
import axios from "axios";

const api = axios.create({
  baseURL: "https://dummyjson.com",
});

export default api;
```

Instead of writing

```javascript
axios.get("https://dummyjson.com/users");
```

You write

```javascript
api.get("/users");
```

Cleaner and reusable.

---

# GET Request (Fetch Data)

```jsx
import { useEffect, useState } from "react";
import api from "./services/api";

function App() {
  const [users, setUsers] = useState([]);

  useEffect(() => {
    const fetchUsers = async () => {
      const res = await api.get("/users");
      setUsers(res.data.users);
    };

    fetchUsers();
  }, []);

  return (
    <>
      {users.map((user) => (
        <p key={user.id}>{user.firstName}</p>
      ))}
    </>
  );
}

export default App;
```

Flow

```
Component Loads

↓

useEffect()

↓

API Call

↓

Server

↓

JSON Response

↓

setUsers()

↓

UI Re-renders
```

---

# Loading State

Without loading state

```
Blank Screen
↓

Data Appears
```

Better

```jsx
const [loading, setLoading] = useState(true);

const fetchUsers = async () => {
  const res = await api.get("/users");

  setUsers(res.data.users);
  setLoading(false);
};

if (loading) return <h2>Loading...</h2>;
```

---

# Error Handling

```jsx
const [error, setError] = useState("");

const fetchUsers = async () => {
  try {
    const res = await api.get("/users");
    setUsers(res.data.users);
  } catch (err) {
    setError("Failed to fetch users");
  }
};

if (error) return <h2>{error}</h2>;
```

---

# Complete Example

```jsx
import { useEffect, useState } from "react";
import api from "./services/api";

function App() {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState("");

  useEffect(() => {
    const fetchUsers = async () => {
      try {
        const res = await api.get("/users");
        setUsers(res.data.users);
      } catch (err) {
        setError("Something went wrong");
      } finally {
        setLoading(false);
      }
    };

    fetchUsers();
  }, []);

  if (loading) return <h1>Loading...</h1>;

  if (error) return <h1>{error}</h1>;

  return (
    <>
      {users.map((user) => (
        <div key={user.id}>
          <h3>{user.firstName}</h3>
          <p>{user.email}</p>
        </div>
      ))}
    </>
  );
}

export default App;
```

---

# POST Request

Create a new user.

```jsx
const addUser = async () => {
  const res = await api.post("/users/add", {
    firstName: "Shahbaz",
    age: 23,
  });

  console.log(res.data);
};
```

---

# PUT Request

Update existing user.

```jsx
const updateUser = async () => {
  const res = await api.put("/users/1", {
    firstName: "Updated Name",
  });

  console.log(res.data);
};
```

---

# DELETE Request

```jsx
const deleteUser = async () => {
  const res = await api.delete("/users/1");

  console.log(res.data);
};
```

---

# Using Query Parameters

Instead of

```
/users
```

Use

```
/users?limit=5
```

Axios

```jsx
const res = await api.get("/users?limit=5");
```

Or

```jsx
const res = await api.get("/users", {
  params: {
    limit: 5,
    skip: 10,
  },
});
```

Result

```
/users?limit=5&skip=10
```

---

# Dynamic Route Parameters

```jsx
const userId = 5;

const res = await api.get(`/users/${userId}`);
```

URL

```
/users/5
```

---

# Search API

DummyJSON supports search.

```jsx
const res = await api.get("/users/search", {
  params: {
    q: "john",
  },
});
```

URL

```
/users/search?q=john
```

---

# Async/Await Flow

```
Button Click

↓

axios.get()

↓

Wait

↓

Server Responds

↓

res.data

↓

Update State

↓

React Re-renders
```

---

# Best Practices

✅ Keep API calls in a `services` folder.

```text
services/
  api.js
```

✅ Use `async/await` instead of `.then()` for readability.

✅ Always handle loading and error states.

✅ Use `try...catch` around API calls.

✅ Use `params` for query strings instead of manually concatenating URLs.

---

# Common Public APIs for Practice

* **DummyJSON** – Users, Products, Todos, Posts
* **JSONPlaceholder** – Posts, Comments, Albums
* **ReqRes** – Authentication and CRUD practice
* **GitHub API** – Users and repositories
* **OpenWeather API** – Weather data (API key required)

---

# What You Should Know for a Fresher

* ✅ What an API is
* ✅ Difference between frontend and backend
* ✅ REST methods: `GET`, `POST`, `PUT`, `DELETE`
* ✅ Fetch data with Axios
* ✅ Read data from `response.data`
* ✅ Display data using `map()`
* ✅ Handle loading and error states
* ✅ Pass query parameters with `params`
* ✅ Fetch a single resource using a dynamic URL (e.g., `/users/:id`)
* ✅ Organize API code in a reusable service file

Once you're comfortable with these basics, the next step is learning **TanStack Query (React Query)**, which manages API fetching, caching, loading states, retries, and background updates automatically.
