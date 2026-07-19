Forms are one of the most commonly asked topics in React interviews. The modern and recommended stack is:

* **React Hook Form (RHF)** → Form state management
* **Zod** → Validation schema
* **@hookform/resolvers** → Connects RHF with Zod

---

# 1. Why React Hook Form?

Without React Hook Form, you usually write a lot of state and validation code.

```jsx
const [email, setEmail] = useState("");
const [password, setPassword] = useState("");
```

With React Hook Form:

* Less code
* Better performance
* Easy validation
* Great TypeScript support
* Works well with Zod

---

# 2. Install

```bash
npm install react-hook-form zod @hookform/resolvers
```

---

# 3. Basic Form

```jsx
import { useForm } from "react-hook-form";

function App() {
  const { register, handleSubmit } = useForm();

  const onSubmit = (data) => {
    console.log(data);
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input
        {...register("name")}
        placeholder="Name"
      />

      <button type="submit">
        Submit
      </button>
    </form>
  );
}
```

If you enter

```
John
```

Console

```js
{
    name: "John"
}
```

---

# 4. register()

Connects an input to React Hook Form.

```jsx
<input {...register("email")} />
```

Equivalent data

```js
{
    email: "abc@gmail.com"
}
```

---

# 5. handleSubmit()

```jsx
<form onSubmit={handleSubmit(onSubmit)}>
```

Runs validation first.

If valid

↓

Calls

```jsx
onSubmit(data)
```

---

# 6. Getting Errors

```jsx
const {
    register,
    handleSubmit,
    formState: { errors }
} = useForm();
```

Display

```jsx
{
errors.email &&
<p>{errors.email.message}</p>
}
```

---

# 7. Built-in Validation

Required

```jsx
<input
    {...register("name", {
        required: "Name required"
    })}
/>
```

Minimum Length

```jsx
<input
{...register("password", {
minLength: {
value: 6,
message: "Minimum 6 characters"
}
})}
/>
```

Pattern

```jsx
<input
{...register("email", {
pattern: {
value:
 /^[^\s@]+@[^\s@]+\.[^\s@]+$/,
message:
"Invalid Email"
}
})}
/>
```

---

# 8. Why Zod?

Instead of validation inside every input

```jsx
register("email", {
required: true,
minLength: 5,
pattern: ...
})
```

Write validation once.

Much cleaner.

---

# 9. Create Zod Schema

```jsx
import { z } from "zod";

const schema = z.object({

name:

z.string()
.min(3, "Minimum 3 characters"),

email:

z.email("Invalid email"),

password:

z.string()
.min(6)

});
```

---

# 10. Connect Zod

```jsx
import {
zodResolver
} from "@hookform/resolvers/zod";
```

```jsx
const {

register,

handleSubmit,

formState: { errors }

} = useForm({

resolver:
zodResolver(schema)

});
```

Now Zod handles validation.

---

# 11. Complete Example

```jsx
import { useForm } from "react-hook-form";
import { z } from "zod";
import { zodResolver } from "@hookform/resolvers/zod";

const schema = z.object({
  name: z.string().min(3, "Minimum 3 characters"),
  email: z.email("Invalid email"),
  password: z.string().min(6, "Minimum 6 characters"),
});

function App() {
  const {
    register,
    handleSubmit,
    formState: { errors },
  } = useForm({
    resolver: zodResolver(schema),
  });

  const onSubmit = (data) => {
    console.log(data);
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input
        placeholder="Name"
        {...register("name")}
      />

      <p>{errors.name?.message}</p>

      <input
        placeholder="Email"
        {...register("email")}
      />

      <p>{errors.email?.message}</p>

      <input
        type="password"
        placeholder="Password"
        {...register("password")}
      />

      <p>{errors.password?.message}</p>

      <button>
        Submit
      </button>
    </form>
  );
}
```

---

# 12. Default Values

```jsx
const {
register
} = useForm({

defaultValues: {

name: "John",

email: "john@gmail.com"

}

});
```

Useful for Edit Forms.

---

# 13. Reset Form

```jsx
const {
reset
} = useForm();
```

```jsx
reset();
```

Reset with values

```jsx
reset({

name: "Alex",

email: "alex@gmail.com"

});
```

---

# 14. setValue()

```jsx
const {
setValue
} = useForm();
```

```jsx
setValue(
"email",
"abc@gmail.com"
);
```

---

# 15. watch()

Watch input changes.

```jsx
const {

watch

} = useForm();
```

```jsx
const email = watch("email");
```

Console

```
abc@gmail.com
```

---

# 16. getValues()

```jsx
const {

getValues

} = useForm();
```

```jsx
console.log(
getValues()
);
```

Output

```js
{
email: "...",
password: "..."
}
```

---

# 17. Controller

Some UI libraries (like shadcn/ui `Select`, Material UI, React Select, Date Picker) don't work directly with `register()` because they are controlled components.

Use `Controller`.

```jsx
import { Controller, useForm } from "react-hook-form";

const { control } = useForm();

<Controller
  name="country"
  control={control}
  render={({ field }) => (
    <select {...field}>
      <option value="">Select</option>
      <option value="india">India</option>
    </select>
  )}
/>;
```

---

# 18. Form Submission Flow

```
User Types

↓

register()

↓

React Hook Form

↓

Zod Validation

↓

Errors?

↓

Yes
Show Errors

↓

No

↓

onSubmit()

↓

API Call
```

---

# 19. Login Form Example

Schema

```jsx
const loginSchema = z.object({
  email: z.email("Invalid email"),
  password: z.string().min(6),
});
```

Submit

```jsx
const onSubmit = async (data) => {
  const res = await axios.post(
    "/login",
    data
  );
};
```

---

# 20. Registration Form

Fields

```
Name

Email

Password

Confirm Password
```

Schema

```jsx
const schema = z
.object({
  password: z.string().min(6),
  confirmPassword: z.string(),
})
.refine(
  (data) =>
    data.password === data.confirmPassword,
  {
    message: "Passwords don't match",
    path: ["confirmPassword"],
  }
);
```

---

# 21. React Hook Form Methods

| Method           | Purpose                |
| ---------------- | ---------------------- |
| register         | Connect input          |
| handleSubmit     | Submit form            |
| reset            | Reset form             |
| watch            | Watch values           |
| getValues        | Get values             |
| setValue         | Set value              |
| trigger          | Validate manually      |
| clearErrors      | Remove errors          |
| setError         | Set custom error       |
| control          | Used with `Controller` |
| formState.errors | Validation errors      |

---

# 22. Zod Validators

```jsx
z.string()

z.number()

z.boolean()

z.array()

z.object()

z.enum(["admin", "user"])

z.string().min(3)

z.string().max(20)

z.email()

z.url()

z.optional()

z.nullable()

z.default("Guest")
```

---

# 23. Real Project Flow

```
Login

↓

React Hook Form

↓

Zod Validation

↓

Axios POST

↓

Backend JWT

↓

Token

↓

localStorage

↓

Protected Routes

↓

Dashboard
```

---

# Interview Questions

* Why use React Hook Form instead of `useState`?
* What does `register()` do?
* What is `handleSubmit()`?
* What is `Controller` and when is it needed?
* Why use Zod instead of inline validation?
* What is `zodResolver()`?
* How do you validate password confirmation?
* Difference between `watch()` and `getValues()`?
* What are `setValue()`, `reset()`, `setError()`, and `clearErrors()` used for?
* How do you integrate React Hook Form with custom UI components like shadcn/ui `Select`?

---

# Mini Practice Project

Build a **Registration Form** with:

* Name
* Email
* Password
* Confirm Password
* Role (`Admin` / `User`)
* Country dropdown
* Terms & Conditions checkbox

Implement:

* React Hook Form
* Zod validation
* Password confirmation with `.refine()`
* Error messages below each field
* Reset button
* Submit button
* Simulated API call using Axios
* Success/error toast notification

This project covers nearly all the React Hook Form and Zod concepts you'll use in production React applications and frontend interviews.
