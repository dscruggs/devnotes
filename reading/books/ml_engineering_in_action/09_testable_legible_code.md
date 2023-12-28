# Chapter 9: Modularity for ML: Writing testable and legible code

This chapter is all about some useful tips for writing good code for ML

**Aspects of code to consider:**

- code structure
- how code is written (syntax & style & naming conventions)
- how efficiently code runs
- how much info is logged / retained from runs
- how easy troubleshooting / debugging is

#### Complicated vs complex code

- `complex code` refers to lots of branching logic paths a piece of code can go down. It's often related to the complexity of the problem trying to be solved, but also can be introduced by bad coding habits
- `complicated code` is code that's written in a way that's difficult to understand

The goal of of ML codebase should be to focus on keeping the measure of *complicated code* as low as possible, while also focusing on reducing the *complexity* as much as is practical

## Understanding monolothic scripts and why they're bad

- monolithic scripts and codebases in ML usually result from porting prototypes to production without refactoring
- sometimes logic would say having everything in one nice linear script is easier to look at. Issues with that:
  - debugging is hard
  - isolating chunks of code is difficult for testing
  - changes are difficult

### Debugging Walls of Text

Debugging Monoliths often involves print statements, commenting out blocks of code, and repeatedly making changes until all errors are gone

The issue here is **you have to modify the source code to debug and testing**. There's no guarantee you won't introduce more issues down the road by these changes

## Designing modular ML code

- Think about the structural and functional aspects of the solution
- Think about public vs private functionality (what do users need to see / know about?)
- think about naming conventions (shorthand and acronyms aren't good)
- dense code blocks and control flows won't get you much performance increase and will lead to tech debt and more overhead for interpretibility

## Using test-driven development for ML

Test driven development is difficult to do in a pure sense for ML. But the principles are very useful

The core principal is to have intentional and predictable behavior that can be confirmed as functioning correctly during your development process

Splitting up your codebase should be thought of as "how can I test this block of code" rather than "what looks nice?"

Write your tests separate from the actual code

**The particular framework or methods used to test aren't as important as doing the testing and being consistent in your approach**

Having a solid test suite takes away uncertainty around changes made and whether or not they will break code down the line
