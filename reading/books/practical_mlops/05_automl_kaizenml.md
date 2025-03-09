# Chapter 5: AutoML and KaizenML

`AutoML` is about creating a model from clean data

`KaizenML` is about automating *everything* about the ML process and improving it

## AutoML

Self-handicapping strategies

- Real: placing visible obstacles to success in one's own path. This makes you less likely to succeed but also provides a ready excuse for failure (drinking before an audition, not studying for an exam)
- Feigned: a person claims that there were difficult obstacles in the path to success, and that's why they failed. aka making excuses that were out of your control

Data science intiative failures often dive into one of those strategies

In software engineering its important to use the best tools available. Data science often denies tools in favor of building themselves. **You don't need to build it to use it**

AutoML solves the problem of automating the training of a model on clean data. The world isn't so simple and more needs to be automated. This is where KaizenML comes in

## KaizenML

### MLOps Industrial Revolution

There's no question that humans can do the whole data science pipeline. But should they?

Humans could build cars but robots can do it better and faster, so humans can focus on more complex problems that can't be automated

### Kaizen vs KaizenML

"Data science is not a solution to any a problem any more than statistics; it is behavioral"

AutoML is just a techinique like CICD, it's not competing with data science. Humans must still make executive decisions about the bigger picture

With model building automated, humans can focus on what matters: the execution

### Feature Stores

DevOps + KaizenML = MLOps

One part of kaizen ML is building `Feature Stores` i.e a registry of high-quality machine learning inputs and the ability to monitor data for drift and register and serve out ML models

Feature stores accomplish 2 things:

- Allows users to add features they built into a shared Feature Store
- Once features are in the Store, they are easy to use in training and prediction

Uber used 10k features in their Feature Store to accelerate ML projects

## Apple's Ecosystem

Apple apparently has a rich ML ecosystem focusing on power and hardware efficiency and to run on their mobile and M series chips

`Apple CreateML` is their AutoML solution

You can download models from other frameworks like tensorflow and convert them to apples CoreML framework in python

## Google's AutoML and Edge Computer Vision

You can quickly go from idea to execution using cloud ML stacks, from AutoML to using pretrained models.

Google has some good options for deploying to Edge Devices

## Azure AutoML

Azure has a great ML ecosystem including AutoML

Can use Azure ML Studio to train models and explain why predictions happened

## AWS AutoML

SageMaker AutoPilot is their autoML solution

## Open Source AutoML

Nothing beats free

### Ludwig

you pass a dataset and a config file and can train models. The biggest open source solution currently out there

### FLAML

Has a design that accounts for cost-effective hyperparameter optimization

### Other Frameworks

- H2O AutoML
- Auto-sklearn
- tpot
- PyCaret
- AutoKeras

## Model Explainability

Most MLOps frameworks like sagemaker and azure ML have built in explainability reports

There's open source frameworks you can use to build your own capabilties:

- ELI5
- SHAP

[Model explainability github project example](https://github.com/noahgift/model-explainability)

`SHAP` is great to look at feature importance to predicitons
`ELI5` has a lot of different methods for looking at feature importance and effect of removing features

## Exercises

- [ ] Download XCode and use Apple’s Create ML to train a model from a sample dataset you find on Kaggle, or another open dataset location.
- [ ] Use Google’s AutoML Computer Vision platform to train a model and deploy it to a Coral.AI device.
- [ ] Use Azure ML Studio to train a model and explore the explainability features of Azure ML Studio.
- [ ] Use ELI5 to explain a machine learning model.
- [ ] Use Ludwig to train a machine learning model.
- [ ] Select a SageMaker Automatic Model Tuning example from the official Sage‐Maker examples and run through it on your AWS Account.

## Critical Thinking Discussion Questions

#### Why is AutoML only part of the automation story with modern machine learning?

AutoML automates the model creation using clean data. It does nothing to automate the data engineering, business processes, model metrics tracking and monitoring, or any of the other parts that go into building and maintaining ML models. That's where KaizenML comes in.

#### How could the NIH (National Institutes of Health) use a Feature Store to increase the speed of medical discoveries?

NIH could store features used for past medical discoveries as starting points for testing new medicines and methods.

#### By 2025, what parts of machine learning will be fully automated and what aspects will not? By 2035, what parts of machine learning will be fully automated and what factors will not?

By 2025 I think we could see the automation of building the code or configurations of most components of ML systems using something like ChatGPT, but it will still have to be fit to specific use cases and data. By 2035 I could see fully integrated SaaS platforms that are point and click and you're able to do automated data engineering, model building, monitoring, and retraining without any knowledge of the ML algorithms or data engineering. I think it would still take a lot of configuration, though a lot of that could be automated. The future of ML is knowing how to build and manage these pipelines and organize buisness units and metrics to take advantage of ML use cases.

#### How could vertically integrated AI platforms (chips, frameworks, data, and more) give particular companies a competitive advantage?

It allows full optimization of algorithms and data to increase performance, and allows for feedback loops where those companies can deploy models and collect data to improve future models. This is how many companies like Google, Facebook, Amazon, Microsoft, and Apple have such a large advantage.

#### How does the chess software industry provide insights into how AI and humans work together for improved outcomes in solving problems for AutoML?

The chess software industry shows that humans augmented with AI tooling can outperform AI alone for many tasks, and this is likely the future that will exist for AutoML. Humans are great at making high level, long term decisions, and AI is great at doing lots of quick computations and finding patterns in data. This combination can solve much more together than apart.

#### How does a data-centric approach differ from a model-centric approach to machine learning? How about a KaizenML approach where data, software, and modeling are all treated as equally important?

A data-centric approach differs from model-centric in that data-centric invests all its resources into improving the quality and quantity of data available for models. The model-centric approach is focusing on building new algorithms and optimizing existing algorithms to get performance gains. From the human standpoint we have a lot more to offer on the data side since capturing and curating good datasets is still something machines can't really do in an automated fashion for many use cases. Business needs, future plans, cost, and strategy all come into play when deciding how to capture and build datasets. Model building can already pretty much be automated once the data is in a good place by AutoML. KaizenML considers all parts important. The data should be good, the software should be automated and maintainable, and the modeling should be accurate, explainable, and easy to upgrade and redeploy. It's the best of both worlds of the two approaches that seeks to always improve in all aspects of the ML system.
