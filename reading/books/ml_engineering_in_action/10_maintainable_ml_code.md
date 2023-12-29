# Chapter 10: Standards of coding and creating maintainable ML code

This chapter goes over the most common issues in ML code bases that directly affect the stability of the solution, and the ability to maintain

## ML code smells

`Code smells` are characteristics of code that may be indicative of deeper problems. Not necessarily bad in of themselves, but are usually signs of bad code

#### Examples of bad ML code smells

| Smell                                                                     | Description                                                                  | Issue |
|:------------------------------------------------                         |:----------------------------------------------------------------------------|:-------|
| **Wildcard imports**                                                      | import *                                                                     | may create namespace collisions, affects readability      |
| **Multiple imports**                                                      | import numpy as np from numpy import add                                     |  confusing and unneccessary     |
| **Too many parameters**                                                   | lots of function / class parameters                                          |   harder to read and maintain, usually a sign of monolithic functions    |
| **Copied boilerplate**                                                    | not wrapping repeated code lines in objects (ETL, feature engineering, etc.) |   harder to read and maintain, should be abstracted    |
| **Default models / hyperparameters**                                      | using defaults for models or using untuned hyperparameters                   |   usually a sign of lack of experimentation / underperformance    |
| **Variable reuse**                                                        | reusing the same variable over and over                                      |   can cause weird stateful bugs. Harder to maintain and debug issues. Violates single-responsibility principle    |
| **Use of literals**                                                       | hard coded numbers in code (especially calculations)                         |  updating these is hard and time consuming     |
| **In-line comments explaining how the code works**                        | comments explaining the how (not the why)                                    |   comments should explain why, not how code works. If you need comments your code isn't good enough    |
| **SQL without common table expressions (CTEs / temp tables) / SQL walls** | bad / unstructed sql                                                         |    hard to debug and read. can lead to performance issues or excessive rewriting of queries   |
| **Constant recasting**                                                    | ints, floats, strings, etc.                                                  |   cast things once and make sure they conform to proper types    |

## Naming, structure, code architecture

key principles:

- Naming conventions should be informative and specific. Don't override any core functionality with generic names (list, etc.)
- Don't try to be too clever with code and make it very concise at the expense of readability
- Don't reinvent the wheel. Use packages when available
- for architecture, don't be rigid. Go with what is simplest for the people maintaining the code to handle
  - only add to current code where new features fit in directly and completely. Otherwise it's usually easiest to start new modules / classes
  - if you have to add all new kinds of connections to an existing module that only adds complexity

## Tuple unpacking and maintaining alternatives

key principles:

- tuple unpacking is messy and makes code harder to understand and maintain
  - ex: if a function returns 3 variables but one call only needs two of them. Or if you update that function to return 2 you need to update all your other places you call that. Or if you have 1 variable to capture the whole tuple on the call, that makes it hard to understand what's happening when reading
- named tuples or dictionaries are the preferred approach when functions need to return multiple items. named objects are much easier to understand than positional encodings for unnamed tuples
  - But a good rule is to keep it to 3 or less items returned so people aren't overwhelmed

## Blind to issues: eating exceptions and other bad practices

`Blind catch` or `eating exceptions` are when you put a try except block in code simply to make it work, and don't record the exceptions anywhere or make any attempt to handle them. This leads to lots of problems down the road

Issues with blind catching:

- makes debugging difficult (you don't know what went wrong)
- leads to downstream issues (allows edge cases to slip through silently) that may fail code or affect model outputs
- makes code more complex

#### Levels of error handling

1. blind try expect block (just get to it work) ((this is bad))
2. try except block for specific exceptions with stdout print (this is better but printing to stdout isn't useful for future debugging)
3. try except block for specific exceptions with outputs to logs, and custom exception classes where necessary (preferred)

key principles:

- log everything you think may reasonably be useful in the future. Even innocuous errors can be useful to know about down the line
- don't use blind exception catching
- don't print to stdout

## Use of global mutable objects

key principles:

- mutability is a problem because it can silently alter objects in ways you wouldn't expect
- know how your language uses mutability and handles state management (for example, in python everything is mutable)
- encapsulate logic and data within functions and methods whenever possible
  - A variable declared in a method is referenced only within that method.
  - The only external-facing access that a method has to the outside world is in its return value.
  - The operations that are performed cannot be influenced by the state of anything other than the arguments passed into it.

## Excessively nested logic

key principles

- huge if else nested statements are hard to read and problematic to update (especially for business logic w/ hardcoded values)
- dictionaries for the business rules are better but still has scaling problems when things get very complex
- try to reduce the problem complexity as much as possible before going down the path of dictionaries and configurations for logic
