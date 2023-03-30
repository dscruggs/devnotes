# Chapter 5: Feature Engineering

Much of the performance of a model depends on finding the right features rather than things like hyperparameter tuning

This chapter goes over common techniques and important considerations for feature engineering

## Learned Features vs Engineered Features

Deep learning has led to some reduction in the amount of feature engineering required, but it has not solved all the problems. Feature engineering is still required in most cases.

Most ML models in production are not deep learning, so in those cases feature engineering are definitely required

`n-gram` is a common NLP tokenization technique to make features out of text that was very popular before deep learning. It involves several steps:

- lemmatization, expanding contractions, removing punctuation, conversion to a vector, etc.

Feature engineering requires domain specific knowledge and techniques. It's also an iterative, brittle process because you need to remember all the steps

Deep learning has led to NLP just involving tokenization, creating a vocabulary, and converting each words into one-shot vectors. Models can learn to extract features from these without all the preprocessing of before

Computer vision is the same way, in many cases you can input raw images to models

ML models usually need data beyond text and images

**Example:** supplementary features for a spam detector for comments (more than just the text of the comment)

- the comment metadata
  - how many upvotes and downvotes?
- the user who posted the comment
  - when was this account created,
  - how often do they post
  - how many upvotes/downvotes?
- the thread in which the comment was posted
  - how many views does it have?

The process of choosing what information to use and how to extract this information into a format usable by your ML model is `feature engineering`

The # of features can go up to millions for some models

You may need subject matter experts to help develop useful features

## Common Feature Engineering Operations

There's many repeated tasks that have been streamlined in feature engineering. this section discusses several of those common tasks and consideration

### Handling Missing Values

Values are always missing in production. But not all types of missing values are equal

There's three types of missing values

- `Missing not at random (MNAR)` - this is when the reason a value is missing is because of the true value itself
  - Ex: survey respondents didn't disclose their income. It may turn out that the income of individuals who did not disclose is higher than those that did. Meaning the values are missing for reasons related to the value itself
- `Missing at random (MAR)` - this is when a value is missing is not due to the value itself, but due to another observed value
  - Ex: the age values are often missing for male respondents. So this may be that male respondents in the survey don't like disclosing age
- `Missing completely at random (MCAR)` - This is when there's no pattern in when the value is missing
  - Ex: people forgetting to fill in values for no particular reason. This type of missing values is very rare

Missing values usually have a reason behind them and should be understood before making decisions on how to handle them

Missing values can be handled either by imputation or deletion

#### Deletion

This is just deleting missing values. Many people prefer this simply because it's easy. There's a few ways to do it

##### Column deletion

If a variable has too many missing values, just delete the column

The drawback here is you might remove important information from the model

Ex: remove marital status from the model with many missing values, but this is highly correlated with buying houses

##### Row deletion

If a sample has missing values, remove the sample

This can work if the missing values are completely random (MCAR) and its a small subset of the data (less than 0.1%). But this is a bad idea if its something like 10% of the data

Removing rows of data can also remove important information that your models need to make predictions. Especially if they're not MCAR. The missing values themselves might have signal and value to offer

Removing rows of data can create bias in your model, especially if they're MAR. This can result in the total removal of certain values of features

#### Imputation

Deleting values can cause information loss. You can impute missing values (fill them in with certain values), but figuring out what the right values are is challenging

Some methods used:

- Filling values with defaults
- Filling in empty strings instead of missing
- filling in with mean, median, or mode

Filling in values can cause debugging issues if the inputs to the model change such that values that should no longer be there are still being filled in by the model (ex: front end removed a survey response)

In general you should avoid filling in missing values with possible values. This allows extra signal to be added by the value being missing and prevents throwing the model off by entering in valid info in cells where there's no info (unless it make sense, like average temperature)

There's no perfect way to handle missing values:

- deletion risks losing important data
- imputation risks introducing bias or noise to the model, or can cause data leakage

You can use multiple techniques, most important thing is to understand why the values are missing and choose the right method to deal with it

### Scaling

Ex: consider the task of predicting whether someone will buy a house in the next 12 months

Age ranges for 20 to 40, but annual income ranges from 10k to 150k.

The model may give more weight or importance to the annual income simply because it's a bigger number, regardless of which is more useful for generating predictions

`feature scaling` is the process of making sure features are scaled to similar ranges to mitigate this problem

- gradient boosted trees and logistic regression are especially sensitive to non-scaled features

Types of feature scaling:

- `min-max scaling` is used to get the values in between 0 and 1 depending on the position of the number in between the min and max values. 0 would be the min and 1 would be the max
  - you can also scale this way to an arbitrary range of [a,b] instead of [0,1]. The author likes [-1,1]
- `standardization` is the process of scaling a variable to have a normal distribution. This is useful if you think your variable might also have a normal distribution. Normalization would be mean 0 variance 1

Dealing with distribution skew of features

- `log transformation` - reduces skew of some data and can be helpful. Other methods exist and analysis on features that have been transformed this way should be done carefully

Issues:

- feature scaling is a common source of data leakage
- it often requires global statistics which you often don't have while making an ML model (you have to know the min, max, mean, etc. of all your data before you make the model and before you see inference data). This is why retraining is so important because distributions and data changes

### Discretization

The author has rarely found this practice useful. It's putting a continuous variable into buckets. Also called `quantizing` or `binning`

This reduces the number of possible categories the model has to learn from infinite to a few, which is an easier learning task

The downside here is discontinuities are introduced at boundaries. $34,999 will be treated completely different than $35,000 if you bin those separately. Choosing boundaries is not that easy. Histograms, percentiles, and SME help can help make these if necessary

### Encoding Categorical Features

Static categories are easy to handle: just give them a number and you're done

In production, most categories change

#### Illustrative Example

An amazon recommender model uses product brand. There's over two million brands on amazon even back in 2019. Training data likely contains different brands than production data as well

You encode all values in the training data 0-2 million and deploy. You get an error because the model sees values it's never seen before

You encode all values the model hasn't seen UNKNOWN and the model works, but sellers complain their brands are not getting any traffic (because there was no unknown in the training set)

You encode 99% of the most popular brands with numbers and make the bottom 1% UNKNOWN

Now your model works for an hour, but click through rate plummets after new brands join the site. Some of them are luxury brands and some are knockoffs, but your model is treating them all the same as UNKNOWN

How do you fix this problem?

#### Hashing

Method popularized by the package Vowpal Wabbit developed by microsoft

You use a hash function to generate a hashed value of each category. The hashed value will become the index of that category. Because you can specify the hash space you can fix the number of encoded values for a feature in advance. without having to know how many categories there will be

Hash space of 18 bits corresponds to 262k possible values. So all values, even the ones that your model has never seen before, will be encoded by an index between 0 and 262k

Hashed functions have collision issues: two categories being assigned the same index. But, with many hash functions the collisions are random, and so new categories can share an index with any of the existing brands instead of always sharing an index with unpopular brands. This is what happens when we used UNKNOWN

Impact of colliding hashes is not very bad. But you can choose large hash spaces can help reduce these odds.

Hashing functions like `locality sensitive hashing functions` exist to make sure similar categories are hashed into values close to each other

Academics consider this hacky but its widely used in production today. This is especially useful in continuous learning in production

### Feature Crossing

Technique to combine two or more features to generate new features

Useful to model nonlinear relationships between features. for example, combining a "married" and "children" column to show "married w/ kids", "single w/ kids", etc.

These kinds of operations are essential for models that are bad at learning nonlinear relationships such as:

- linear regression
- logistic regression
- tree-based models

Less important to neural networks but it can still help to get them to learn nonlinear relationships faster

Drawbacks:

- makes the feature space of the model blow up
  - combining features A and B with A having Z values and B having Y values will give Z x Y possible values. Ex: 100 x 100 = 10000 values
    - need a lot more data to learn these values and relationships
  - Because this increases the number of features the model uses, it can more easily overfit training data

### Discrete and Continuous Positional Embeddings

Positional embeddings have become standard data engineering technique for many applications both in computer vision and NLP

`Embeddings` are representing a string or other object as vector. It's common in NLP but also can be used for many different use cases. Multimodal embeddings are in development. All embeddings generated by an algorithm for a type of data is called an `embedding space` and these are vectors of the sample length

#### Language Modeling Example

The task of predicting the next token in a sequence based on previous sequence is a common one.

A sequence length can be 512 or more in practice, but for example we'll go with 8

Recurrent neural networks process tokens in order, but transformers process data in parallel so words' positions need to be explicitly inputted so our model knows the order of the words

Inputting absolute positions (0,1,2, etc.) would present a feature scaling problem we discussed before

Rescaling positions between 0 and 1 would make the differences between two positions too small for the network to easily learn

Instead, we can treat position embeddings the same way we treat word embeddings. With word embeddings we use an embedding matrix where the columns are the vocabulary size and each entry in the column is the embedding for the word at the index of the column

In positional embedding cases the number of columns is the number of positions in the sequence

The embedding size for positions is usually the same size as the embedding for the words so they can be summed. So the embedding for the word "food" at position 0is the sum of the embedding vector of the word "food" and the embedding vector at position 0 (this is how BERT works)

Embeddings change as the model weights get updated, so the positonal embeddings are also learned

#### Fixed positonal embeddings

Fixed positional embeddings exist. In the original transformer paper they used fixed positonal embeddings with a predefined function for each element in the embedding vector of length S. Sine for even indexes and cosine for odd indexes was used in Attention is All You Need

This is a special case of `Fourier Features`. If positions in positional embeddings are discrete, fourier features can also be continuous

For example, when representing 3D objects where each position on the surface of teapot is a 3D vector, which is continuous. Building an embedding matrix with continuous column indexes for positions is hard, but fixed embeddings using sine and cosine are still possible

Fourier features have been shown to improve performance for tasks that take coordinates as inputs. **"Fourier Features Let Networks Learn High Frequency Functions in Low Dimensional Domains" (Tancik 2020)**

## Data Leakage

This is the phenomenon when a form of the label "leaks" into the set of features used for making predictions, and this same information is not available during inference

Examples:

- For a model to predict lung cancer you get data from hospital A and evaluate it successfully. Then performance on images from model B is poor
  - it turns out at hospital A when patients have serious risk of illness they go to a special machine, meaning the image is different and the model picked up on this difference

Data leakage is very tough to find and can effect even the most experienced ML engineers

### Common Causes for Data Leakage

#### Splitting Time-correlated data randomly instead of by time

You won't have future data in production, so don't train models with it

Stock prices is an easy example of this

A nonobvious example: in a music recommendation system including samples of music from the same day as you have in your test set. Music preferences can be influenced by global trends that will leak into your training data.

Split by time whenever possible, and train on periods before your test set (ex: train on first 4 weeks of data, test/eval on 5th)

#### Scaling Before Splitting

Using global statistics from your whole dataset is a luxury you won't have in production, so you may get unrealistic performance in evaluation if you include it in your scaling process with the training data. The mean, variance, etc. of the test set will leak into the training set

Always split data first before doing any kind of scaling. Some suggest even splitting before doing any EDA so as to not gain information about the test split

#### Filling in missing data with statistics from the test split

Similar to scaling, by filling in missing values using statistics from the test set you can leak this knowledge into the training set

Split data first and only statistics from the train split to fill in missing values to avoid this

#### Poor handling of data duplication before splitting

Duplicated data is very common in industry, and failure to remove or do something about this data can result in leakage to the training set with duplicates ending up in both the train an test sets

Data processing can also cause this (ex: oversampling)

Always check for duplicates before splitting and after splitting just to make sure. Also only do oversampling or data augmentation after splitting

#### Group Leakage

A group of examples having strongly correlated labels but divided between the train and test sets can be a problem (ex: a patient gets two lung cancer scans a week apart and 1 ends up in train and the other in test set)

Understanding how your data is generated and watching out for this is the best way to deal with this

#### Leakage from the data generation process

The example about the lung cancer model from earlier where high risk patients got special scans is an example of this. It's hard to catch without knowing specifics about how data is collected

Mitigate the risks of this by keeping track of the sources of your data and understand how it's collected and processed

Normalize data so that the data from different sources can have the same mean and variance

If different sources have different resolutions, normalize the resolution

Incorporate SMEs into the ML design process because they'll have more insight into how the data is generated and collected

### Detecting Data Leakage

Data leakage can happen during many steps: data generation and collection, sampling, splitting, processing, feature engineering, etc. You need to monitor this problem throughout the lifecycle of the project

Measure the predictive power of each feature or set of features with respect to the target variable. If any feature has unusually high correlation, investigate how that feature is generated and whether the correlation makes sense. It's possible that two features independently don't contain leakage, but together do. Ex: start and end data for a model to predict employee churn

Do `ablation studies` to measure how important a feature or set of features is to your model. If removing a feature causes model performance to diminish significantly, investigate why

- with many features ablation may be impractical, but you can still do this on a subset of features you suspect the most (SME help is useful here)
  - this can be done offline, so your own machines can be used for this during downtime

Keep an eye out for new features added to the model and measure their effect (if it significantly improves performance investigate why, is it really good or is there leakage?)

Be careful every time you look at the test split. Using this in any way other than to report the model's final performance (ex: coming up with ideas for new features, tuning hyperparameters) risks leaking info into the training process

## Engineering Good Features

Generally, the more features added to the model leads to better model performance. Usually the list of features for a model only grows over time

However more features doesn't always mean better performance, and having two many features can be be a problem both in training and inference:

- the more features you have, the more opportunities there are for data leakage
- too many features can cause overfitting
- too many features can increase the memory required to serve a model, which may increase costs
- too many features can increase inference latency when doing online prediciton
  - especially if you need to extract these features from raw data for predictions online. (they go deeper into online prediciton in chapter 7)
- useless features become technical debt
  - whenever the data pipeline changes, all the affected features need to be adjusted (ex: removing age, now all age-related features need to change)

In theory things like L1 regularization help by reducing feature weights that don't help performance to 0

In practice removing useless features might help the model learn faster

You can store removed features for later use. You could store the actual data, or just the definitions and share them across the company. Feature stores are used for this, but feature definition management is not supported by all feature stores

Two factors to consider when evaluating whether a feature is good for a model:

1. importance to the model
2. generalization to unseen data

### Feature Importance

There's many methods to assess feature importance, depending on the model used

Classical ML models like boosted gradient trees have built in feature importance measures

**InterpretML** is an open source library to help with feature importance and understanding how your model makes predictions

Intuitively, a feature's importance to a model is a measurement by how much that model's performance deteriorates if that feature or set of features is removed from the model

- `SHAP` is a model-agnostic feature importance measure
  - SHAP measures not only how important a feature is to an entire model, but also how important it is to specific predictions

Often a small number of features accounts for a large portion of your model's feature importance. Facebook found their top 10 features for ads was responsible for about half of the model's performance. The last 300 account for less than 1% of feature importance

Feature importance techniques are also great for interpretability

### Feature Generalization

Features should generalize to unseen data, otherwise it's useless

Measuring feature generalization is a lot less scientific than measuring feature importance. It requires intuition and SMEs in combination with statistics

Two things to consider: feature coverage and distribution of feature values

#### Feature Coverage

This is the percentage of the samples that have values for this feature in the data. So the fewer values missing, the higher the coverage.

Usually features with low coverage are not very useful, even if they do have predictive power

However, if missing values in features aren't random, coverage may not be very important for that feature

If coverage varies dramatically between train and test sets, you may be pulling from different distributions and this should be investigated. Coverage will vary widely between different slices of data though

### Distribution of Feature Values

If the set of values that appears in the seen data has no overlap with the set of values in the unseen data this feature may hurt model performance

Ex: training a model to predict taxi rides and using day of the week as a feature but retraining every 7 days, so your training data has monday-saturday and test data has sunday. This feature has 100% coverage because no values are missing, but will not perform well and may even hurt the model performance. You'd need a clever encoding scheme for this feature to work. HOUR of the day would be a good feature here!

There's a trade off between model generalization and specificity. For example making HOUR_OF_DAY into an IS_RUSH_HOUR flag is more generalizable because it will cover all possible values, but its less specific, and if you don't also include HOUR_OF_DAY in the model you're losing that extra information that may help the prediction
