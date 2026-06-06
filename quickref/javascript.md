# Javascript fundamentals

Quick reference: https://quickref.me/javascript.html

**JS:** [vars](#vars) · [conditionals](#conditionals) · [Objects](#objects) · [Classes](#classes) · [Prototypes](#prototypes) · [Loops](#loops) · [Arrays](#arrays) · [Errors](#errors) · [Sets](#sets) · [Maps](#maps) · [Promises](#promises) · [Event loop](#the-event-loop) · [Runtimes](#runtimes) · [Modules](#modules)

**TypeScript:** [Types](#types) · [Unions](#unions) · [Arrays (TS)](#arrays-1) · [Objects (TS)](#objects-1) · [Classes (TS)](#classes-1) · [Generics (TS)](#generics) · [Local dev](#local-development)

---

## vars
- basic types are `boolean` `number` `string` and `undefined`. Number is both int and float, undefined is something special
- declaring using `var` does function-scoped variables (or global if not in a function). `let` is for variables that can change `const` is for constants. Those are both block-scoped
- ++ and += works in javascript
- javascript has two values for none. `undefined` is something that does not exist / the default value of a variable. `null` has to be set. I think null is more rare? Basically it means empty where undefined means does not exist
- javascript is dynamically typed and weakly typed so it does some implicit type conversions (like if you add a string a number it will concat or actually add depending on some factors. Basically just look out for this)
- can do same line declarations of variables, just do comma in between
- has garbage collection, runs single threaded by default, and is JIT compiled
- `template literals` are basically javascript version of f strings. Uses backticks for the string and ${} for variables
- camelCase is usually used in javascript when writing variables

## conditionals
- if, else, else if all supported. Parentheses around the conditions
- `==` compares equality loosely, it may do implicit conversion. `===` does exact equality, so in most cases should probably do this
- &&, ||, ! for and, or, not 
- switch statements don't have fallthrough by default, need break/return statement in your cases if you want them to be exclusive
- has ternary operations for if statements
- true/false kind of behaves like python where you can treat empty strings like false, variables that exist as true, etc.
- `??` operator checks for undefined or null and can return a default if so. Very scientific name of `nullish coalescing` https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Nullish_coalescing


## Objects
- `POJO Plain old javascript objects` are used a lot like dictionaries or maps. But they can also be used for things like classes or prototypes
- if you use a variables in an object you don't need the colon. It just creates a map of the variable name to its value
- the `.` operator is used to access values and also set values.
- you can nest objects / properties
- `?.` is an optional chaining operator, returns undefined instead of throwing an error if you try to chain on a property doesn't exist
- you can define methods in objects as well as properties
- `this` kindof behaves like `self` in python
- `const` object does not mean contents of the object can't be modified
- bracket notation object["thing"] is used for when you want to grab properties dynamically
- `this` refers to the context where a piece of code is executed. So you can use it anywhere not just in an object. For example in the browser it returns the Window context
- there's a few different ways to declare functions:
```js
// declaring a function without a variable
function add(x, y) {
  return x + y;
}

// declaring a function with a variable
const add = function (x, y) {
  return x + y;
};

// using the fat arrow syntax
const add = (x, y) => {
  return x + y;
};
```
- fat arrow syntax inherits the `this` scope of its parent. So can be useful to take in the global state, but has weird behavior when used as an object method
- spread syntax can shallow copy object properties, I think kind of like * in python?
- you can only return one value from a function, so objects can be used to return multiple things
- `destructuring` can be used to unpack objects more cleanly into separate variables. This can be used anywhere it seems like (in function inputs, outputs, etc.)
- methods are not bound to their objects by default:
```js
const user = {
  name: "Lane",
  greet() {
    console.log(`Hi, ${this.name}`);
  },
};

const greet = user.greet;
greet(); // 'this' is wrong — need bind

const boundGreet = user.greet.bind(user);
boundGreet(); // works
```
- have to use `bind` on the method to bind it to an object

## Classes
- javascript has classes:
```js
class User {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }
}

const user = new User("Lane", 100);
```
- all properties/methods of a class are public by default
- can use `#` prefix to make something private. Can also enforce encapsulation by not publishing/importing things elsewhere if you want them to be private
- classes are just object templates
- `static` methods are bound to the class itself not instances of it
- unlike python where you can still access static methods from instances of classes, in JS you can only access from the class object itself not instances. And it's global across all instances, so you could track how many class instances are out there with a static method for example
- getters and setters are special methods in classes for getting and setting values of properties. Outside the class you access them like normal properties but under the hood it calls the methods
- you can do class inheritance with `extends` keyword. Something like `class BeastTitan extends Titan`
- can use `super` like python

## Prototypes
- this is the underlying inheritance mechanism in JS. You can define child objects from object prototypes and do overrides and inheritance and everything
- basically when you call a method or property on an object it first checks if that object has it, if it doesn't it goes up the prototype chain. So to "inherit" the parent methods and properties you just don't override them

## Loops
- has for, while loops. Has c-style syntax
- has break and continue syntax
- has a for...in loop too for iterating over objects
    - kind of a confusing order is used here but it will be consistent "Within each component of the prototype chain, all non-negative integer keys (those that can be array indices) will be traversed first in ascending order by value, then other string keys in ascending chronological order of property creation."

## Arrays
- arrays are like python lists. No requirement to have elements of the same type
- `spread` syntax works as well to combine arrays `const newNums = [...nums, 4, 5, 6];`
- they have `For...of` syntax for looping through elements, not the same as for...in for looping through keys which is annoying
- `.slice()` is for getting slices of arrays. Can use negative indexing here but can't with regular array access []
- you can destructure like with objects, and can combine that with spread operator to store a bunch of items
```js
const [x, ...theRestOfThem] = double(nums);
console.log(x);
// 2
console.log(theRestOfThem);
// [4, 6]
```

## Errors
- Errors are a core part of javascript, they have a message property that says in human readable format what's wrong
- `try/catch` blocks are like try except blocks
- `finally` runs regardless of what happens with try/except
- throwing errors yourself is done with `throw new Error("something went wrong");`. You don't need to throw error objects but it helps with structure

## Sets
- there's sets in javascript, basically work like python. Have set operations like union/intersection/etc.


## Maps
- maps are like python dicts / hash sets
- you can basically use anything as a key, but should probably use strings. If you use something like an array the key is actually the reference to the array not the values so you'd need to use the same reference in the future to access
- you can use objects instead of maps, but maps have benefits of having consistent and sane ordering, they're lower memory footprint because they don't have constuctor or proto methods, and they're easier to iterate through. They also have faster lookup, insertion, and deletion

## Promises
- promises are how async is done in javascript. A promise can either me resolved or rejected.
- `then` and `catch` are methods you implement on a promise, then runs if it works, catch works if there's an issue (the promise rejects)
- `await` is the more modern version of then, so you can just kindof call it like python. If the promise rejects it throws an error so you can use a try catch block:
```js
try {
  const message = await promise;
  console.log(`Resolved with ${message}`);
} catch (error) {
  console.log(`Rejected with ${error}`);
}
```
- for creating new promises with functions you can use async instead of the new promise syntax:
```js
// with new promise syntax
function getPromiseForUserData() {
  return new Promise((resolve) => {
    fetchDataFromServer().then(function (user) {
      resolve(user);
    });
  });
}

const promise = getPromiseForUserData();

// with async syntax, much cleaner
async function getPromiseForUserData() {
  const user = await fetchDataFromServer();
  return user;
}

const promise = getPromiseForUserData();
```
- don't need to really use then/catch. Can just use async/await for the most part

## The event loop
- there's only one thread for the runtime. JS is single threaded
- the main thread cannot be blocked by async tasks
- the results of async tasks are pushed into the task queue
- async callbacks are pushed into the task queue by external APIs
- basically functions are being run on the `call stack`. Functions get added and popped from there when they return
- the `task queue` is where async functions get added. When the call stack is empty then something from the task queue is pushed to the call stack
- `microtask queue` is used for smaller operations than the task queue, but similarly is for scheduling tasks to execute later. For example the .then and .catch methods for promises use this
- There are two important differences between the task queue and the microtask queue:
    - Order of Execution: All microtasks are executed before the next task in the task queue.
    - Addition of Microtasks: Microtasks can add more microtasks to the queue, and those will still execute before the next "macro" task.


## Runtimes
- for web dev it's the browser (or a web worker within a browser)
- for backend there's different options like node.js (most popular), or bun, or others.
    - idk if this is actually true. Like is bun useless for frontend dev?
- the runtime determines what APIs are available, how the code executes, and what dependencies are needed in production
- ECMAScript is the standard that defines javascript across all the runtimes
- `polyfill` is an extra bit of code that helps to ensure compatability across runtimes (especially older browsers)
- `transpilers` is something that takes your entire code and converts it into an older version of javascript or something else

## Modules
- modules in JS are file based like python. Looks something like this in commonJS which is a node.js standard before ES6 was released:
```js
// math.js
const add = (a, b) => a + b;
const subtract = (a, b) => a - b;

module.exports = {
  add,
  subtract,
};

// importing from another file looks like this:
// main.js
const { add, subtract } = require("./math.js");

console.log(add(1, 2)); // 3
console.log(subtract(1, 2)); // -1
```
- ES6 modules are modern but not really default in a lot of places. It enforces strict mode by default and has a little bit of simpler syntax for exporting/importing:
```js
// math.js
export const add = (a, b) => a + b;
export const subtract = (a, b) => a - b;

// main.js
import { add, subtract } from "./math.js";

console.log(add(1, 2)); // 3
console.log(subtract(1, 2)); // -1
```
- to use ES6 modules in the browser you need to specify it:
```html
<script type="module" src="math.js"></script>
<script type="module" src="main.js"></script>
```
- default exports can be used if you just want to export one thing from a module
- `bundlers` are used to take your code as you wrote it and optimize it for production. A lot of the elements that are involved can be:
  - Bundling: Duh, we just covered this.
  - Minification: Making your code smaller by removing unnecessary whitespace (useful when you have to serve your code to a browser via a slow connection).
  - Code Splitting: Breaking your code into smaller chunks that can be loaded on-demand.
  - Tree Shaking: Removing unused code from your final bundle.
  - Asset Optimization: Optimizing images, fonts, and other assets. For example, converting images to WebP format or reducing their size.
  - Source Maps: building files that allow you to debug your code in the browser as if it were still in its original form.
- `vite` is a common bundler these days
  - can use vite build with a vite.config.js to define how you want to build things
  - then can run the resulting script with node

---

# TypeScript

## Types
- Typescript allows for typing in javascript
- you don't need to explicitly declare types like mypy in python, typescript can infer
- it's a `superset` of javascript. All javascript code is valid typescript code. Not all typescript code is valid javascript
- typescript is basically just a compiler that converts typescript to javascript. It is currently written in typescript, but there's a port to Go currently underway which is achieving 10x speedups. Could also just be thought of as an advanced linter
- basically to convert javascript to typescript you just change the file extension from .js to .ts and get tsc compiler running without errors. It will treat variables as `any` type most likely. Then you can add more strict typing as you need
- type annotations can be used in function signatures on parameters and the return as well
- `void` in typescript indicates no return value
- `function types` in typescript are a combination of parameter and return types: `(param1: type1, param2: type2, ...) => returnType`
- can create custom types with the `type` syntax: `type LoggerCallback = (s1: string, s2: string) => string;`
  - this can be used when you're making higher order functions or something
- importing types
  - a couple different syntaxes here. The point is this tells the compiler not to worry about it when converting to javascript
  - `import type { User, Post } from "./models";`
  - `import { type User, type Post } from "./models";`

## Unions
- typescript supports union types like `number | string`
- you can specify optional params on a function with `?` like `title?: string`. This has an undefined type unioned onto it autmatically. Optional params have to come after required params
- can use default parameters in functions like python, those don't need to be specified as optional and don't need to include type annotations since it can be inferred
- `literal types` basically let you assign a single acceptable value for a type for something like numbers or strings. This is not super useful on its own but when combined with unions you can essentially make a lightweight enum:
```ts
type Direction = "north" | "south" | "east" | "west";

function move(direction: Direction) {
  // Implementation...
}
```
- `template literal types` allow you to take this idea and enforce certain templates or more easily expand options like below:
```ts
type Class = "wizard" | "warrior" | "rogue";
type Race = "elf" | "human" | "dwarf";
type Hero = `Hero: ${Race} ${Class}`;
// Hero: elf wizard | Hero: elf warrior | Hero: elf rogue | Hero: human wizard | Hero: human warrior | Hero: human rogue | Hero: dwarf wizard | Hero: dwarf warrior | Hero: dwarf rogue

type logRecord = `${string}: ${number}`;

// this is valid because it's a string followed by a colon and a number
const criticalErr: logRecord = "CRITICAL: 69";

// these are all invalid
const criticalErr: logRecord = "CRITICAL 92";
const criticalErr: logRecord = "CRITICAL: 92a";
const criticalErr: logRecord = "92: CRITICAL";
```
- don't go crazy with this. Typescript will enforce some limitations on number of type combinations in giant unions. It will also slow compilation time a lot

## Arrays
- a couple of ways to declare arrays:
```ts
// Using bracket notation
function assignLightsaberColors(name: string, colors: string[]): void {
  // ...
}
// Using generic type parameter syntax
function assignLightsaberColors(name: string, colors: Array<string>): void {
  // ...
}
```
- for heterogenous arrays you can use a union type `(string | number)[]`
- typescript has a `rest parameter` which allows a variable number of args to be put into a parameter. Kind of looks like the spread syntax but is not quite the same. basically the same as `*args` in python
- `evolving any` type works when you don't declare a type for an array. In its scope like in a function you can insert anything and it auto creates a union type, but then when it's passed outside that scope it will enforce the types it has which is inferred from the values

## Objects
- Object literal types are also a thing. Basically like pydantic models?
- usually when you pass an object to a function who expects a certain object literal you can pass extra params. But if you try to pass an object literal it will do excess param checking
  - you can modify that in your `tsconfig.json`
- you can specify optional properties with `?` like function params but use them sparingly
- empty object types cannot have values added to them, but you can reassign the variable to something else since basically everything in javascript is an object
- for objects when you use a union type of two objects it's hard to tell what you're working with, so you can tag the underlying objects to help there. This is called `discriminated unions`
```ts
type MultipleChoiceLesson = {
  kind: "multiple-choice"; // Discriminant property
  question: string;
  studentAnswer: string;
  correctAnswer: string;
};

type CodingLesson = {
  kind: "coding"; // Discriminant property
  studentCode: string;
  solutionCode: string;
};

type Lesson = MultipleChoiceLesson | CodingLesson;

function isCorrect(lesson: Lesson): boolean {
  switch (lesson.kind) {
    case "multiple-choice":
      return lesson.studentAnswer === lesson.correctAnswer;
    case "coding":
      return lesson.studentCode === lesson.solutionCode;
  }
}
```
  - `kind` does not have to be the tag used, but it's convention
- typescript also has sets and maps. Can use <> syntax to specify what types can exist in there
- can define dynamic keys like this, This type says "this object can have any number of properties if the keys are strings and the values are numbers.":
```ts
type UserMetrics = {
  [key: string]: number;
};
```
- can still specify required fields there. So you can specified your required fields while having flexibility for extra ones
- `propertykey` is a built in typescript type that you can use as keys. It's strings, numbers, and something called symbols which is kind of like a string
- `readonly` is something you can set a property as that functions kind of similar to const. It doesn't let the value be changed
- `as const` allows you to basically create a deep readonly object including all nested objects. `Object.freeze()` is a js builtin and prevents top level modifications at runtime but doesn't affect nested objects
- there are some odd things that happen if you have a type that has union types and want to create an object of that type but have it be more narrow. Like if you have an object with a string but the type you're using is string | number you can't do string operations on it. So you can use something called `satisfies` to make sure it works but not actually adopt that type:
```ts
type ColorMap = {
  red: string | number;
  green: string | number;
  blue: string | number;
  yellow: string | number;
};

const colorsSatisfies = {
  red: "#ff0000",
  green: "#00ff00",
  blue: 255,
  yellow: "#ffff00",
  // Error: "yelow" is not in type ColorMap
  // yelow: "#ffff00"
} satisfies ColorMap;

// We keep the narrowed types!
type RedHexSatisfies = typeof colorsSatisfies.red;
const redUpper = colorsSatisfies.red.toUpperCase(); // "#FF0000"
```
- you can define `function overloads` to specify certain ways you can call a function:
```ts
// note: function overloads need to be declared above the implementation
function formatEmployeeMessage(employee: Employee): string;
function formatEmployeeMessage(
  employee: Employee,
  isNew: true,
  onBoardedDate: Date,
): string;
```
- `tuples` in typescript are special arrays where you specify the types of each element
  - tuples are a typescript concept so they can enforce things at compile time but not at runtime. Since they're arrays under the hood you can still push and pop from them so if you want them to behave like immutable objects use const and readonly
  ```ts
  const nameAndAge: readonly [string, number] = ["Martha Jones", 24];
  // Error: Property 'push' does not exist on type 'readonly [string, number]'.
  nameAndAge.push("Donna Noble");
  ```
  - named tuples exist for documentation, it doesn't really change anything about access or behaviors
  - can also have optional elements in tuples which are just unioned with undefined types
  - can also use rest elements for variable length amount of a type

## Intersections
- Uses the `&` operator. Intersection types just merge all the components of each type, so if you have 2 types each with 2 properties the resulting type needs to have all of them
- `never` type is a type that can't occur. Useful if you want to ensure you've checked every possible value from a type literal
- if you do intersections on types that have the same properties they will merge if it's possible, like if both are strings. But if not it will set the whole type to never and complain
- type unions will basically mean you need to satisfy either one type or the other. Intersection means you need to satisfy both
- `super set unions` is a bit of a hack if you want to allow the most common values and have them show up in autocomplete, but still allow other values:
```ts
type ErrorCodes = "OK" | "NOT_FOUND" | "INTERNAL_ERROR" | (string & {});
```

## Interfaces
- interfaces are very similar to types. They are a bit better when it comes to extending other types/interfaces with the `extends` syntax vs intersection. It creates a flat object and compilation time / bug tracking is a bit easier
- you can extend multiple interfaces at once
- you can override interface properties but they need to be compatible with the original interface
- so even though there's this nice property, one quirk is that if you declare and redelcare an interface it merges all those together which is probably not what you want. If you did the same thing with types you'd get an error about type redeclaration

## Enums
- defined constants. Enums if you don't specify the values will just automatically have values underneath that are incrementing numbers
- you can hardcode values. Numeric enums are bidirectional so you can use the underlying value to get the name and the name to get the value
- as always have to worry about database and json serialization of enums so you don't get confusing values
- const enums are more performant because it doesn't create the bidirectional mapping. You can only access them with the string literal or enum value though so you can't really get the enum value.
  - also can't dynamically create values in the enum, you could only create static values using other fields in the enum
- enums give you a little extra functionality over unions but can also create some headaches so use them wisely

## Type Narrowing
- there's an automated handling when types narrow in specific branches of code in the typescript compiler
- `unknown` is basically an alternative to `any` that forces you to typecheck before you do something with it, whereas `any` basically just turns off type checking. So unknown is safer, especially for IO boundaries
- basically there's a hierarchy in typescript of types, and as you "narrow" types by defining more about them it limits your ability to assign things to that type. I.E. you can always assign a "hello" to a string or "hello" | "there" but cannot assign "hello" | "there" to "hello"
- `in` operator can check for specific properties in a type to help you narrow down what you're working with. Discriminated unions are preferred if you have control
- can define type predicates (just functions that check for a type with custom logic) that returns a `value is Type`
- using union literals you can define `exhaustive checks` where you don't need an "else" statement in your checks. Typescript will give you an error if you try to add that because it's unreachable code
- `type assertions` are possible with a couple different syntaxes
```ts
// as keyword
const userId = (route.query?.userId as string).toLowerCase();

// angle brackets
const userIdRaw = <string>route.query?.userId;
const userId = userIdRaw.toLowerCase();
```
- you can double assertions by asserting to unknown first and then to your type if you really need to get around typescript complaining. But its a bad idea, it will probably crash at runtime
- you can do non-null assertions with `!` after the variable. Like other assertions you need to be absolutely sure here and it's risky. The safer solution is to handle the edge cases. Here's some examples:
```ts
// assume getCleanedText returns a string | null
import { getCleanedText, sendText } from "./text-utils";

const cleanedText = getCleanedText("some text");
// cleanedText is string | null
// but we know that it's not null because we passed in a valid string

// sendText expects a string, so we use a non-null assertion
sendText(cleanedText!);

// safer alternative
function sendTextSafely(text: string | null) {
  if (text === null) {
    throw new Error("Text is required");
  }
  sendText(text);
}
```


## Classes
- basically the same as javascript but gives you typing and can giving you compilation errors with things like trying to access private class members outside the class
- to create private members you use `#` in javascript, but typescript added `private` syntax before this was added to javascript. `#` should be used now that javascript has it almost always
- `protected` is a typescript-specific syntax for if you want something accessible by a class and subclasses but not outside those. No JS equivalent so this is something to potentially use
- `abstract` classes can't be instantiated directly but are basically templates to be inherited and extended. you use syntax like `class Circle extends Shape`
- to make sure your class has all the methods and attributes of interfaces you can use `implements` keyword like `class ElectricCar implements Vehicle, Drivable` and it will throw an error if you don't have everything
- types and interfaces and classes are all kinda similar. It seems like types are really all you need or interfaces, classes aren't really necessary for most use cases
- examples of class-specific features (things interfaces and types can't do):
  - Have private, protected, static, and abstract members
  - Have dedicated constructors
  - Have method implementations predefined on all instances
- you can explicitly control the type of `this` in typescript:
```ts
class Counter {
  private count: number = 0;

  increment(this: Counter, n: number): void {
    // 'this' is explicitly typed as Counter
    // the `this` parameter is not available at runtime
    // it is only used for type checking
    this.count += n;
  }

  getCount(this: Counter): number {
    // 'this' is explicitly typed as Counter
    return this.count;
  }
}
```
- you can declare parameters directly in a class constructor for convenience but you can only use private and public for them, not `#`
```ts
class Hero {
  constructor(
    public name: string,
    public health: number,
    private level: number,
  ) {}
}
```

## Utility types
- try to avoid making a bunch of similar types without any relation to eachother like User and UserWithoutId. Just extend UserWithoutId to User
- you can use `Partial` to make all the top level fields of a type optional like `Partial<User>`. This helps not need to make explicit new types
- `Required` is basically the opposite, forces all fields to be required `Required<T>`
- `Readonly` is another utlity type making all top level properties readonly
- `Record` is a utility type that basically can enforce a dictionary schema. Useful for a lookup table to ensure you defined all the values (like http errors or something)
- `Pick` lets you take only some properties from a type like `type ProductSummary = Pick<Product, "id" | "name" | "price">;`
- `Omit` is the opposite of pick

## Generics
- Generics are a way to handle many types but still maintain useful type info (unlike Any)
- for example for a stack or queue you don't want to have to define different ones for every type.
- specified like `Queue<T>`
- you can define many types in a generic, like `function transform<InputType, OutputType>` and the values can be anything, but typically T,U,V are the most common to keep signatures short
- you can restrict this a bit by using interfaces, requiring types to have certain properties:
```ts
interface HasCost {
  cost: number;
}

function applyDiscount<T extends HasCost>(vals: T[], discount: number): T[] {
  const arr: T[] = [];
  for (const val of vals) {
    val.cost *= discount;
    arr.push(val);
  }
  return arr;
}
```
- creating generic types looks like this:
```ts
interface Store<T> {
  get(id: string): T;
  save(id: string, item: T): void;
  list(): T[];
}
// also works with type aliases using
// type Store<T> = { ... }
```
- when calling generic functions you usually don't have to pass the <T> param with your actual type because typescript can infer from what variabls you're passing
- classes can be generic too


## Conditional Types
- Typescript can define types conditionally. This seems kindof confusing and hacky but I'm sure has some uses 
```ts
type NewType = SomeType extends OtherType ? TrueType : FalseType;
```
- `infer` is something that lets you use the type from the true branch in the conditional. Again idk how exactly this is useful but probably is
- `mapped types` are ways to dynamically create new types based on values of existing types. Something like:
```ts
type StringifiedSoldier = {
  [K in keyof Soldier]: string;
};
```
- you can combine these to make some wild conditionally mapped types:
```ts
type FilteredSoldier = {
  [K in keyof Soldier]: Soldier[K] extends string ? Soldier[K] : never;
};
```
- you can also use that to extract keys of types to do something like creating a union

## Local development
- nice `tsconfig` notes. This is what you change to modify typescript behavior:
  - lib: Add dom and dom.iterable (note: lowercase) to the list of libraries to allow all the browser APIs if you're writing front-end code.
  - strict: If true, enables all strict type checking options. I strongly recommend it for new projects. You might need to turn it off if you're migrating an existing JS project.
  - skipLibCheck: If true, skips type checking of all declaration files (which means it won't try to type check your infinitely large node_modules folder). Drastically speeds up compilation time.
  - verbatimModuleSyntax: If true, simplifies some weirdness with importing and exporting types, basically it forces you to import and export types using the import type syntax. I recommend it.
  - esModuleInterop: If true, allows you to use import syntax with CommonJS modules. Very useful if you need to work with CommonJS (Node) code.
  - moduleDetection: If set to force, will consider everything to be a module, which is what you want in any new project.
  - noUncheckedIndexedAccess: If true, adds undefined to the type of any indexed access, which can prevent some runtime errors. I recommend it.
- `d.ts` files are declaration files for types but does not include runtime code. Useful for if you want to delcare types you use in your code but don't define yourself (like using libraries)
- remember that typescript is used a lot of times as a language server and linter rather than compiler in local development. So make sure to keep your local typescript in sync with remote / deployment env
- `@ts-ignore` and `@ts-nocheck` are useful for spot disabling lines or files from checking respectively
- typically you're always working with a build tool when building front end apps (like Vite)
  - they handle things like creating a dev server with hot reloading, compiling on changes, building, etc.
  - just tries to make your life easier as a developer to make and see your changes
- for runtime validation (for things like API calls) there are libraries like `Zod`