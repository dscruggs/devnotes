# Machine Learning Engineering in Action

By Ben Wilson

[Accompanying Github Repo](https://github.com/BenWilson2/ML-Engineering)

Nice words of advice given by Ben:  
*Ask the experienced rather than the learned.*
—Arab proverb  
*It is best to learn wisdom by the experience of others.*
—Latin proverb

This book is split into 3 parts with chapters within each. I want to keep the chapters as the main visual sections, so I'm just going to bold the sections.

--------------------------------------------

**Part 1: An Introduction to Machine Learning Engineering**  

Goes over the begginning phases of ML projects: ideation, planning, scoping, and experimentation. The keys here are focusing on the **What** over the **how** initially, constant communication, and speed in arriving at a working solution early on

## [Chapter 1: An Introduction to Machine Learning Engineering](./01_what_is_mle.md)

Discusses what ML engineering is and the core tenets and goals of it

ML engineers need to know aspects of data science, software engineering, project management, and many other things to be sucessful, all with the goal of ensuring ML projects succeed in being deployed and providing measurable business value

Focus on solving the problem, less on technical details until you understand the problem to be solved completely

Six project phases: planning, scoping and research, experimentation, development, deployment, evaluation (maintenance and proving value)

Topics include:

- tenets of ML Engineering
- High level roadmap for ML projects

## [Chapter 2: Your Data Science Could Use Some Engineering](./02_data_science_eng.md)

Covers differences between data scientist and ML engineer, and gives strategies from software engineering to run successful ML projects

ML Engineering can be thought of as a trinity of core concepts:

- Technology (tools, frameworks, algorithms)
- People (collaborative work, communication)
- Process (software dev standards, experimentation rigor, agile methods)

ML Engineering is all about keeping things as simple as possible to solve a given problem

Topics include:

- What is ML engineering
- Agile for ML
- Devops vs MLOps

## [Chapter 3: Planning and Scoping a Project](./03_planning_project.md)

Discusses planning and efficient solution evaluation methods for ML projects

The most important points here are to have clear communication with the business, especially in the early phases. Focus on the *what* and *why* during planning, and timebox your research and experiments to arrive at a working, practical solution as quickly as possible.

Topics include:

- How to plan out a project
- How to approach research
- How to approach experimentation and work estimation

## [Chapter 4: Communication and Logistics of Projects](./04_comms_logistics.md)

Goes over the structure of meetings and communication involved with projects. Major points are around clear and open communication is key, constant feedback is key, and you should tie meetings to milestones and with a clear agenda to not waste peoples time

Topics include:

- questions to ask the business during planning
- meeting structure and goals during a project
- approaching experimentation communication
- dealing with business rules and project changes
- explaining ML topics to laymen

## [Chapter 5: Planning and researching an ML project](./05_experimentation.md)

Goes over in a deeper fashion the process of researching, planning, and doing experimentation using a time-series example

Topics include:

- approaches to research and planning prior to experimentation
- approach to experimentation
- reusability in experimentation

## [Chapter 6: Testing and Evaluating a project](./06_testing_eval.md)

Goes through in detail how you should approach experimentation and evaluation during your rapid experimentation phase. Also gives some great questions to ask while planning the transition into development afterwards. Takeaways are to be consistent in all your experimentation methods, and wholistic in your evaluation, using a variety of visualizations and metrics outside of pure model performance

Topics include:

- experimentation process
- evaluation approach
- planning development once an approach is selected

## [Chapter 7: Moving from Prototype to MVP](./07_moving_to_mvp.md)

This chapter is all about model hyperparameter tuning. It goes over the tuning methods themselves and some technologies and methods to help speed up and distribute tuning jobs

Topics include:

- hyperparameter tuning methods / libraries
- Apache Spark for hyperparameter tuning in a distributed / parallel fashion

## [Chapter 8: Finalizing an MVP with MLflow and runtime optimization](./08_finalizing_mvp.md)

This chapter covers logging, monitoring, development patterns, and infrastructure

The main takeaways are to track everything possible, choose your infrastructure wisely in a way that supports cost, stability, and ease of use, and only increase complexity when there's tangible benefits

Topics include:

- ML logging
  - MLflow specifics
- scaling and concurrency

--------------------------------------------

**Part 2: Preparing for production: Creating maintainable ML**  

Focuses on the ML coding and solution itself. Focuses on building fundamentals to achieve:

- Code that runs well
- Code that is testable and able to be debugged
- A solution that can be modified easily
- A solution that can be evaluated for performance (based on whether it solves the problem well and continues to solve the problem it set out to solve)
- A solution that you don’t regret building

## [Chapter 9: Modularity for ML: Writing testable and legible code](./09_testable_legible_code.md)

This chapter covers some good code practices (pretty basic stuff for software engineers) related to modular and test-driven development

Topics inlcude:

- monolithic scripts / codebases and their drawbacks
- how to think about modular codebases
- test driven development / testing best practices

## [Chapter 10: Standards of coding and creating maintainable ML code](./10_maintainable_ml_code.md)

Goes over problematic implementation patterns in ML codebases, and how to handle many common problems with them

Topics include:

- code smells
- naming conventions and code structure
- return handlings
- error / exception handling
- mutability in python
- nested logic / business rules

## [Chapter 11: Model measurement and why it's so important](./11_model_measurement.md)
