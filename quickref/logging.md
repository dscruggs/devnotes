# Logging

## Structured logging

- logs can be "structured" in different ways. The most common is with `levels` like info, debug, warning, error, etc.
- you can use key:value pairs in logs to help structure
- `slog` in go is one implementation of a structured logging library
- always include timestamps in logs
- include the minimal logging necessary at the right levels for your code. Too much logging is almost as bad as no logs

## Errors

- most errors should be logged, and you should include stack traces
- you should separate stack trace from error messages in your logs, preferably in separate key:value pairs
- "handle errors once". If you find an error, handle it. But only handle it once, don't do it a bunch of times

## Context

errors / logs are only good if you can understand the whole story around them. This is context. Examples:

- what machine it came from (and OS info, etc.)
- what request it's related to
- what user
- what service
- what build/version. Commit SHAs are the clearest signal here
- networking info (region, IP, hostname, time zone, etc.)
- request info like response codes, bytes in request, info about user, header, duration, http error, etc.

Correlating requests across processes is a big deal. Stable IDs being passed around helps with this.

## Log storage

- usually just output to console. These can be redirected to files or to cloud logging services
- if logging to file make sure to "rotate" your logs (stop writing to one file and start writing to a new one)
- `syslog` is a standard in the 80s that's still widely used. A syslog server accepts logs from the network or socket and writes them to a file, and lets you filter through them, transform formats, and forward to things like DBs

## Log security

- don't include secrets or internal details about the application in your error messages / logs. They might seem useful, but it's a security risk. Things like passwords/api keys, etc. are obvious, but things like table schemas are less obvious but still risky
- don't log full requests/responses
- use wrappers to make strings safe for logging for things like DB paths. This is called `obfuscation` where you include a little bit of info useful for debugging, but don't include the whole thing. this is different from `redaction` where you just hide the whole thing
- you can also add filters to logging view tools to help hide secrets since in large systems it's unlikely there will be 0 that get logged
- encrypted logs are useful for cases where you must have sensitive info in there
    - this needs a key to access / read / use

---

## Metrics

- metrics measure certain aspects of the app / environment and are usually much more standardized than logging
- `prometheus` is a popular monitoring/alerting tooling for this
- system health metrics are the baseline importance. These can tell you if your whole app will break with resource utilization stats
- in the case of prometheus you need to export the metrics / data there so it can be the hub for them
- some methods for metrics:
    - `RED - rate, errors, duration`. Number of requests per second, number of requests that are failing, and how long they take
    - `USE - utilization, saturation, errors`. Percentage of time the resource is busy/consumed. Amount of work the resource has to do (queue length, etc.), and count of errors
- something like `grafana` lets you create charts and graphs of metrics
- very basic metrics that web apps should have is count of requests by method, path, and status

## Alerting

- metric-based alerts just have some kind of logic to look at metrics and perform an action (usually a notification) based on that value
- most important thing here is to avoid alert fatigue. Make only the important and actionable things alert, they should not be just informational
- tune thresholds/alerts over time

---

## Profiling

- practice of taking measurements of your running program to learn where it's spending the most resources: CPU cycles, memory, context switches, etc.
- Common measurements here:
    - CPU / GPU utilization
    - memory (heap) utilization
    - thread usage
    - blocking calls
    - execution tracing
- typically in depth profiling is not something that should be done upfront. More when you have something deployed and need to diagnose performance issues or during UAT
- you can get some cool maps / charts of this showing end to end lifecycles and all the steps it took for optimization

---

## Tracing

- `Tracing` records the execution path of a request through a system (distributed or not)
- a basic example of a trace is a stack trace
- A full request trace should show all the steps that happened in a web request, and the timing of each, and interesting characteristics about each step
- a trace is comprised of `spans`
- `opentelemetry` and `jaeger` are two popular open source tracing solutions
- A trace is an end to end request
- A trace is made up of spans
    - each trace has at least one span: the "root span". A root span can have one or more child spans, and those can have grandchild spans, etc. for arbitrary depths
    - each span has a unique ID, a name, a start and end time, and optional metadata like status, attributes, and events
    - `attributes` are arbitrary key:value pairs in spans
    - `events` are timestamped annotations on a span. They indicate exactly when something noteworthy happened on a span
    - `status` is a status on the span. Default UNSET but can be OK or ERROR

### Instrumenting

- you need to wire up your app to send things to jaeger/opentelemetry. Think of opentelemetry as providing the instrumentation to collect the traces and move them somewhere, and jaeger is one place you can put them
- you need to decide how to define your spans. technically you could add them for everything. This can create span fatigue like too much logging. So think about the right level of granularity
- you can define `scopes` to spans to help group them among different traces / different spans in the same trace
- a `tracer` is a lightweight object that creates spans
    - you do a `start` and `end` with this tracer which sets the times for the span
- for distributed systems you need to propagate the trace context throughout HTTP request headers or some other way to tie traces / spans together
