# Chapter 4: Training Data

This chapter will go over how to handle data from a data science instead of a systems perspective (chapter 3)

Building models is fun, wrangling data is hard. But data can sink your whole ML operation if not handled properly.

This chapter goes over how to obtain or create good training data. Like other ML modeling steps, creating training data is an iterative process. And a training dataset should not remain static for long in production

- Data has biases. use it but don't trust it too much

## Sampling

Sampling methods are used for many things. Sampling here is discussed for training data purposes but can be used for many cases.

There's two families of sampling: nonprobability sampling and random sampling

### Nonprobability Sampling

This is when the selection of data isn't based on any probability criteria

- Convenience sampling
  - Samples of data are selected based on availability. popular because it's **convenient** :)
- Snowball sampling
  - future samples are selected based on existing samples
  - Ex: to scrape legitimate twitter accounts w/o having access to twitter DBs, you can start with a small number of accounts and then scrape accounts they follow
- Judgment sampling
  - Experts decided on samples
- Quota Sampling
  - You select samples based on quotas for certain slices of data without any randomization
  - For example, when doing a survey, you might want 100 responses from each of the age groups: < 30, between 30 and 60, > 60. Regardless of the actual distribution

These methods selected by nonprobability criteria are not representative of the real-world data and have much selection bias.

- It's a bad idea to use nonprobability data to build ML models with, but many models are driven by convenience so it may be necessary:
  - Language models often use what's available on the internet instead of a sample of all text
  - sentiment analysis uses things like amazon or IMDB reviews which don't represent people who don't leave reviews
  - self driving cars data mostly came from arizona and california, where snow isn't common

Nonprobabilty can be a good way to get your project off the ground running, but for reliable models probability based sampling is much better

### Simple Random Sampling

- All samples get equal weight and you select x number of samples (e.g. 10% chance of selecting each of 10 items)
- pro: easy to implement
- con: may miss out on rare classes, so models won't know they exist

### Stratified Sampling

- First divides population into groups you care about and then randomly sample from each group separately. This ensures there's at least 1 item from each group
- Each group is called a stratum
- When samples are impossible to divide into groups this becomes hard, for example in multilabel cases or complex data

### Weighted Sampling

- each sample is given a different weight for their probability of being selected
- this method allows you to leverage domain expertise (for example if more recent data is more valuable then you can weight based on time)
- This also helps when you know your data comes from a different distribution than the true data. You can give underrepresented or overrepresented classes different sampling weights
- This differs from `sample weights` in ML where you give different weights to different samples during training that changes how that sample affects the loss function

### Reservoir Sampling

- Useful for streaming data
- Illustrative example:
  - you have an incoming stream of tweets, don't know how many there are but want to sample *k* of them to do analysis on.
  - You must ensure that
    - every tweet has an equal probability of being selected
    - You can stop the algorithm at any time and the tweets are sampled with the correct probability
- Algorithm:
    1. Put the *k* first elements into the reservoir
    2. for each incoming *n*th element, generate a random number *i* such that 1 <= *i* <= *n*
    3. If 1 <= *i* <= *k*: replace the *i*th element in the reservoir with the *n*th element. Else, do nothing
- Each incoming *n*th element has k/n probability of being in the reservoir. Each element in the reservoir also has the same probability of being in there. This can be stopped at any time

### Importance Sampling

This allows us to sample from a distribution when we only have access to another distribution

- If you have a distribution P(x) that's expensive or impossible to sample from, but another Q(x) that you can use, you can sample x from Q(x) and weight it by P(x)/Q(x) and in expectation they should be the same. Q(x) is called the `importance distribution` or the `proposal distribution`
- Policy-based RL uses this
  - Calculating the total reward of taking an action is costly, but if the new policy is relatively close to the old policy you can calculate the total rewards based on the old policy instead and re-weight them according to the new policy. The rewards from the old policy make up the proposal distribution

## Labeling

- labeling is just as, if not more important, than ML engineering. There is no good model without good labels.
- Head of AI at tesla told their recruiter they'd need an in-house labeling team as long as there was an engineering team

### Hand Labels

Acquiring hand labels is difficult

- it's expensive, especially when SMEs are required
  - spam emails are easy to label. Chest X-rays not so much
- There's a threat to data privacy
  - someone's looking at the data. This makes it difficult to transport and manage the data in the labeling process
- it's slow
  - it takes a long time to get the right labels (weeks, months, years sometimes)
  - slow labeling leads to slow iteration speeds and makes the model less adaptive to change
  - any change in relabeling can set the project back weeks
    - adding labels is expensive
    - splitting up categories is expensive
    - Data collection if there's not enough of a new category is expensive
  - if your model is in production and you need new data, the longer this takes the more the performance will degrade

#### Label multiplicity

- to get labeled data companies often rely on different data sources and labelers with varying levels of expertise
- `label multiplicity` or `label ambiguity` is the problem of having multiple conflicting labels for a data instance
- this is a big problem that only gets bigger as the level of domain knowledge needed to label data increases

How to combat this?

- Have a clear problem definition. Clarify obvious cases of disagreement and be consistent. Conduct training prior to labeling or after a few samples have been labeled to clear these things up before costs grow

#### Data Lineage

- Using data from multiple sources can get confusing. And one bad data source can cause a model to mysteriously fail. There needs to be a way to track where data came from so bad data can be removed
- `data lineage` is the process of keeping track of where data and labels originated from

### Natural Labels

- `Natural labels` is data with labels that naturally exist already in the data, which makes for easy model building
- Examples:
  - google maps route time estimation (when people take trips they generate labels)
  - stock price prediction
  - recommender systems (you can derive lots of labels from recommendations depending on user actions)
    - Natural labels inferred from user behavior are known as `behavioral labels`
- You can set up ML systems without natural labels to collect them over time
  - a "like" button for facebook news articles to decide ranking
  - allowing alternative user input for translation
- Natural labels are not that uncommon, 63% of companies surveyed said they work with tasks with natural labels
- `implicit labels` can be inferred when users take no action, giving negative feedback
- `explicit labels` are when users explicitly downvote

#### Feedback loop length

- feedback loop length for tasks with natural ground truth labels is the time it takes from when a prediction is served until when the feedback on it is provided
- tasks with short feedback loops are tasks where labels are available within minutes
  - ex: website clicks on recommendations
- Feedback loop can be hours on some use cases (ex: user orders something but has to try it on first, taking weeks)
- Types of feedback
  - There's feedback that is aligned more with volume of feedback, like clicks
  - There's feedback that is aligned more with the strength of signal, like purchases
  - Both are valid approaches and depend on the use cases and outcome goals of the project
- Setting the feedback window is a trade off
  - short windows means you can capture more data and find issues in the model faster, but you may miss out on delayed clicks or purchases that may have shown a recommendation to be good
    - There will always be missed labels. Try to use distributions and data to set your window
  - long windows, such as in fraud detection, may take weeks or months for labels to materialize
    - these are good for quarterly or yearly reports, however are not helpful if you want to catch issues with your model ASAP because feedback cycles take weeks

### handling the Lack of Labels

There's techniques made to help with the usual lack of plentiful high-quality labels: weak supervision, semi-supervision, transfer learning, and active learning

| Method | How | Ground truth required? |
| - | - | - |
| Weak supervision | Leverages (often noisy) heuristics to generate labels | No, but a small number of labels are recommended to guide the development of heuristics |
| Semi-supervision | Leverages structural assumptions to generate labels | Yes, a small number of initial labels as seeds to generate more labels |
| Transfer learning | Leverages models retrained on another task for your new task | No for zero shot learning, Yes for fine-tuning, though the number of ground truths required is often much smaller than what would be needed if you train the model from scratch |
| Active learning | Labels data samples that are most useful to your model | Yes |

#### Weak Supervision

- using heuristics made by experts to label data instead of hand labeling using something called a `labeling function (LF)`
- Common Heuristics:
  - `Keyword heuristic` - using keywords in text or otherwise
  - `Regular expressions`
  - `Database lookup` - using values from a database (ex: dangerous diseases)
  - `The outputs of other models`
- Drawbacks
  - heuristics are noisy. So training data made from this method is noisy
  - multiple heuristics might apply to the same label and you have multiplicity issue
- It's important to combine, denoise, and reweight all LFs to get a set of most likely to be correct labels
- A small number of hand labels to validate accuracy of LFs is recommended
- LFs can be used to label and train a model, then that model can be used to label more data

Hand labeling vs programmatic labeling

| Hand Labeling | Programmatic labeling |
| - | - |
| Expensive: Especially when SMEs required | Cost saving: Expertise can be versioned, shared, and reused across an org |
| Lack of privacy: Need to ship data to human annotators | Privacy: Create LFs using a cleared data subsample and then apply LFs to other data without looking at individual samples |
| Slow: time required scales linearly with number of labels needed | Fast: Easily scale from 1K to 1M samples |
| Nonadaptive: Every change requires relabeling the data | Adaptive: When changes happen, just reapply LFs |

#### Semi-supervision

Uses structural assumptions to generate new labels based on a small set of initial labels. Unlike weak supervision this does require initial labels

Comprehensive survey of methods are in "Semi-Supervised Learning Literature Survey" and "A Survey on Semi-Supervised Learning"

`Self Training:`

1. start by training a model on existing set of labeled data
2. then predict on new data and add samples with high probability to your training data
3. Retrain on new training data and repeat until you have enough samples / are happy with model performance

`Similarity:`

- assumes data samples that share similar characteristics share similar labels
- for example using keywords generated from existing labeled samples
- similarity is usually only discoverable by complex methods like clustering in most cases (k-means,k-nearest neighbor)

`Perturbation-based:`

- assumes small perturbations to a sample shouldn't change its label
- apply small perturbations to training samples and keep the same labels, this generates new data

Semi-supervised approaches have reached performance of purely supervised performance in some cases, but many labels have to be discarded

This is most useful when the number of training labels is limited. A decision has to be made on how much of limited training data should be used to train models vs evaluate candidate models

A popular method is to keep a large evaluation set, pick the best model, then train the best model also on the evaluation set so there's more data

#### Transfer Learning

This is a family of methods where a model developed for one task is reused as the starting point for a model on a second task

1. Train model on a base task (usually task with cheap, abundant training data)
2. Fine tune or zero-shot on a new task (downstream task)

Language modeling is a classic use-case because it doesn't require labeled data (with next-token prediction training). Downstream tasks include: sentiment analysis, intent detection, question answering

`Zero shot learning` is using this model as-is on a new task
`Fine tuning` is further training the base model or part of the base model for a new task

Sometimes you need to modify the inputs to fit a template for a given downstream task, such as a question answering model you need to structure the training data as question, answer

Benefits of transfer learning:

- less labeled data requirements (lower cost to collect data)
- usually has better performance even with sufficient data
- Useful in many different applications including NLP and computer vision

The trend in recent years has been the larger the pretrained base model, the better the performance on downstream tasks. But large models are expensive to train. **GPT-3 is estimated to have cost tens of millions to train**!!

Takeaway: use pretraining when you can regardless of data availability

#### Active Learning

Method for improving the efficiency of data labels. Idea: ML models can achieve greater accuracy with fewer training labels if they can choose which data samples to learn from.

**Process:** A model (active learner) sends back queries in the form of unlabeled samples to be labeled by annotators (humans usually)

Instead of randomly labeling data samples, label the samples that are most helpful to the model according to some metrics or heuristics.

Measures of helpfulness:

- uncertainty: label the samples the model is least certain about, hoping they will help the model the most
  - in cases where models output raw probabilities, can pick the ones with the lowest probability
- disagreement among candidate models (`query by committee`): have multiple candidate models (usually the same models trained on different settings and hyperparameters or different data) and label the samples the model committee disagrees on the most
- "Active Learning Literature Survey" (Settles 2010) for more examples

The data to be labeled can come from anywhere:

- data already collected but unlabeled
- can synthesize data where the model generates samples in the input space its most uncertain about
- can use real-world data on a deployed model (this allows for faster adaption to changes in inputs in production)

## Class Imbalance

This is the problem of there being a substantially different number of training samples for each class in a dataset (ex: 99% non spam, 1% spam)

Happens in both regression and classification tasks (need to predict high values just as well as the median)

### Challenges of Class Imbalance

ML and especially deep learning works well in situations where the data distributions are more balanced. There's a few reasons why:

- Class imbalance often means there's insufficient signal for your model to learn to detect the minority classes
  - For minority classes the model is doing a few-shot learning problem.
  - In extreme cases the model may not even know they exist if the class is missing
- It's easier for models to get stuck in a nonoptimal solution by exploiting a simple heuristic instead of learning anything useful about the underlying patterns of the minority classes
  - If the minority class is only 1% of the data the model can get 99% accuracy only predicting the majority class
- There's a asymmetric costs of error: the cost of a wrong prediction on a sample of a rare classes might be much higher than a wrong prediction on a sample of the majority class
  - Ex: if you're predicting lung cancer a false positive is much less problematic than a false negative
  - If you don't weight the loss function the model may perform equally on all classes, where you may way it to perform better on some than others

#### Causes of Class Imbalance

Class imbalance is the norm in the real world.

1. Many tasks are based around detecting rare events
    - Churn prediction
    - Fraud detection
    - disease screening
    - spam detection
    - resume screening
    - Object Detection: currently works by generating a bunch of bounding boxes in an image and predicting which is most likely to contain objects. Most bounding boxes don't have objects
2. Biases during the sampling process
    - In the case of spam detection, your company's database might already filter out spam emails so there's not enough to build a model on
3. Labeling errors
    - Annotators might have read the instructions wrong or followed the wrong instructions and didn't know all the classes are options
    - Labeling errors en masse can cause class imbalance

Always examine the data when dealing with class imbalance to understand the cause of it

### Handling Class Imbalance

The effects of class imbalance depends on the problem

- It's been shown that the sensitivity to class imbalance increases with the complexity of the problem.
- Noncomplex, linearly separable problems are unaffected by all levels of class imbalance
- Binary classification problems are much easier to deal with class imbalance than multiclass problems
- Deep neural networks (>10 layers) tend to perform better with class imbalance

Some argue you shouldn't try to fix class imbalance and that should be part of the model's learning. But you need a very complex model to handle this imbalance properly. So techniques exist to help with this

Three approaches to class imbalance are covered here: choosing the right metrics for your problem, data-level methods, and algorithm level methods

Comprehensive list in **"Survey on Deep Learning with Class Imbalance" (Johnston and jhoshoftaar 2019)**

#### Using the Right Evaluation Metrics

Wrong metrics give you the wrong idea of how your model is doing. So they can't help you develop, select, or improve your model

Overall accuracy and error rates are commonly used metrics, but these are insufficient metrics for tasks with class imbalance. Majority classes will dominate these metrics since it treats them all equally (Ex: the 99% accuracy example where the model only predicts one class but it makes up 99% of the data)

Metrics that help you understand your model's performance with respect to specific classes are better choices:

- F1
- precision
- recall
- ROC curve (true positive rate against the false positive rate for different thresholds)
- Precision-recall curve

You can treat regression problems as classification problems and bucket predictions into groups, then get metrics on those specific groups!

#### Data-level methods: Resampling

Modify the distribution of the training data to reduce the level of imbalance

Resampling is a popular method which includes:

- oversampling (adding more instances from minority classes)
  - making random copies of minority data is the easiest method to do this
  - `SMOTE (synthetic minority oversampling technique)` samples convex combinations of existing data points within the minority class to create new samples
- undersampling (taking out samples from majority classes)
  - randomly removing samples is the easiest method here
  - `Tomek links` (removing majority samples closest to minority samples) is one method but this can hurt the model's ability to learn subtleties of the decision boundary

Both SMOTE and Tomek links have only proven effective in low-dimensional data. Many sophisticated resampling techniques like Near-Miss require expensive calculations for high-dimensional data

**Never evaluate your model on resampled data**. It will cause the model to overfit to that resampled distribution

Drawbacks of resampling:

- Oversampling can overfit the training data
- Undersampling can risk losing important data to the model

Mitigating these drawbacks:

- `two-phase learning`
    1. First train on resampled data by randomly undersampling large classes until each class has *N* samples
    2. Fine-tune model on the original data
- `dynamic sampling`
  - Oversample the low-performing classes and undersample the high-performing classes during training
  - Shows the model less of what it's learned and more of what it hasn't

#### Algorithm-level Methods

Data level methods alter the distribution of the data. Algorithm level methods keep the distribution intact but alter the algorithm to make it more robust to class imbalance

Many algorithm level methods involve modifying the loss function since this guides the learning process.

Standard loss functions typically weight all sample losses equally. There's ways to modify this:

- `cost sensitive learning` - manually defined cost matrix that gives different weights to different wrong predictions depending on which class was supposed to be predicted
  - This lets you weight the cost of a false negative higher than a false positive for example
  - drawback is these weights have to be manually entered and the cost could vary depending on problem or scale
- `Class balanced loss` - Setting the weight of each class to be inversely proportional to its presence in the training data. This means minority predictions will carry a higher weight than majority predictions
  - More sophisticated versions can take into account the overlap among existing samples, such as class balanced loss based on the effective number of samples
- `Focal Loss` - Adjusting the loss to give a higher weight to samples the model thinks it will have problems learning (lower probability of being correct)

Ensembling can also be used to combat class imbalance but that's typically not why they're used, and these other methods are more straightforward to implement

## Data Augmentation

This is a family of techniques used to increase the amount of training data. It can also make models more robust to adversarial attacks and noise

Computer vision uses this as a standard practice but it's becoming more common in NLP as well

### Simple Label-Preserving Transformations

Randomly modify data but preserve the label. This can double or triple your training data without much effort

- Computer vision
  - cropping, flipping, rotating, inverting, erasing, etc. are common techniques. Most ML libraries support this by default
- Natural Language Processing
  - randomly replace words with similar words as long as it doesn't change the meaning. Can be done by using dictionaries, synonyms, or words where embeddings are close to each other

### Perturbation

Also label-preserving, but it's sometimes used to make models predict incorrectly

Neural networks are sensitive to noise. Changing one pixel can ruin an image classification model.

Adversarial attacks are commonly done by adding noise. The effectiveness of adding noise increases with the resolution of the images

Adding noisy samples to training data can help improve performance and reduce the effect of noise.

- `adversarial augmentation` - augmenting an image to replicate adversarial attacks with noise
  - can do this in images by either randomly or adding noise through search. (ex: DeepFool to find the minimum amount of noise to cause a misclassification)
  - in NLP it's less common but you can do the same thing by replacing a certain number of tokens with random words. This has been shown to give a small performance boost

Chapter 6 talks about how perturbation is not just a method to increase performance but also to evaluate your model's performance

### Data Synthesis

Collecting data is slow and expensive. There's privacy concerns, etc. Synthesizing data can eliminate these problems

#### NLP Synthesis

Templates can be a cheap way to bootstrap your model

- For a chatbot could have something like "Find me a (CUISINE) restaurant within (NUMBER) miles of (LOCATION)" and use a database or collected data to generate lots of samples

#### Computer Vision Synthesis

Combining existing examples with discrete labels to generate continuous labels is one method

- For a classifier to detect dogs and cats, you can have data for dogs and cats and combine their labels with weights to produce a 0-1 score. this is called `mixup` and it's been shown to be highly effective. It's also used in GANs to stabilize training

Using Neural networks to generate training data is still an active area of reserach

**"A Survey on Image Data Augmentation for Deep Learning" (Shorten and Khoshgoftaar 2019)** has a more complete list
