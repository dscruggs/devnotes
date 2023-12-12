# Chapter 4: Before you Model: Communication and Logistics of Projects

Data science teams sometimes struggle with communicating to business stakeholders because they're speaking a different language. We need to do this translating so that we can have meaningful conversations with the business.

Areas DS struggle with:

- knowing which questions to ask at what time
- keeping communication targeted on essential details and ignoring insignification things to project work
- discussing project details in laypersons terms
- focusing discussions on the problem instead of the machinations of the solution

#### Having Difficult conversations

- Don't talk at people, listen and clearly communicate their concerns back to them so you're on the same page
- be honest, don't promise magical solutions that you can't deliver
- Help them to understand your point of view and communicate in ways they can understand

#### Key questions for business

- Why do you want this built?
- What do you expect a solution to do?
- How does your team do this now?
- What would you consider to be a perfect solution for this?
- How much would you pay for another company to do this for you?
- When would this solution become irrelevant?

You want to understand what to build, how to build it, when to have it done, and what the success criteria is

## Communication: Defining the problem

Technical people want to focus on the *how* of the problem, but it's important to first understand the *why*.

The kickoff meeting should help the data scientists to get its research, scoping, and planning details. The business should get a review schedule on the work to be conducted and expectations on commitments from them

By focusing on the key principles of the project you also can understand the simplest way to solve the problem, and then iterate from there

### Understanding the problem

There should be a clear, agreed upon idea of *what* to build and *why* its being built

#### What do you want it to do?

The team should develop some high level diagrams / wireframes to define how the solution will work in production and how it will be used (`flow path diagrams`, `wireframes`, `user-experience journey map`). Think through the different personas, take a page from UX designers. Even if there won't be a front end on the project.

Showing these diagrams will help in ideation sessions and will uncover important details that may alter these plans early rather than right before production

- take down hard requirements
- take down "nice to haves" with the caveat that there is a cost to each addition
- keep notes on all ideas, but don't allow all ideas to get into the core experimentation plan

Keep engineering details and architecture details as minimal as possible at this stage to include everyone in the conversation

#### What does the ideal end-state look like?

Have a stream-of-consciousness discussion on what an ideal solution would look like with SMEs and don't immediately shut down ideas, just listen

- helps build a sense of trust and ownership
- may lead to key insights that will build a great solution

#### Scope Creep

Avoid *death by a thousand requests*. Get a solid understanding of the ideal end state up front and frame all future requests in the light that they will push back these core components from timely release. If that's acceptable, then do it. Otherwise don't.

#### Who is your champion for this project that I can work with on building these experiments?

You need an SME who is *not* the executive project owner / sponsor. You need someone on the ground with vast domain knowledge on workflows in the area you're building

SME should have the time, availability, and authority to deal with the projects and critical decisions that will need to be made throughout

#### When should we meet to share progress?

Frequent meetings are critical. But they should not always be prescribed on a cadence-based schedule. They should be purposeful and tied to events / milestones in the project

- include only relevant people in meetings
- full team should be included for demos and major decision points so everyone is on the same page

The goal is to minimize rework and make sure the project is on track to meet the requirements and vision you set out to do

### Setting Critical Discussion Boundaries

- Set deliverable date meetings with tangible results to review, a strong agenda, and expectation of contribution from everyone who attends
- You should communicate that heavy cadence of status meetings will slow progress if the idea is raised
- The general rule of thumb is you should only meet when there's something new to review, but this varies from project to project

#### Post-research Phase Discussion (Update Meeting)

- goals:
  - focus on the core details required to get to the next phase of experimentation, don't explain every option researched
  - present the viable options to the SMEs and go over pros and cons and your recommendation
  - leave technical complexity and details out of the discussion and relatable to the SME
  - only present the options that give the greatest chance of project success
- outcomes:
  - decision on the path forward for experimentation
  - new important features are added to development plan from discussion

#### Post-experimentation phase (SME / UAT review)

- goals:
  - show what a prototype of the solution looks like, including predictions
  - show a mockup of the core features, even though the full implementation should not be done
  - give the group a picture on what the final solution will be
- outcomes:
  - original features and requirements can be revised if necessary
  - update assumptions and plan from the planning phase
  - everyone is aware of the moving pieces involved in the project, and everyone knows what work there is to do going forward: what is already defined and what is left to scope and plan

#### Development Spring Review (Progress reports for a nontechnical audience)

- goals
  - recurring meetings to serve as a bellweather for the state of the project and understand when integration of systems can begin
  - have a high-level project focused discussion
  - generate a current-state demonstration of progress on the project up to that point
  - show simulations of the final solution
- outcomes
  - get everyone on the project on the same page frequently outside of technical details
  - risks can be surfaced and dealt with ASAP
  - cross functional communication strengthened

#### MVP Review (Full Demo with UAT)

Code complete solution, with passing tests, functioning solution, and all planned features are complete

- goals
  - MVP review and UAT testing are had to pass subjective quality tests
  - model results are validated by real users and high-value scenarios are tested
  - subjective feedback from the group is given
- outcomes
  - You can reconcile the subjective feedback from the group with ML metrics to determine the best solution
  - iterate based on feedback
  - get baseline approval from the group

#### Preproduction (Final demo with UAT)

This is held after all the UAT tests have been addressed and issues fixed, load tests are complete, etc.

- goals
  - review comparisons to original plan, features rejected, and any additions
  - lay out where they can find performance data on the project and how they can evaluate themselves how its doing self-service if possible
- outcomes
  - path to production and validation post-launch are fully laid out
  - understanding of what work is left to do after launch is defined

## Don't waste our time: meeting with cross-functional teams
