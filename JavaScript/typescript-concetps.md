# Difference between `any` and `unknown`

In TypeScript, the difference between `any` and `unknown` is all about **type safety** and how much trust the compiler gives you.

---

### 🧨 `any`: "I don't care what it is"
- You can assign **anything** to a variable of type `any`.
- You can then do **anything** with it—call methods, access properties, etc.
- TypeScript won’t check if what you’re doing makes sense.
- It’s like telling the compiler: “Trust me, I got this.”

```ts
let value: any = "hello";
value.toUpperCase(); // OK
value();             // Also OK (even if it's not a function!)
```

---

### 🛡️ `unknown`: "I don’t know what it is (yet)"
- You can assign **anything** to `unknown`, just like `any`.
- But you **can’t use** it until you **narrow** it to a specific type.
- It forces you to be explicit and safe.

```ts
let value: unknown = "hello";
value.toUpperCase(); // ❌ Error: Object is of type 'unknown'

if (typeof value === "string") {
  console.log(value.toUpperCase()); // ✅ Safe now
}
```

---

### 🧠 Mental model:
- `any` = “_I don’t care_ what this is.”
- `unknown` = “_I don’t know_ what this is, so I’ll check first.”

---

In short: **`unknown` is the type-safe version of `any`**. If you're writing robust, maintainable code, `unknown` is usually the better choice.

------------------

# Diff between `interface` and `type`

In TypeScript, both `interface` and `type` let you describe the shape of data—but they have some subtle and not-so-subtle differences. Here's a quick breakdown:

---

### 🧱 1. **Use Case & Flexibility**
- **`interface`**: Best for defining the shape of **objects and classes**.
- **`type`**: More flexible—you can define **primitives, unions, intersections, tuples**, and more.

```ts
type ID = string | number; // ✅ valid
interface ID = string | number; // ❌ invalid
```

---

### 🔁 2. **Extending & Composition**
- Both can be extended, but the syntax differs:
  - `interface` uses `extends`
  - `type` uses intersections (`&`)

```ts
interface A { x: number }
interface B extends A { y: number }

type C = { x: number }
type D = C & { y: number }
```

---

### 🧬 3. **Declaration Merging**
- **Only `interface`** supports declaration merging—multiple declarations with the same name are merged.

```ts
interface User { name: string }
interface User { age: number }
// Result: User has both name and age
```

- `type` will throw an error if you try to redefine it.

---

### 🧪 4. **Function & Tuple Types**
- `type` is preferred for **function signatures** and **tuples**.

```ts
type Greet = (name: string) => void;
type Point = [number, number];
```

---

### 🧠 TL;DR
| Feature                  | `interface`         | `type`                     |
|--------------------------|---------------------|-----------------------------|
| Object shape             | ✅ Yes              | ✅ Yes                      |
| Primitives, unions, etc. | ❌ No               | ✅ Yes                      |
| Declaration merging      | ✅ Yes              | ❌ No                       |
| Extending                | ✅ `extends`        | ✅ `&` (intersection)       |
| Function types           | 😐 Verbose          | ✅ Cleaner syntax           |
| Preferred for classes    | ✅ Yes              | 😐 Possible but uncommon    |

---

If you're defining an object or class structure, `interface` is often the go-to. But for everything else—especially when you need flexibility—`type` is your friend.

-----

# How JS Understand union type


In short: **TypeScript understands unions, JavaScript doesn’t — it just runs the final, type-erased code**.

```ts
function printId(id: string | number) {
  console.log(id);
}
```

You're telling **TypeScript** that `id` can be either a `string` or a `number`. But once you compile this code to JavaScript, the type information is **stripped away** — JavaScript just sees:

```js
function printId(id) {
  console.log(id);
}
```

So how does it work?

### 🧠 TypeScript handles unions at compile time:
- It checks your code and ensures you **narrow** the type before using it.
- For example, you might use `typeof` to check the type:

```ts
if (typeof id === "string") {
  console.log(id.toUpperCase()); // Safe
} else {
  console.log(id.toFixed(2));    // Also safe
}
```


-------

# Abstract Classes 

```ts
// Abstract base class
abstract class Animal {
  constructor(public name: string) {}

  // Abstract method — must be implemented by subclasses
  abstract makeSound(): void;

  // Normal method — can be inherited
  move(): void {
    console.log(`${this.name} moves gracefully.`);
  }
}

// Subclass 1
class Dog extends Animal {
  makeSound(): void {
    console.log(`${this.name} says: Woof!`);
  }
}

// Subclass 2
class Cat extends Animal {
  makeSound(): void {
    console.log(`${this.name} says: Meow!`);
  }
}

// Usage
const myDog = new Dog("Buddy");
myDog.makeSound(); // Buddy says: Woof!
myDog.move();      // Buddy moves gracefully.

const myCat = new Cat("Luna");
myCat.makeSound(); // Luna says: Meow!
myCat.move();      // Luna moves gracefully.
```

### 🧠 Key Takeaways:
- You **can't instantiate** an abstract class directly.
- Subclasses **must implement** all abstract methods.
- Abstract classes can have both abstract and normal methods.

Nope — in TypeScript, you **cannot create an object directly from an abstract class**. That’s the whole point of marking a class as `abstract`: it’s meant to serve as a blueprint, not a finished product.

If you try something like this:

```ts
abstract class Animal {
  abstract makeSound(): void;
}

const pet = new Animal(); // ❌ Error: Cannot create an instance of an abstract class.
```
----------
# Type assertions

Type assertions in TypeScript are like telling the compiler, “Hey, I know more about this value’s type than you do — trust me.”

A **type assertion** lets you manually specify the type of a value when TypeScript can’t infer it correctly. It doesn’t change the value at runtime — it just tells the compiler how to treat it during type checking.

You can write it in two ways:
```ts
const value = someVar as string;      // Preferred
const value = <string>someVar;        // Also valid (except in JSX)
```

---

### 🛠️ When Should You Use It?

1. **Working with `any` or `unknown`**
   ```ts
   const data: any = "hello";
   const length = (data as string).length;
   ```

2. **Accessing DOM elements**
   ```ts
   const input = document.querySelector('input') as HTMLInputElement;
   console.log(input.value);
   ```

3. **Narrowing union types**
   ```ts
   function handle(id: string | number) {
     if (typeof id === "string") {
       const upper = (id as string).toUpperCase();
     }
   }
   ```

4. **Using third-party libraries or dynamic data**
   When TypeScript doesn’t know the shape of the data (like from an API), you can assert it:
   ```ts
   const user = response as { name: string; age: number };
   ```

---

# Literal Types

In TypeScript, **literal types** let you specify the *exact* value a variable can hold — not just the general type like `string` or `number`, but a specific one like `"success"` or `42`.

---

### 🔤 Types of Literal Types

1. **String Literal Types**
   ```ts
   type Direction = "up" | "down" | "left" | "right";
   let move: Direction;
   move = "up";     // ✅ OK
   move = "forward"; // ❌ Error

    let x: "hello" = "hello";
    // OK
    x = "hello";
    // ...
    x = "howdy";
    Type '"howdy"' is not assignable to type '"hello"'.
    //Here variable x is also behaving like const because we cannot assign anyother value instead of hello.

   ```

2. **Numeric Literal Types**
   ```ts
   type DiceRoll = 1 | 2 | 3 | 4 | 5 | 6;
   let roll: DiceRoll = 4; // ✅ OK
   ```

3. **Const Literal Types**
   ```ts
   const constantString = "Hello World";
      - const constantString: "Hello World"
    //Because constantString can only represent 1 possible string, it has a literal type representation If you hover on constantString variable you will see.
   ```

---

### 🧠 Why Use Literal Types?

- **Restrict values** to a known set (like enums)
- **Improve type safety** and catch errors at compile time
- **Guide control flow** with discriminated unions

---

### 🧪 Real-World Example

```ts
type Status = "loading" | "success" | "error";

function showStatus(status: Status) {
  if (status === "success") {
    console.log("All good!");
  }
}
```

Only `"loading"`, `"success"`, or `"error"` are allowed — anything else will throw a compile-time error.

---------

# typeof null

```ts
    function print(strs: string | string[] | null) {
        if(typeof strs === 'object') {
            for(const s of strs) {
                //We are checking type to loop over string[] But this will give error -> 'strs' is possibly 'null'.
                // typeof null is also object. thats why it giving error.
            }
        } else if (typeof str === 'string'){
            //
        } else {
            //
        }
    }
```

**typeof null is actually "object"! This is one of those unfortunate accidents of history.**

------

# 🧱 What Are Static Members?

In TypeScript, **static members** are properties or methods that belong to the **class itself**, not to instances of the class.


```ts
class MathUtils {
  static PI = 3.14;

  static square(x: number): number {
    return x * x;
  }
}

console.log(MathUtils.PI);         // ✅ Access without creating an object
console.log(MathUtils.square(5));  // ✅ 25
```

- You **don’t need to instantiate** the class to use static members.
- They’re accessed via `ClassName.member`, not `this.member`.
---

### ✅ When Static Members *Are* Useful

- Utility/helper functions (e.g. `Math.abs()`)
- Shared constants (`AppConfig.DEFAULT_TIMEOUT`)
- Factory methods (`User.createFromJson()`)

---

# `Partial<Type>` and `Required<Type>`

In TypeScript, `Partial<Type>` and `Required<Type>` are **utility types** that help you transform existing types by adjusting the *optionality* of their properties.

---

### 🧩 `Partial<Type>`

This makes **all properties optional**.

```ts
interface User {
  name: string;
  age: number;
}

const updateUser = (user: Partial<User>) => {
  // user.name and user.age are optional here because of Partial utility type.
  // Without Partial type, you must need to provide name and age both.
};
```

✅ Use it when:
- You’re updating part of an object (e.g. patch APIs)
- You want to allow incomplete data

---

### 🔒 `Required<Type>`

This makes **all properties required**, even if they were optional in the original type.

```ts
interface User {
  name?: string;
  age?: number;
}

const createUser = (user: Required<User>) => {
  // user.name and user.age must be provided because of Required Type while in origional interface they are optional.
};
```

✅ Use it when:
- You want to enforce that all fields are present
- You're validating or finalizing an object

---

# Generics

In TypeScript, **generics** are a way to create reusable components that work with **any data type**, while still maintaining **type safety**.

---

### 🧠 Why Use Generics?

Without generics, you'd either:
- Use `any` (which loses type safety), or
- Write multiple versions of the same function for different types

Generics let you write **one version** that works for all types — and TypeScript will still check types for you.

---

### 🧪 Basic Example: Identity Function

```ts
function identity<T>(value: T): T {
  return value;
}

const num = identity<number>(42);       // T is number
const str = identity<string>("hello");  // T is string
```

- `T` is a **type variable** — a placeholder for the actual type
- TypeScript infers the type when you call the function

---

### 📦 Generic Function with Arrays

```ts
function firstElement<T>(arr: T[]): T {
  return arr[0];
}

const first = firstElement<string>(["a", "b", "c"]); // "a"
```

---

### 🧱 Generic Interface

```ts
interface Box<T> {
  value: T;
}

const stringBox: Box<string> = { value: "hello" };
const numberBox: Box<number> = { value: 123 };
```

---

### 🏗️ Generic Class

```ts
class Container<T> {
  private content: T;

  constructor(value: T) {
    this.content = value;
  }

  getContent(): T {
    return this.content;
  }
}

const c = new Container<boolean>(true);
```

---

### 🔒 With Constraints

You can restrict what types are allowed:

```ts
function logLength<T extends { length: number }>(item: T): void {
  console.log(item.length);
}

logLength("hello");       // ✅
logLength([1, 2, 3]);      // ✅
logLength(123);            // ❌ Error: number has no length
```

---

Generics are everywhere in TypeScript — from `Array<T>` to `Promise<T>` to `Map<K, V>`.

-------------

# 🧠 What is Type Inference?

**Type inference** means **TypeScript figures out the type for you**, even if you didn’t explicitly annotate it.

You write less code 🧑‍💻, and TypeScript still gives you full type safety ✅.

---

### 🔍 Basic Example
```ts
let message = "Hello, world!";
```
- TypeScript infers that `message` is of type `string` — because `"Hello, world!"` is a string literal.

```ts
let count = 10; // inferred as number
```

---

### 🛠️ Function Inference
```ts
function add(a: number, b: number) {
  return a + b;
}
// TypeScript infers the return type as number
```

Even without explicitly writing `: number`, TypeScript knows what’s coming back.

---

### 🪄 Array Inference
```ts
let colors = ["red", "green", "blue"];
// inferred as string[]
```

---

### 🧩 Contextual Typing
When you pass a function as an argument:
```ts
const names = ["Alice", "Bob"];
names.forEach(name => {
  // name is inferred as string
  console.log(name.toUpperCase());
});
```

---

### ⚠️ When to Be Explicit
TypeScript’s inference is smart, but for:
- **APIs**
- **Public libraries**
- **Complex logic**

…it's better to write explicit types for clarity, maintainability, and readability.

---

### ✅ `let num = 5;`  `let num: number = 5;`

### 🧠 So, which is more accurate?

If the question is about **TypeScript best practices or accuracy**, then:

> ✅ **`let num = 5;` is more accurate** — because TypeScript infers the type as `number` anyway, and there's no need to repeat yourself.

------------