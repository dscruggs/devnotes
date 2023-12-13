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

### Experimental Update Meeting: Do we know what we're doing here?

This is the hardest meeting for DS teams because its sharing a bunch of half-baked ideas, but it's a VERY important meeting

- this is almost the last time for the DS team to graciously say this project is impossible
- the focus of this discussion should be "Can we actually figure this out?"

Discussion points:

- how is the progress towards the prototype?
  - are any of the tests done
  - which option looks the most promising
  - have any options been killed
  - is the due date on track
- what risks have you uncovered?
  - are there data challenges data engineering needs to know about?
  - are we going to need a new technology, platform, or tooling that the team doesn't know?
  - right now, is the problem solvable?

If all answers are positive, keep working

#### How to admit defeat and cancel a project gracefully

The earlier the better, and it's important to know that you're saving everyone time and money to do something truly great

### SME review/prototype review: can we solve this?

This is the most important early meeting. Resource commitments happen here and the official go/no go is here

Discussion points:

- the same discussion topics as the experimental update meeting should be discussed, but there's more information known so a real answer to if there's the capability, budget, and desire to develop the full solution
- Show the prototype and record positive / negative feedback
- get consensus on whether most in the group are comfortable going with the recommended approach

### Development Progress reviews: is this going to work?

These are focused on "righting the ship" during develipment

Discussion points:

- milestones
- risks to developement / deadlines
- current feature demos and feedback

Early meetings should be focused on the actual development, getting feedback and confirming fixes to negative feedback

Later meetings should be focused more on integration and demos should be more elaborate and representative of the final product. Working in QA environments when possible

### MVP Review: did you build what we asked for?

By this point all the tests should have passed and load tests done, this is where you focus on UAT testing and code hardening for production (logs, monitoring, etc.)

Discussion points:

- get feedback on the MVP
- give status on fixes in UAT

Make sure to get good feedback in this stage. If there's no feedback it usually means SMEs are checked out, so guide them

### Preproduction review: We really hope we didn't screw this up

This is after UAT passes, code hardened, and you've been running the solution for a while without failures in the tesing environment, you're ready for monitoring and reporting in production after launch

Discussion points:

- project retrospective and analysis of features
  - "did we build what we set out to build?"
  - compare initial design and features to the final ones
- do final scenario checks and feedback to make sure you're covering all your bases

## Setting Limits on your experimentation

Time is the most finite and valuable resource at a company. Use it wisely in projects. This means you need boundaries, and experimentation is the hardest one to time bound

### Set a time limit

- Set a time limit that allows time only to find an algorithm that will work well at scale and cost, and that meets the quality standards and use case set out in planning.
- Don't allow time for complex code or full solutions
- simply the amount of steps needed to get experiments done. use a static dataset, notebooks, etc. don't develop for production

### Can you put this into production? Would you want to maintain it?

There's important questions to consider here during experimentation to use to chop off potential solutions:

- how long is this solution going to take to build?
- how complex is the code going to be?
- how expensive is training and retraining going to be?
- does the team have the skill to maintain and build?
- how quickly could we modify this solution? Is it extensible for future iterations?
- has anyone else reported success with using this methodology? Are we reinventing the wheel or doing something different from normal patterns in this space?
- how much additional work will the team have to do to meet all the features?
- Is it testable / auditable?

Don't just go with the ones with the "best results"

### TDD vs RDD vs PDD vs CDD for ML projects

#### Test Driven Development or Feature Driven Development

- very hard for ML because you can't really test in the same way as traditional software
- pure feature driven development can cause a lot of rework

Aspects of these methods are useful, but its important to stay flexible and keep the future in mind

#### Prayer-driven development

- "Just work please" development is what a lot of ML was in the early days
- another form of this is listening to advice online for ML in blog posts where the writer has no more experience than the data scientist developing the solution
- `copy culture` in ML is huge and you should be cautious, taking the "easy road" presented in blog posts rarely ends well

The lesson here is to never go with just 1 approach in experimentation, always go with a few different ones because no solution works all the time for every problem

#### Chaos-Driven Development

- also called `cowboy development`, this is skipping experimentation and prototyping altogether
- this leads to a LOT of rework because there's no feedback ahead of real code being written
- don't do this, this is very common in ML

#### Resume-Driven Development

- overengineering, show off implementations
- this is a leading cause of project abandonment because of the resources spent on a nonworking solution

characteristics:

- novel algorithm is involved with no sufficient justification
- a new framework is involved in executing the job
- blog posts are being written about the solution during development
- a ton of code is devoted to the ML algorithm
- there's a ton of discussion about the model rather than the business problem

Some problems require very complex solutions, but this shouldn't be the starting point and should only be pursued once all other options are exhausted

## Planning for business rules chaos

Business rules are very important but also are difficult to implement correctly in a project and plan for

### Embracing chaos by planning for it

- try to learn about business rules before project coding starts
- anticipate changes will come and write easily extensible code
- treat required business rules as planned within the scope of the project and as an integral part of the modeling process
- non-essential business assumptions or hypotheses could be aspects that can be tested through a/b testing or moved to future work

### Human in the loop design

- assume humans will need to make and update rules as the model progresses
- implement guardrails to limit changes without approval processes

### What's your backup plan?

- You need to plan for sparse data because there will always be edge cases (new or returning customer, outlier, etc.)
- all ML projects should expect data quality issues
- a fallback such as pulling aggregated data to make predictions or sending predefined response for these cases should be planned for upfront

## Talking about results

Explaining why a model is doing what its doing is always a difficult task

- use data when possible
- explain clearly when you don't know the answer and why
- explain model limitations and limitations of ML (it needs data)
- educate and speak in laymens terms
- don't talk down, you know about ML not everything
