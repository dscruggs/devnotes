# Chapter 1: What is a machine learning engineer?

### Overview on ML Projects

- ML projects are hard
- Tooling or algorithms are never the reason ML project fails to provide value to the company
  - planning, scoping, and experimentation are the key steps in ensuring success

- ML is fun but complex
  - ML projects involve data engineering, statistics, visualization, business expertise, and more
  - ML engineering applies a system around this complexity to minimize the chances of failure

### High Level Roadmap for ML Projects

ML work is all about solving a business problem. Tasks should be done for a "why", not a prescriptive plan. The goal of a process is to reduce rework, confusion, and complexity.

1. **Planning (Cross Functional Planning)**
    - **Steps:** problem definition, when to deliver
    - **Goal:** Focused on alignment between the business unit and the data science team on the project, *not the implementation details*
2. **Scoping and Research (Cross Functional Planning)**
    - **Steps:** Research, staffing, SME interviews
    - **Goal:** What can we test, who can test it, what details about this problem will help guide the way testing is conducted?
3. **Experimentation (Go/No-Go on project)**
    - **Steps:** Feasibility checks, MVP scoping, cost / efficacy tests.
    - **Goal:** Which (if any) of the approaches tested has the best chance of solving the problem? How long is it going to take to build?
4. **Development (Project Commitment)**
    - **Steps:** Platform selection, code design, MLOps tooling
    - **Goal:** writing maintainable, testable, and extensible code that supports metric tracking, parameter logging, and artifact registration
5. **Deployment**
    - **Steps:** testing, infrastructure, model performance validation
    - **Goal:** Validate model performance (ML Metrics), QA evaluation (business metrics), and deploy cost-effective serving infrastructure
6. **Evaluation**
    - **Steps:** Drift detection, attribution, retraining
    - **Goal:** implement model health checks, problem solution validation (look at project OKRs), and decide on/implement retraining paradigm

## Why ML Engineering?

The goal of ML engineering isn't to become a master at everything. It's to know just enough about each component of ML work (data engineering, software engineering, data science, visualization, etc.) to build a system that gets to production, solves a problem, and is easy to maintain  

### Why do so many ML projects fail?

Mainly planning and scoping, some issues with tooling maturity, and inexperience of devs.

ML Engineering is in the same place Software engineering was in the early 90s, many people don't know how to do ML right. Extra care has to be taken to minimize risk of projects

## The Core Tenets of ML Engineering

### Planning

Don't build an ML solution to solve the wrong problem

- High level requirements aren't enough
- Detailed planning sessions (non technical) should be have to reveal the true expectations of the business
- Without proper guidance on what needs to be built, how it must function, and what the end goal of predictions are, projects are doomed to fail
- Lots of wasted mental effort and man hours can be spent if the business problem and solution aren't fully understood
- A few clarifying questions can help determine where to go next and what quick wins may be available

Key questions: "**What** will be built?" "**Why** does it need to be built?"

**How** and **when** can be decided after everyone is aligned

Don't discuss implementation details with the business in the planning phase. It keeps everyone engaged on the problem and the value.

**Business units don't care how many eggs go into the mix or what color they are. They just want to eat the cake when its done**

### Scoping and Research

- Planning upfront is important. If you switch approaches mid development, the business will think you're an idiot. They care about 2 questions:  
  - Is this going to solve my problem?
  - How long is this going to take?
- Insufficient planning, underestimating complexity, and overscoping can all lead to the downfall of ML projects
- Embrace both sides of the experimental and practical mindsets to ensure you get an adequate solution for a problem that can be built quickly

### Experimentation

If you don't test enough, you're probably not finding the best solution. If you test too much it wastes time. Find the Goldilocks zone

Spend enough time to find the right solution to the fully understood problem before beginning development, taking into account budget and complexity of testing and development

### Development

Poor dev practices won't be as directly visible as other causes of failure, but will make a project harder to work on, easier to break in production, and harder to improve over time

Notebook and scripting based codebases will fail over time, and making edits to these codebases, especially with multiple developers, is very challenging. Modular and decoupled codebases are better

### Deployment

Not planning a project around deployment needs and strategies will either result in wasting money or making models unusable

Strive for the simplest deployment strategy possible for the requirements of the use case. Each added complexity is another thing that can break

Underengineering or overengineering risk is high if you don't plan out deployment at the start. There's cost and complexity tradeoffs to be discussed as well as what's appropriate for a given problem.

### Evaluation

*If you can't justify the benefits of your project being in production, don't expect it to remain there for very long.*

How do you tie model predictions to some aspect of the business that can justify its existence?

Metrics for attribution and measurement of model performance should be agreed upon **during project planning**

## The goals of ML engineering

Number 1 goal is to solve problems. Not develop the most complex solution or fanciest models. We want to see our models in production solving real business problems
