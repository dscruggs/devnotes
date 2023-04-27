# Chapter 6: Monitoring and Logging

It's important when thinking about logging not only to capture the right things but to captue them in a way that is useful and actionable. Weird logs and error messages that capture data but don't make sense to most humans aren't very valuable.

It takes work to make good monitoring and logging. And likely no one will appreciate great logs until something breaks.

## Observability for Clou MLOps

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
