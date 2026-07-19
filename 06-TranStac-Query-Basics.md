# TanStack Query (React Query) Basics Tutorial

If you already know **React + Axios**, TanStack Query is the next step.

Instead of writing lots of `useState`, `useEffect`, `loading`, and `error` logic yourself, TanStack Query manages server data for you.

---

# What is TanStack Query?

It is a library for

* Fetching data
* Caching data
* Updating data
* Synchronizing server state
* Automatic refetching
* Pagination
* Infinite scrolling

Think of it as **Axios + Data Management**.

```
Axios -> Fetches data

TanStack Query -> Uses Axios and manages the data
```

---

# Why not use useEffect?

Without React Query

```jsx
const [users, setUsers] = useState([]);
const [loading, setLoading] = useState(false);
const [error, setError] = useState(null);

useEffect(() => {
  const fetchUsers = async () => {
    try {
      setLoading(true);

      const res = await axios.get("https://dummyjson.com/users");

      setUsers(res.data.users);
    } catch (err) {
      setError(err);
    } finally {
      setLoading(false);
    }
  };

  fetchUsers();
}, []);
```

Lots of code.

---

With React Query

```jsx
const { data, isLoading, error } = useQuery({
  queryKey: ["users"],
  queryFn: fetchUsers,
});
```

Much simpler.

---

# Installation

```bash
npm install @tanstack/react-query
```

---

# Step 1: Create QueryClient

main.jsx

```jsx
import React from "react";
import ReactDOM from "react-dom/client";

import {
  QueryClient,
  QueryClientProvider,
} from "@tanstack/react-query";

import App from "./App";

const queryClient = new QueryClient();

ReactDOM.createRoot(document.getElementById("root")).render(
  <QueryClientProvider client={queryClient}>
    <App />
  </QueryClientProvider>
);
```

Now every component can use React Query.

---

# Step 2: Create API function

```jsx
import axios from "axios";

export const fetchUsers = async () => {
  const res = await axios.get("https://dummyjson.com/users");

  return res.data.users;
};
```

Notice we return

```jsx
res.data.users
```

not

```jsx
res
```

---

# Step 3: Use useQuery

```jsx
import { useQuery } from "@tanstack/react-query";
import { fetchUsers } from "./api";

function Users() {
  const {
    data,
    isLoading,
    error,
  } = useQuery({
    queryKey: ["users"],
    queryFn: fetchUsers,
  });

  if (isLoading) return <p>Loading...</p>;

  if (error) return <p>Error</p>;

  return (
    <div>
      {data.map((user) => (
        <p key={user.id}>{user.firstName}</p>
      ))}
    </div>
  );
}

export default Users;
```

Done!

No `useEffect`.

No `useState`.

---

# Understanding useQuery

```jsx
const result = useQuery({
    queryKey: ["users"],
    queryFn: fetchUsers
});
```

Returns

```jsx
{
 data,
 error,
 isLoading,
 isError,
 isSuccess,
 isFetching,
 refetch
}
```

---

## data

Fetched data

```jsx
data
```

Example

```jsx
[
 { id:1, firstName:"John" },
 { id:2, firstName:"Jane" }
]
```

---

## isLoading

True only on first fetch.

```jsx
if(isLoading){
    return <p>Loading...</p>;
}
```

---

## error

Contains error object.

```jsx
if(error){
    return <p>Something went wrong</p>;
}
```

---

## isFetching

Whenever React Query is fetching data again.

Example

```
Initial fetch

Loading...

↓

User comes back to page

↓

Fetching...

↓

Data updated
```

Unlike `isLoading`, this can become true multiple times.

---

## refetch

Refetch manually.

```jsx
const { refetch } = useQuery(...);

<button onClick={refetch}>
Refresh
</button>
```

---

# queryKey

Most important concept.

```jsx
queryKey: ["users"]
```

React Query stores cache using this key.

```
Cache

users
↓

John
Jane
Alex
```

Another query

```jsx
queryKey:["products"]
```

Creates another cache.

```
Cache

users

products
```

---

# Why queryKey matters

Suppose

```jsx
queryKey:["users"]
```

React Query saves

```
users -> cached
```

If another page asks

```jsx
queryKey:["users"]
```

No API call.

Uses cache.

Much faster.

---

# Dynamic queryKey

Fetching user by id

```jsx
queryKey:["user", id]
```

Examples

```
["user",1]

["user",2]

["user",3]
```

Each user has separate cache.

---

# Passing parameters

```jsx
const fetchUser = async ({ queryKey }) => {
  const [, id] = queryKey;

  const res = await axios.get(
    `https://dummyjson.com/users/${id}`
  );

  return res.data;
};
```

Use

```jsx
const { data } = useQuery({
    queryKey:["user",5],
    queryFn:fetchUser
});
```

---

# staleTime

Default behavior

```
Open page

↓

API called

↓

Leave page

↓

Come back

↓

API called again
```

Sometimes you don't want that.

```jsx
useQuery({
    queryKey:["users"],
    queryFn:fetchUsers,
    staleTime:60000
});
```

```
60000 ms

=

1 minute
```

Within one minute

No API request.

Uses cache.

---

# gcTime (cacheTime in older versions)

Controls how long unused cached data stays in memory.

```jsx
useQuery({
  queryKey: ["users"],
  queryFn: fetchUsers,
  gcTime: 5 * 60 * 1000, // 5 minutes
});
```

If no component uses the query, the cache is removed after 5 minutes.

---

# Refetch on Window Focus

Default

```
User switches tab

↓

Returns

↓

React Query fetches latest data
```

Disable

```jsx
useQuery({
    queryKey:["users"],
    queryFn:fetchUsers,
    refetchOnWindowFocus:false
});
```

---

# Retry

If API fails

```
Try

↓

Fail

↓

Try again

↓

Fail

↓

Try again
```

Default retry behavior is enabled for many errors.

Disable

```jsx
useQuery({
    queryKey:["users"],
    queryFn:fetchUsers,
    retry:false
});
```

Or set retries

```jsx
retry:2
```

---

# DevTools

Install

```bash
npm install @tanstack/react-query-devtools
```

Add

```jsx
import { ReactQueryDevtools } from "@tanstack/react-query-devtools";

<QueryClientProvider client={queryClient}>
  <App />
  <ReactQueryDevtools initialIsOpen={false} />
</QueryClientProvider>
```

This lets you inspect cached queries, their status, and refetch behavior.

---

# Query Lifecycle

```
Component Mounts
        │
        ▼
useQuery()
        │
        ▼
Calls queryFn()
        │
        ▼
API Request
        │
        ▼
Stores in Cache
        │
        ▼
Returns data
        │
        ▼
Next component with same queryKey
        │
        ▼
Uses Cache (or refetches if stale)
```

---

# Common Interview Questions

### 1. What is TanStack Query?

A library for fetching, caching, synchronizing, and updating server state in React.

---

### 2. Difference between Axios and React Query?

| Axios                         | TanStack Query                 |
| ----------------------------- | ------------------------------ |
| Makes HTTP requests           | Manages server state           |
| No caching                    | Built-in caching               |
| Manual loading/error handling | Automatic loading/error states |
| No refetch logic              | Automatic refetching           |
| No cache invalidation         | Built-in invalidation          |

---

### 3. What is `queryKey`?

A unique key used to identify and cache a query.

Example:

```jsx
["users"]
["user", 1]
["products", "laptops"]
```

---

### 4. What is `queryFn`?

The async function that fetches data.

```jsx
queryFn: fetchUsers
```

---

### 5. Difference between `isLoading` and `isFetching`?

* **`isLoading`**: `true` only during the initial load when no cached data exists.
* **`isFetching`**: `true` whenever a fetch is in progress, including background refetches.

---


# 1. `useMutation`

## What is it?

`useMutation` is used to **change data on the server**.

Unlike `useQuery` (which fetches data), `useMutation` is used for:

* ➕ Create (POST)
* ✏️ Update (PUT/PATCH)
* ❌ Delete (DELETE)
* 🔐 Login / Register

### Simple Syntax

```jsx
const mutation = useMutation({
  mutationFn: addUser,
});
```

Call the mutation:

```jsx
mutation.mutate({
  firstName: "John",
  age: 25,
});
```

### Useful States

```jsx
mutation.isPending   // Loading
mutation.isSuccess   // Success
mutation.isError     // Error
mutation.error       // Error object
```

### Easy to Remember

```text
useQuery      → GET data

useMutation   → POST, PUT, PATCH, DELETE
```

---

# 2. Query Invalidation

## What is it?

After a mutation (Create, Update, Delete), the cached data becomes **outdated**.

**Query Invalidation** tells React Query:

> "This data is stale. Fetch the latest data from the server."

### Example

Current users:

```text
John
Alex
Mike
```

You add:

```text
David
```

Database:

```text
John
Alex
Mike
David
```

But the UI still shows:

```text
John
Alex
Mike
```

So invalidate the query:

```jsx
const queryClient = useQueryClient();

const mutation = useMutation({
  mutationFn: addUser,

  onSuccess: () => {
    queryClient.invalidateQueries({
      queryKey: ["users"],
    });
  },
});
```

React Query automatically refetches the users list, and the UI updates.

### Easy Flow

```text
useMutation
      ↓
Data Changed
      ↓
invalidateQueries()
      ↓
useQuery runs again
      ↓
Updated UI
```

---

## Difference Between Them

| `useMutation`                     | Query Invalidation                         |
| --------------------------------- | ------------------------------------------ |
| Changes data on the server        | Refreshes outdated cached data             |
| Used for POST, PUT, PATCH, DELETE | Usually called after a successful mutation |
| Sends the request                 | Triggers `useQuery` to fetch fresh data    |

### One-line interview answers

* **`useMutation`**: "It is used to create, update, or delete data on the server."
* **Query Invalidation**: "It marks cached data as stale so React Query refetches the latest data after a mutation."


---

Here's a **simple React + Axios + TanStack Query** example that demonstrates both:

* ✅ `useMutation`
* ✅ `Query Invalidation`

We'll use **DummyJSON**.

> **Note:** `https://dummyjson.com/users/add` simulates adding a user. It does **not** permanently save data because it's a demo API, but it's perfect for learning.

---

## Folder Structure

```text
src/
│── api.js
│── main.jsx
│── App.jsx
```

---

# Step 1: Install

```bash
npm install axios @tanstack/react-query
```

---

# Step 2: main.jsx

```jsx
import React from "react";
import ReactDOM from "react-dom/client";

import {
  QueryClient,
  QueryClientProvider,
} from "@tanstack/react-query";

import App from "./App";

const queryClient = new QueryClient();

ReactDOM.createRoot(document.getElementById("root")).render(
  <QueryClientProvider client={queryClient}>
    <App />
  </QueryClientProvider>
);
```

---

# Step 3: api.js

```jsx
import axios from "axios";

// GET Users
export const fetchUsers = async () => {
  const res = await axios.get("https://dummyjson.com/users");

  return res.data.users;
};

// POST User
export const addUser = async (user) => {
  const res = await axios.post(
    "https://dummyjson.com/users/add",
    user
  );

  return res.data;
};
```

---

# Step 4: App.jsx

```jsx
import { useQuery, useMutation, useQueryClient } from "@tanstack/react-query";
import { fetchUsers, addUser } from "./api";

function App() {
  const queryClient = useQueryClient();

  // GET Users
  const {
    data: users,
    isLoading,
    error,
  } = useQuery({
    queryKey: ["users"],
    queryFn: fetchUsers,
  });

  // POST User
  const mutation = useMutation({
    mutationFn: addUser,

    onSuccess: () => {
      alert("User Added!");

      // Refresh users list
      queryClient.invalidateQueries({
        queryKey: ["users"],
      });
    },
  });

  const handleAddUser = () => {
    mutation.mutate({
      firstName: "Shahbaz",
      age: 24,
    });
  };

  if (isLoading) return <h2>Loading...</h2>;

  if (error) return <h2>Something went wrong</h2>;

  return (
    <div style={{ padding: "20px" }}>
      <h1>Users</h1>

      <button onClick={handleAddUser}>
        Add User
      </button>

      <br />
      <br />

      {mutation.isPending && <p>Adding user...</p>}

      <ul>
        {users.map((user) => (
          <li key={user.id}>
            {user.firstName}
          </li>
        ))}
      </ul>
    </div>
  );
}

export default App;
```

---

# What happens?

### 1. Page Loads

```text
useQuery

↓

GET /users

↓

Users displayed
```

---

### 2. Click "Add User"

```text
Button Click

↓

mutation.mutate()

↓

POST /users/add

↓

User Added
```

---

### 3. onSuccess Runs

```jsx
onSuccess: () => {
  queryClient.invalidateQueries({
    queryKey: ["users"],
  });
}
```

This tells React Query:

> "The `users` data is now outdated. Fetch it again."

---

### 4. React Query Automatically Refetches

```text
GET /users

↓

Updated list
```

---

# Key Lines to Remember for Interviews

### Fetch data

```jsx
const { data } = useQuery({
  queryKey: ["users"],
  queryFn: fetchUsers,
});
```

### Change data

```jsx
const mutation = useMutation({
  mutationFn: addUser,
});
```

### Call mutation

```jsx
mutation.mutate({
  firstName: "Shahbaz",
});
```

### Refresh cache after success

```jsx
onSuccess: () => {
  queryClient.invalidateQueries({
    queryKey: ["users"],
  });
}
```

This example demonstrates the complete flow that interviewers commonly expect:

**`useQuery` → `useMutation` → `onSuccess` → `invalidateQueries` → automatic refetch of updated data.**
