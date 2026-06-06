# Functional Programming

## Takeaways

- Functional programming is a focus on immutable data types, and declarative-style programming over imperative
- Not focused on manipulating state like OOP. Functions should be stateless
- Tries to minimize need to dig into state to debug, can just look at the flow of data and actions in functions
- "supporting first-class functions" means they're treated like any other variable, so they can be passed as arguments to other functions, returned, and assigned to variables

## Higher-order functions

- `Higher order function` is a function that accepts another function as an argument
- Common higher order functions are map, filter and reduce
    - `map` maps a function to a list of values and returns a list
    - `filter` filters an iterable and returns that filtered iterable
    - `reduce` takes a function and list of values and applies the function to the list, accumulating a single result as it goes

## Core concepts

- `pure functions` are functions that always return the same value for the same arguments, and have no side effects
- pass by reference / pass by value concept. Be aware. reference can change the original values, values are only a copy
- `memoization` is storing results of a function and returning that result later rather than rerunning, basically just a type of caching
- `referential transparency` is the property of a function where you can replace it by its equivalent output without issue (basically just deterministic output of a pure function)

## Recursion

- oftentimes slower than a normal loop but sometimes the right tool for the job
- beware of stack overflow
- tail call optimization can be helpful here but python doesn't support it

## Python-specific

- `function transformation` is a specific type of higher order function that takes a function as input and returns a new function
- `closures` are functions that are stored along with an environment. Basically the function returns another function, but with some variables defined that it has access to. It's basically a stateful function if you modify those variables in your inner function
- `currying` is a type of function transformation where a single function accepting multiple args is transformed into multiple functions each accepting a single arg. Can be useful to modify the function signature to better fit an interface. Kind of a rare need in python it sounds like
- `decorators` in python are a function transformation where a higher order function takes a function and returns a function with new behavior
- `args` and `kwargs` allow functions to deal with a variable number of arguments. Useful for making functions really flexible
- `lru cache` is a type of decorator and uses memoization in python
- `Sum types` or `tagged union` is a data structure to hold a value that could take on several different, but fixed, types. Only one type can be used at a time. This is helpful for constraining types in a flexible way. Python doesn't really support it, but enums are kind of like that?
- I was today years old when I realized python has a `switch` equivalent: `match`
