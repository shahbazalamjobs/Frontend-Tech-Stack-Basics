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

## What to Learn Next

Once you're comfortable with `useQuery`, the next React Query topics are:

1. **Mutations (`useMutation`)** – Create, update, and delete data.
2. **Query Invalidation** – Refresh related queries after a mutation.
3. **Optimistic Updates** – Update the UI immediately before the server responds.
4. **Pagination** – Fetch data page by page.
5. **Infinite Queries** – Implement "Load More" and infinite scrolling.
6. **Prefetching** – Load data before the user navigates to a page.

For a fresher-to-intermediate React developer, mastering these topics will cover the majority of React Query usage in interviews and real-world CRUD applications.
