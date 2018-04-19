## Package overview

This package provides APIs for using the capabilities of Metrics and Tracing.

### Metrics

Following 4 types of metrics are supported: 
Counter
Gauge
Summary
Timer

**NOTE:** These metrics will be by default exposed via an endpoint that supports Prometheus format. That being said, metric implementation is capable to support other monitoring solutions by extending ballerina runtime.

#### Counter 

A counter is used to hold a single numerical value that could only increase. This could be used to track counts of events or running totals. For example, total number of successful requests and total number of 5xx Server errors.

```ballerina
//Create a tag
map tags = {"event_type":"test"};

//Create a counter
observe:Counter counter = new("event_total", "Total count of events.", tags);

//Increment the counter by 1
counter.incrementByOne();

//Increment the counter by 5
counter.increment(5);

//Print the current value of the counter
io:println("count: " + counter.count());
```ballerina

#### Gauge

A gauge is used to hold a single numerical value that could increase as well as decrease. This could be used to report instantaneous values. For example, CPU Usage and Maximum number of open file descriptors.

```ballerina
//Create a tag
map tags = {"event_type":"test"};

//Create a gauge
observe:Gauge gauge = new("event_queue_size", "Size of an event queue.", tags);

//Increment the gauge by one
gauge.incrementByOne();

//Increment the gauge by three
gauge.increment(3);

//Decrement the gauge by one
gauge.decrementByOne();

//Decrement the gauge by two
gauge.decrement(2);

//Print the current value of the guage
io:println("gauge: " + gauge.value());
```ballerina

#### Summary 

A summary is used to sample the size of events, which means it can calculate the distribution of a value. For example, request duration and response size. It supports returning the count of recorded events and returning the maximum and mean values of events recorded. Furthermore, it could return values at different percentiles.

```ballerina
//Create a tag
map tags = {"event_type":"test"};

//Create a Summary
observe:Summary summary = new("event_size", "Size of an event.", tags);

//Record events in the summary
summary.record(5);	
summary.record(1);
summary.record(8);
summary.record(3);
summary.record(4);

//Count the number of recorded events in the summary
io:println("count : " + summary.count());

//Return the maximum value of events recorded in the summary
io:println("max: " + summary.max());

//Return the mean value of events recorded in the summary 
io:println("mean: " + summary.mean());

//Return the values at different percentiles
io:print("percentile values: ");
io:println(summary.percentileValues());
io:println("");
```ballerina

#### Timer
A timer is similar to a Summary, except it is especially used to sample the time durations. Which means it can aggregate timing durations and provides duration statistics. Similar to Summary, it supports returning the count of recorded items and returning the maximum and mean values of events recorded and as well as returning values at different percentiles. A timer uses a Summary internally to provide these features.

```ballerina
//Create a timer
observe:Timer timer = new("event_duration", "Duration of an event.", tags);

//Record times in the timer
timer.record(1000, observe:TIME_UNIT_NANOSECONDS);
timer.record(200, observe:TIME_UNIT_MICROSECONDS);
timer.record(30, observe:TIME_UNIT_MILLISECONDS);
timer.record(4, observe:TIME_UNIT_SECONDS);
timer.record(1, observe:TIME_UNIT_MINUTES);

//Return the number of times that record has been called since this timer was created.
io:println("count: " + timer.count());

//Return the maximum time recorded in the timer
io:println("max: " + timer.max(observe:TIME_UNIT_SECONDS) + " seconds");

//Return the mean value of times recorded in the timer
io:println("mean: " + timer.mean(observe:TIME_UNIT_SECONDS) + " seconds");

//Return the latencies at specific percentiles.
io:print("percentile values: ");
io:println(timer.percentileValues(observe:TIME_UNIT_SECONDS));
```ballerina

### Tracing
This package exposes APIs according to OpenTracing specification version 1.1. By default Jaeger OpenTracing implementation is used. 

#### Span
According to OpenTracing specification, tracing is built around the key concept of a Span. A span encapsulates a start time, finish time, a set of Span Tags, a set of Span Logs, a Span Context and a Reference to a parent span. A single trace could consist of multiple spans, especially when the trace goes across multiple services.

#### Reference Type
Following are the types of references that could exist between spans:
REFERENCE_TYPE_CHILDOF - The parent span depends on the child span in some capacity.
REFERENCE_TYPE_FOLLOWSFROM - The parent span does not depend on the child span.
REFERENCE_TYPE_ROOT - Root span which has no reference to a parent span.

#### Span Tags
These are key:value pairs that can be used to maintain metadata of the Span. The keys must be of type string, and the values could be strings, booleans, or numeric types. There is a set of standard tags documented under [OpenTracing Semantic Conventions](https://github.com/opentracing/specification/blob/master/semantic_conventions.md)

**Example of starting Root and Child spans with tags**
```ballerina
//Start a root span with service name ‘Store’, span name ‘Add Item’ and ‘span.kind’ //tag as ‘server’
observe:Span span = observe:startSpan("Store", "Add Item", {"span.kind":"server"}, observe:REFERENCE_TYPE_ROOT, ());

//Start a span with CHILDOF reference to the root span with service name ‘Store’, 
//span name ‘Update Manager Connector’. Add ‘span.kind’ tag as ‘client’
observe:Span childSpan = observe:startSpan("Store", "Update Manager Connector",
                  	{"span.kind":"client"}, observe:REFERENCE_TYPE_CHILDOF, span);
```ballerina

#### Span Logs
This is a key:value map paired with a timestamp. The logs related to the  The keys must be strings, though the values may be of any type. There is a set of standard log fields documented under [OpenTracing Semantic Conventions](https://github.com/opentracing/specification/blob/master/semantic_conventions.md)

**Examples of log types**
```ballerina
span.log("Something", "Log");
span.logError("Payload Error", payloadError.message);
```ballerina

#### Span Context
A span has an implementation specific context that can be used to hold data related to the span. Depending on the OpenTracing implementation, a span context could have data such as trace ID, span ID, and Baggage items, which are key value pairs that can be propagated among different services. Jaeger is the default implementation used, and it supports those mentioned data.

#### Trace Group
Trace group is a set of related spans across different services. By defining trace groups, user can start multiple spans of different traces within a service and then propagate those span contexts to other services. Related spans will be associated with the trace via the trace group ID.

```ballerina
//In order to propagate the span across http requests, the span should be injected to http headers as a span context
http:Request outRequest = childSpan.injectSpanContextToHttpHeader(new http:Request(), "group-1");

//Extracting the span context received via http
observe:SpanContext parentSpanContext = observe:extractSpanContextFromHttpHeader(req, "group-1");
```ballerina

# Package contents
<auto-generated from the code comments>
