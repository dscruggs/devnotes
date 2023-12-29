# Chapter 11: Model measurement and why it's so important

This chapter covers impact measurement and attribution for ML after release of the model

## Measuring model attribution

**Don't use model metrics to show performance. Use hypothesis testing or some direct measure of value to the business**

The first step in measuring attribution is measuring the right metrics on the right groups of customers to get true performance (design of experiments principles)

- work with SMEs to understand how they currently measure performance of their work
- reduce the inherent variability in your population through the use of stratification or cohorts
  - recency, frequency, and monetary value (RFM) is common in direct marketing

Focus on the business metrics. Examples of model vs business metrics:

| Project            | ML Metric                         | Business Metric                                        |
|--------------------|-----------------------------------|--------------------------------------------------------|
| Fraud Detection    | Area under PR, area under ROC, F1 | Fraud loss dollars, number of fraud investigations     |
| Churn Prediction   | Area under PR, area under ROC, F1 | recency of purchases, login events for high-churn risk |  
| Sales Forecasting  | AIC, BIC< RMSE, etc.              | revenue                                                |
| Sentiment Analysis | BLEURT, BERTScore                 | number of users, engagement rate                       |
| Ice Cream Coupons  | MAE, MSE, RMSE                    | revenue, coupon usage                                  |

### Correlation vs causation

Be clear about the differences between these two to the business and to yourself

`Correlation` is the relationship observed variables have to one another. It doesn't imply anything outside of the existence of that relationship

`Causation` is a direct causal link between two variables. And this is almost impossible to prove because of the sheer number of unobserved factors present in any real world use case

The goal of any statistical analysis is to identify relationships, and use those findings in context with many other factors to come to educated decisions. But the conclusions from correlations should always be viewed with an understanding that they aren't absolute and aren't causal

## Leveraging A/B testing for attribution calculations

Typical A/B testing is done with a control group (getting generic / business as usual predictions) and a test group (getting model predictions)

#### How much data do you need to collect?

Simple power analysis can be done to determine necessary sample sizes to prove significance (higher lift will require fewer samples). Assuming normal distribution and homogenous sample group sizes the test uses simple t test, there are other tests for other distributions / assumptions

Set expectations with the business **before** launch on how long it will take to understand significance, how you will measure it, and set up automated monitoring to accomplish this

### Evaluating Continuous metrics

There's different tests for parametric and nonparametric. There's also tests for normal and non-normal data. It's important to look at your data's distribution and choose statistical tests that match that. Examples are given here for parametric measures and discussion on temporal dependencies are had

### What level of attribution / measurements are required?

it depends on the project and ROI. Here's some rules of thumb:

- Internal tooling model for generating labeled data for other projects
  - Standard ML metrics are fine.
- Internal-to-the-company predictive model designed to assist another department with repetitive tasks
  - Attribution modeling not applicable, periodic ad hoc drift detection could be worthwhile.
- Internal-to-the-company project that directly affects critical business operations (helping to influence major business decisions)
  - Absolutely critical to have drift detection, and attribution modeling is a good idea to build.
- External customer-facing model
  - Attribution measurement, drift detection, and bias detection (evaluation of prejudicial predictions that have real-world consequences of amplifying systemic societal issues based on the nature and type of data collected) are absolutely required.

### Alternative displays and tests

Outside of standard ANOVA and student tests, `Tukey HSD (honestly significant difference)` is another test that can be used when there's lots of pairwise groups to compare (high, medium, low RFM groups for example)

### Evaluating categorical metrics

Goes over an example for evaluating "happen" vs "not happen" events using fisher and chi squared tests

## Final thoughts on attribution measurement

- your metrics are going to depend on the use case and the business. The important thing is that the metric is useful and incorporates the nuances of the business
- dicuss which metrics to use with SMEs in the area. If it's revenue, talk to finance, etc.
- tie metrics as closely as possible to the actual metrics executives and the business care about (OKRs, etc.)
