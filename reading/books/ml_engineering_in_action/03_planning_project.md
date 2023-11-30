# Chapter 3: Planning and Scoping a Project

The biggest killers of ML projects are planning and scoping projects

Teams focusing less on the *how* and more on the *what*, and working more in collaboration and communication with eachother and the business leads to more successful projects

ML projects that start with a vague problem statement from the business and data scientists immediately begin trying out algorithms are doomed to fail

**A better approach:**

- Get requirements
- Scope out project at kickoff meeting with business
- Hold internal meeting to plan research
- Select approaches to test in experimentation phase
- Hold another business unit meeting to explain experiments and gather additional info
- Finalize approach based on discussions

NOW work

This decreases wasted effort chasing what the customer really wants after building a solution, and builds a sense of trust with the customer

**Planning and scoping properly as an ML practitioner, rather than leaving it to PMs, will save you a lot of time and trouble down the road**

## Planning: You want me to predict what?

A lack of planning and solid understanding of the business problem to solve and customer requirements can lead to failure, even if your model metrics are good.

Ex: A product recommendation system has a low RMSE but all the recommendations are of the same product type, when the business wanted variation

Business stakeholders often know very nuances details of datasets and problems that need to be fleshed out through frequent collaboration

Frequent validation and checks from them, and not just model metrics, are important to a successful model

### Basic Planning for a project

The beginning of an ML project starts with an idea. That idea can have many meanings to many people. The first thing to do is have a meeting and discuss *Why are we building this?*

`Goals of the first meeting:`

- Opens a discussion to motivations for the project from different groups
- Defines the expectations of the output of any ML work done
- Decides an metric criteria for the model's performance to be used to measure success
- Aligns everyone's expectations (business users and dev team)

There's a venn diagram with little overlap at the start of a project between business and DS team understanding of the project. **It's the job of the DS team lead and project manager to widen the overlap before any discussion of technical details**

**False Assumptions to Challenge in Planning**

- Assumption of Business Knowledge
  - ML teams are often insulated and don't share intricate business knowledge that many execs and leaders take for granted
  - SMEs should be used to explain how they currently do related tasks the model is being designed to do, this allows for discovery of nuances and rules that may be applied to model outputs
- Assumption of Data Quality
  - Data quality is a huge problem, and a main reason companies struggle to get ML solutions to production
  - Anticipation, validation, and understanding of the nature of data from SMEs is the way to solve this. Data will never be perfect
- Assumption of Functionality
  - There's always new feature requests from end users in a demo
  - Don't dismiss them, or blindly accept them. Present the time, money, and human cost to the feature and let the customer decide if its worth it
- Curse of Knowledge
  - Details of solutions will probably sound like babble to anyone outside the development team
  - Try to handle complex topics in layperson context always, if they ask for more detail, ease into it
- Analysis Paralysis
  - Without planning, an ML team will just overexperiment in an unbounded fashion
  - Approach projects with the overall goal in mind and keep the solution as simple as possible

### The First Meeting
