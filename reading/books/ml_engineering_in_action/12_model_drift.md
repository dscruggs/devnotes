# Chapter 12: Holding on to your gains by watching for drift

This chapter goes over drift monitoring and detection, and how to handle a response to model drift

## Detecting drift

Entropy is all around us. Data changes over time based on our actions and latent factors outside of our control

Therefore, models need to be retrained

### What influences drift?

| Drift Type       | Measurement Method                                                                                                               | Corrective Actions                                                                                 |
|------------------|----------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| Feature Drift    | Feature distribution validation, prediction post hoc error calc                                                                  | Retrain on new data, revisit feature engineering                                                   |
| Label Drift      | Post hoc analysis of predictions                                                                                                 | Retrain on new data, tune                                                                          |
| Concept Drift    | Attribution measurement, feature distribution validation, post hoc prediction analysis, ad hoc analysis, causality models (sims) | Feature engineering work, retraining, revisit solution / approach of model                         |
| Prediction Drift | Post hoc analysis of predictions, attribution measurement                                                                        | Analyze impact on business                                                                         |
| Reality Drift    | No real measurement. Lots will be wrong                                                                                          | Human intervention, reevaluate featuers, create hard-stop boundary in training data, retrain model |
| Feedback Drift   | Time spent improving model, performance during retraining                                                                        | Check efficacy of solution / determine if new solution is needed                                   |
