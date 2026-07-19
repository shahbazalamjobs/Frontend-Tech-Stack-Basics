If you're learning React 19, TypeScript is one of the most valuable additions because it helps catch errors while coding and improves autocomplete. Here's a beginner-friendly tutorial.

---

# 1. What is TypeScript?

TypeScript is JavaScript + Types.

Instead of

```js
let name = "John";
```

you can specify the type

```ts
let name: string = "John";
```

If you try

```ts
name = 10;
```

❌ Error

```
Type 'number' is not assignable to type 'string'
```

---

# 2. Basic Types

## String

```ts
let username: string = "Shahbaz";
```

---

## Number

```ts
let age: number = 23;
```

---

## Boolean

```ts
let isLoggedIn: boolean = true;
```

---

## Array

```ts
let numbers: number[] = [1, 2, 3];

let names: string[] = ["John", "Jane"];
```

Alternative

```ts
let numbers: Array<number> = [1, 2, 3];
```

---

## Any

Avoid using whenever possible.

```ts
let value: any = "Hello";

value = 10;

value = true;
```

---

## Unknown

Safer than `any`.

```ts
let data: unknown = "Hello";

if (typeof data === "string") {
  console.log(data.toUpperCase());
}
```

---

## Void

Functions that return nothing.

```ts
function greet(): void {
  console.log("Hello");
}
```

---

## Null & Undefined

```ts
let a: null = null;

let b: undefined = undefined;
```

---

# 3. Type Inference

TypeScript often figures out the type automatically.

```ts
let city = "Pune";
```

TypeScript understands

```ts
let city: string
```

No need to write it.

---

# 4. Functions

Without types

```js
function add(a, b) {
    return a + b;
}
```

With TypeScript

```ts
function add(a: number, b: number): number {
    return a + b;
}
```

---

Arrow Function

```ts
const multiply = (a: number, b: number): number => {
    return a * b;
}
```

---

Optional Parameter

```ts
function greet(name?: string) {
    console.log(name);
}
```

Usage

```ts
greet();

greet("John");
```

---

Default Parameter

```ts
function greet(name: string = "Guest") {
    console.log(name);
}
```

---

# 5. Objects

```ts
const user: {
    name: string;
    age: number;
} = {
    name: "John",
    age: 22
};
```

---

Better approach

```ts
type User = {
    name: string;
    age: number;
};

const user: User = {
    name: "John",
    age: 22
};
```

---

# 6. Type Alias

```ts
type Employee = {
    id: number;
    name: string;
    salary: number;
};
```

Usage

```ts
const emp: Employee = {
    id: 1,
    name: "Alex",
    salary: 50000
};
```

---

# 7. Interface

```ts
interface User {
    id: number;
    name: string;
}
```

Usage

```ts
const user: User = {
    id: 1,
    name: "John"
};
```

---

Difference

```ts
type User = {}
```

or

```ts
interface User {}
```

For React projects, both are common:

* `interface` → object shapes, component props
* `type` → unions, utility types, aliases

---

# 8. Union Types

```ts
let id: number | string;

id = 1;

id = "ABC";
```

Both are allowed.

---

# 9. Literal Types

```ts
type Status = "loading" | "success" | "error";

let state: Status = "loading";
```

Not allowed

```ts
state = "done";
```

---

# 10. Enum

```ts
enum Role {
    Admin,
    User,
    Guest
}

let role = Role.Admin;
```

String enum

```ts
enum Role {
    Admin = "ADMIN",
    User = "USER"
}
```

---

# 11. Tuples

Fixed length array.

```ts
let person: [string, number];

person = ["John", 25];
```

---

# 12. Type Assertions

```ts
let value: unknown = "Hello";

let str = value as string;

console.log(str.length);
```

---

# 13. Generics

Without Generics

```ts
function identity(value: any) {
    return value;
}
```

With Generics

```ts
function identity<T>(value: T): T {
    return value;
}
```

Usage

```ts
identity<number>(10);

identity<string>("Hello");
```

---

# 14. Arrays of Objects

```ts
type User = {
    id: number;
    name: string;
};

const users: User[] = [
    {
        id: 1,
        name: "John"
    },
    {
        id: 2,
        name: "Jane"
    }
];
```

---

# 15. Readonly

```ts
type User = {
    readonly id: number;
    name: string;
};

const user: User = {
    id: 1,
    name: "John"
};
```

Not allowed

```ts
user.id = 2;
```

---

# 16. Optional Properties

```ts
type User = {
    name: string;
    age?: number;
};
```

Usage

```ts
const user = {
    name: "John"
};
```

---

# 17. Record

```ts
type Scores = Record<string, number>;

const score: Scores = {
    math: 95,
    english: 90
};
```

---

# 18. Utility Types

## Partial

```ts
type User = {
    name: string;
    age: number;
};

type UpdateUser = Partial<User>;
```

Everything becomes optional.

---

## Pick

```ts
type UserName = Pick<User, "name">;
```

---

## Omit

```ts
type UserWithoutAge = Omit<User, "age">;
```

---

## Required

```ts
type RequiredUser = Required<User>;
```

---

## Readonly

```ts
type ReadOnlyUser = Readonly<User>;
```

---

# 19. Type Narrowing

```ts
function print(value: string | number) {
    if (typeof value === "string") {
        console.log(value.toUpperCase());
    } else {
        console.log(value.toFixed(2));
    }
}
```

---

# 20. Modules

Export

```ts
export const PI = 3.14;
```

Import

```ts
import { PI } from "./math";
```

---

# 21. Common React Types

Props

```tsx
type ButtonProps = {
    title: string;
};

function Button({ title }: ButtonProps) {
    return <button>{title}</button>;
}
```

State

```tsx
const [count, setCount] = useState<number>(0);
```

Input

```tsx
const [name, setName] = useState<string>("");
```

Event

```tsx
const handleChange = (
    e: React.ChangeEvent<HTMLInputElement>
) => {
    console.log(e.target.value);
};
```

---

# Mini Practice Project

Create a **Todo App** with:

* `Todo` type
* Array of todos
* Add todo
* Delete todo
* Mark complete
* Filter completed
* Use `useState<Todo[]>`
* Separate components with typed props

This single project will teach most of the TypeScript concepts you'll use in React.

## TypeScript roadmap for React developers

Learn these in order:

1. Basic types (`string`, `number`, `boolean`, arrays)
2. Functions and return types
3. Objects
4. `type` and `interface`
5. Optional properties
6. Union types
7. Type inference
8. Generics
9. Utility types (`Partial`, `Pick`, `Omit`, `Record`)
10. Type narrowing
11. React typings (props, state, events, refs)
12. Generic custom hooks and reusable components

Once you're comfortable with these topics, you'll be ready to build React applications using TypeScript with libraries like React Router, React Hook Form, Zod, TanStack Query, Zustand, and Axios.
