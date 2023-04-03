# Chapter 7: Model Deployment and Prediction Service

"Production" is a spectrum. For some teams it means generating plots for business users to see. For other teams it means keeping models up and running for millions of users a day

Deploying is easy if you ignore all the hard parts. Just deploy a model to a fastAPI endpoint with dependencies in a container

The hard part is making your model available to millions of users with a latency of milliseconds and 99% uptime. Also setting up infrastructure so the right person can be notified immediately when something goes wrong, then seamlessly deploying updates to fix what's wrong

Some companies have separate deployment and development teams, this can cause issues with overhead and latency in updating and fixing models

## Machine Learning Deployment Myths

### Myth 1: You only deploy one or two ML models at a time

Companies have many, many ML models. An application might have many different features, and each feature might require its own model.

Uber example models:

- ride demand, driver availability, time of arrival, dynamic pricing, fraudulent transaction, customer churn, etc.
- Different time zones, user profiles, languages, etc.
- For 20 countries and 10 models this is already 200 models

41% of organizations using ML with over 25k employees have over 100 models in production

### Myth 2: If we don't do anything, model performance remains the same

Software ages poorly. ML systems are especially problematic because not only do you have code issues, you also have data distribution issues.

ML models tend to perform best right after training and degrade over time

### Myth 3: You won't need to update your models as much

Model updates need to be fast because their performance can and will degrade over time.

Some companies update their models every 10 minutes. The goal should be the ability to update models ASAP

### Myth 4: Most ML Engineers Don't need to worry about scale

Most ML engineering roles are at large companies. This means they serve a lot of users, and you need to worry about scale

## Batch Prediction vs Online Prediction

This is a big decision when designing an ML model that will effect how users can use your model

3 Main methods of prediction:

- Batch prediction using only batch features
- Online prediction that only uses batch features (ex: precomputed embeddings)
- Online prediction that uses both batch features and streaming features (streaming prediction)

`Online Prediction` is when predictions are generated and returned as soon as requests for these predictions arrive. Also called `On Demand prediciton`

- Typically done using REST APIs. When this is done in synchronization with requests they're known as `synchronous prediction`

`Batch Prediction` is when predictions are generated periodically or whenever triggered. The predictions are stored somewhere like SQL tables or memory and retrieved as needed.
    - known as `asynchronous prediction` because they're generated asynchronously from requests

`Batch features` are features computed from historical data, such as in data warehouses or databases

`Streaming features` are computed from data in real-time transport. This differs from `Online features` which mean any features used for online prediction, including batch features

You can use both batch and streaming features in the same model. Requirements for each problem determine which to choose

Key differences between batch and on-line prediction:

| Feature | Batch Prediction (asynchronous) | Online Prediction (synchronous) |
| - | - | - |
| Frequency | Periodical, some time interval | As soon as requests come |
| Useful for | Processing accumulated data when you dont need immediate results (like recommender systems) | When predictions are needed as soon as a data sample is generated (such as fraud detection) |
| Optimized for | High throughput | Low latency |

Online prediction can be comparable in performance to batch prediction, especially when you consider that you don't need to generate predictions for all of your users with online prediction. Only those who use your service. So it can be more cost-effective in some cases

### From Batch Prediction to Online Prediction

Online predictions are natural for most people. 1 input = 1 output immediately, and its how most models are tested

#### Pros to Batch

But online predictions may be too slow for some cases in production. This is where batch predictions is useful because predictions can be precomputed. Distributed computing can compute predictions for many samples as once and store them for later

Batch can be used as a way to reduce model latency for end users for complex models

#### Cons to batch

Batch predictions can limit your ability to respond to user changes. Such as a netflix recommendation list not updating when you search for movies in the same session

You also need to know what requests to generate predictions for in advance. For a translation example you'd have to know every possible text to translate to and from

Sometimes failures of batch predictions are just inconvenience (like the netflix example) but it can be catastrophic for others. Like high-frequency trading, autonomous cars, fraud detection, etc. batch prediction just wouldn't work

#### Overcoming latency challenge of online predictions

Batch prediction is a workaround for when online prediction isn't cheap enough or fast enough

As hardware improves, so will the feasibility of all predictions being online

Things needed for overcoming latency challenges:

- a (near) real-time pipeline that can work with incoming data, extract streaming features (if needed), input them to a model, and return a prediction in near real time
  - streaming pipeline with real-time transport and a stream computation engine can help with that
- A model that can generate predictions at a speed acceptable to end users (likely milliseconds for most use cases facing consumers)

### Unifying Batch Pipeline and Streaming Pipeline

Batch prediction is largely a product of legacy systems like spark and mapreduce

One cause of differences in pipelines is for streaming features oftentimes during training they're computed as batch features. Like for example calculating average speed of all cars on your path in google maps in the last 5 minutes. In training you may use the last month as an example

Having two different pipelines to process the data is a common cause of bugs in ML systems. Especially when they're maintained by 2 different teams (development batch pipeline and productions streaming pipeline)

Companies have tried to unify these pipelines using tools like Apache Flink or using feature stores to ensure consistency in features used in testing and production

## Model Compression

A real time data pipeline isn't enough for fast ML inference. You need fast models too

Three main approaches to reducing model latency:

- Make it do inference faster
- Make it smaller
- Make the hardware its on run faster

Making a model smaller is called `model compression`

Methods on this are discussed below. It's still an active area of research

### Low-Rank Factorization

The idea behind this is to replace high-dimensional tensors with lower-dimensional tensors.

One type is called `compact convolutional filters` where the overparameterized convolutional filters (too many parameters) are replaced with compact blocks to reduce the number of parameters and increase speed
    - ex: reducing a 3x3 convolution to a 1x1, refactoring the way convolutions are done, etc.

Very use-case constrained on where this can be used

### Knowledge Distillation

Method where a small model (student) is trained to mimic a larger model or ensemble of models (teacher). The smaller model is deployed, and you can get smaller models with only small performance hits
    - DistilBERT was 60% faster and 60% the size of BERT while still having 97% of its capability in language understanding

### Pruning

Originally used for decision trees, removing uncritical or redundant sections of trees

Has two meanings in neural networks:

- Removing sections of nodes
- reducing unused parameters of models to 0
  - makes models more sparse which means they can use less storage space without changing the network architecture

Pruning can introduce bias into your model, but its generally considered effective at reducing size while retaining performance

### Quantization

Most general and commonly used compression method. This is using fewer bits to represent model parameters

Ex: using 16 bits instead of 32 bits to represent parameters (32 is usually the default)

- Can reduce memory footprint by half

Quantization reduces memory footprint but also increases speed of computation. Used mostly in inference for now but there are efforts to introduce this to training to fit bigger models on the same hardware

Cons:

- you can't represent as many values with fewer bits. You need to clip and round

## ML on the Cloud and on the Edge

`On the cloud` means the bulk of the computation is done on cloud resources

`On the edge` means that the bulk of the computation is done on consumer devices (browsers, laptops, smartwatches, etc.)

Cloud deployment is much easier, but it comes with a drawback of cost. Many companies are trying to transition to edge devices to save on cloud costs

Edge computing benefits:

- allows your models to run where cloud cannot (no internet or unreliable connections)
- don't have to worry about network latency
- easier to handle sensitive data

You need to consider memory, computation, and power consumption when thinking about edge computing with ML models

### Compiling and Optimizing Models for Edge Devices

ML frameworks have to be supported by the hardware vendor the model will be running on

- CPU, GPU, TPUs all have different ways of handling computations and software. And there's different architectures for each

There's `Intermediate Representations (IR)` you can use that converts your code into instructions usable generally by many types of hardware. This usually involes converting your neural network to a computation graph. This process is also called `lowering` where you lower your code into low-level hardware-specific code

#### Model Optimization

"lowered" code may run on hardware, but may not be efficient

Using multiple libraries for your models can cause slowdowns when translating data across frameworks (ex: pandas to numpy to pytorch)

`Optimization engineers` are often hired to help with this issue and hand-optimize code for specific hardware, but they are hard to come by and expensive.

There's two ways to optimize your ML models: locally and globally.

- Locally is optimizing an operator or set of operations.
- Globally is when you optimize the entire computation graph end to end

Local optimization methods for you models:

- **Vecorization** - For a given for loop or nested loop, execute multiple elements contiguous in memory at the same time to reduce latency caused by data I/O
- **Parallelization** - divide inputs inot different, independent work chunks, and do the operation on each chunk individually at the same time
- **Loop tiling** - change the data accessing order in a loop to leverage hardware's memory layout and cache. This is hardware dependent
- **Operator fusion** - fuse multiple operators to avoid redundant memory access. Minimize loops over an array

Much bigger speedups can be got by leveraging higher-level structures of your computation graph. Ex: fusing a convolutional NN vertically or horizontally to reduce memory access and speed up the model

#### Using ML to optimize ML models

Issues with hand-optimized computational graphs:

- They're nonoptimal (no guaruntee it's the best)
- They're nonadaptive (new frameworks or hardwares requires a huge amount of effort/time)

Model optimization is dependent on the operators contained in the model (CNNs are different than RNNs)

ML could be used to optimize the computations done in a graph

cuDNN exists for convolutional operators

autoTVM is an open source effort for the compiler stack TVM:

1. First breaks computation graphs into subgraphs
2. Predicts how big each subgraph is
3. Allocates time to search for the best possible path for each subgraph
4. Stitches the best possible way to run each subgraph together to execute the entire graph

ML-powered compilers are impressive but slow. However, its a one time operation and then you can cache and use it for future training sessions

### ML in Browsers

One workaround for optimizing code for specific hardware on edge devices is just to run your code in browsers. Then most devices can run it as-is.

Javascript is what comes to mind for browsers, but javascript is slow and complex logic is difficult in it

WebAssembly (WASM) is much more promising. It's an open standard that allows you to run executable programs in browsers. You can compile models in WASM one you have them, and it gives you an executable file to use with javascript

WASM is much faster than javascript but its still slow compared to device-native code. About 55-45% slower
