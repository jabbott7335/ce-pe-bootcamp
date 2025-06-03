---
Title: Event Automation - Labs
hide:
    - toc
---

# Event Automation Labs

## Lab 1 - Filter events based on particular properties

**Filter:**  When processing events, we can use filter operations to select a subset that we want to use. Filtering works on individual events in the stream.

**Scenario: Identify orders from a specific region** 

The EMEA operations team wants to move away from reviewing quarterly sales reports and be able to review orders in their region as they occur.

Identifying large orders as they occur will help the team identify changes that are needed in sales forecasts much earlier. These results can also be fed back into their manufacturing cycle so they can better respond to demand.

[Take me to Lab 1](https://ibm.github.io/event-automation/tutorials/guided/tutorial-1){target="_blank"}

## Lab 2 - Transform events to create or remove properties
**Transform:** When processing events we can modify events to remove some properties from the events. Transforms work on individual events in the stream.

**Scenario: Redact personal information from order events**

The operations team wants to enable another team to analyze order events, however this other team is not permitted access to personally-identifiable information (PII) about customers. They need to be able to process order events without customer PII.

[Take me to Lab 2](https://ibm.github.io/event-automation/tutorials/guided/tutorial-2){target="_blank"}

## Lab 3 - Aggregate events to detect trends over time

**Aggregate:** Aggregates enable you to process events over a time-window. This enables a summary view of a situation that can be useful to identify overall trends.

**Transform:** When processing events we can modify events to create additional properties, which are derived from the event. Transforms work on individual events in the stream.

**Scenario: Track how many products of each type are sold per hour**

In this scenario, we identify the product that has sold the most units in each hourly window. This could be used to drive a constantly updating event streams view of “Trending Products”.

[Take me to Lab 3](https://ibm.github.io/event-automation/tutorials/guided/tutorial-3){target="_blank"}

## Lab 4 - Join related events within time windows

**Interval join:** When looking for patterns in an event stream, sometimes we need to examine events from more than one topic. We talk of this as a “join” between the streams - the same term we would use when working with databases and correlating data between two tables.

**Filter:** When processing events we can use filter operations to select a subset that we want to use. Filtering works on individual events in the stream.

**Scenario: Identify suspicious orders**

Many interesting situations need us to combine multiple streams of events that correlate events across these inputs to derive a new, interesting situation.

In this scenario, we will look for suspicious orders. Specifically, we will be looking for a particular pattern of behavior where large orders have been placed, followed by a smaller order, but the large order was at some point cancelled. This pattern would suggest an attempt to manipulate prices, since the presence of the large order might result in a subsequent reduction in prices, which the smaller order can take advantage of.

To find this pattern, we will use the “join” capability to compare a stream of “orders” with a stream of “cancellations”.

[Take me to Lab 4](https://ibm.github.io/event-automation/tutorials/guided/tutorial-4){target="_blank"}

## Lab 5 - Automate actions based on event triggers
Event destination
When processing events we can send the results to a new Kafka topic. This lets the results from the flow be used to trigger automations, notifications, business workflows, or be processed further in other applications.

Scenario : Distributing results of analysis and processing
The EMEA operations team wants to provide a dedicated stream of EMEA order events for further processing.

[Take me to Lab 5](https://ibm.github.io/event-automation/tutorials/guided/tutorial-5){target="_blank"}

## Lab 6 - Share events for discovery by others

**Scenario: Sharing results of analysis and processing**

The EMEA operations team wants to share their new topic of EMEA orders for use by other teams in their enterprise.

[Take me to Lab 6](https://ibm.github.io/event-automation/tutorials/guided/tutorial-6){target="_blank"}