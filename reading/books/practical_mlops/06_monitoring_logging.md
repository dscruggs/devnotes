# Chapter 6: Monitoring and Logging

It's important when thinking about logging not only to capture the right things but to captue them in a way that is useful and actionable. Weird logs and error messages that capture data but don't make sense to most humans aren't very valuable.

It takes work to make good monitoring and logging. And likely no one will appreciate great logs until something breaks.

## Observability for Cloud MLOps

`Amazon Cloudwatch`, `Google Cloud Operations Suite`, `Azure Monitor` are all cloud monitoring solutions

MLOps requires all the monitoring of traditional software (memory, cpu usage, etc.) along with additional monitoring for new components around data statistics, model outputs, retraining triggers, etc.

## Introduction to Logging

Logging can have several `"levels"`. One level can be basic logs, another can be errors. Levels are useful for finding out specific types of events or actions

Print statements are useful for debugging, but then you have to delete and add in new print statements for each problem. This is where logging can help

## Logging in Python

Basic components of logging:

- log levels
- redirection of output
- other facilities

One valuable aspect of logging is resilient to failures when constructing a message. If there's an error it will call out the error but not break the production application like a print statement would

You can configure logger templates to include things like name and levelname or timestamps in the print out of logs

Basic example showing logging for a program reading a CSV. It continues to run even through failure

```python
import sys
import logging
import pandas as pd

log_format = "[%(name)s][%(levelname)-6s] %(message)s"
logging.basicConfig(format=log_format)
logger = logging.getLogger("describe")
logger.setLevel(logging.DEBUG)

argument = sys.argv[-1]
logger.debug("processing input file: %s", argument)

try:
    df = pd.read_csv(argument)
    print(df.describe())
except Exception:
    logger.exception("Had a problem trying to read the CSV file")

logger.info("the program continues, without issue")
```

### Modifying Log Levels

Log levels:

1. debug
2. info
3. warning
4. error
5. critical

`The bus test` - if you can get hit by a bus today, and everything still works tomorrow and those around you can pick up the slack, it's a good piece of software

## Model Monitoring Basics

There's a few good metrics to capture for systems:

- `Counter`: counting items (empty values, nonnull values, etc.)
- `Timer`: timing how long actions take
- `Value`: Values are helpful for debugging and giving texture to timers and counters

"Strong foundations enable strong results"

## Exercises

- [ ] Use a different dataset and create a drift report with violations using AWS Sage‐Maker.
- [ ] Add Python logging to a script that will log errors to STDERR, info statements toSTDOUT, and all levels to a file.
- [ ] Create a time-series dataset on Azure ML Studio.
- [ ] Configure a dataset monitor in Azure ML Studio that will send an email when a drift is detected beyond the acceptable threshold.

## Critical Thinking Discussion Questions

#### Why might it be desirable to log to multiple sources at the same time?

Different applications may use the output of logs for different purposes. For examples you may want errors and debugging to go to one place, but standard system output or messages for users to go to the front end

#### Why is it critical to monitor data drift?

Garbage in garbage out. If the data no longer accuractely reflects what the model was trained on, it's time to retrain the model.

#### Name three advantages of using logging facilities versus print() or echo statements

- Print and echo can be easily lost and are only good in the moment. Logs can be stored for later reference.
- Logs can be given at various levels and can be sent to various sources with ease whereas print statements can only go to the console.
- Print statements require a lot of code changes. Many libraries have logging built in that you can take advantage of so you don't need to print

#### List the five most common log levels, from least to most verbose

1. debug
2. info
3. warning
4. error
5. critical

#### What are three common metric types found in metric-capturing systems?

Couner, timer, values
