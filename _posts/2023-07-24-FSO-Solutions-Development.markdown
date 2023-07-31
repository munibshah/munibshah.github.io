---
layout: post
title: "Full Stack Observability for Developers"
date: 2023-07-24 14:37:19 -0700
categories: Observability
---

![spacecraft](/assets/img/spacecraft.jpg)

In this blog we will explore the solutions development aspect of Cisco's Full Stack Observability (FSO) platform. FSO serves as a one-stop-shop for monitoring all layers of your tech stack, effectively putting an end to the time-consuming task of manual log collection and parsing. Instead of trying to figure out where and how to store data, FSO allows developers to focus on what they do best - writing business logic.

To understand different components of the solution, we will take the example of a sample application called Spacefleet. We will break it down into solution components and understand how they can be modelled in FSO into entities, attributes and metric types. Once an application has been modelled, FSO expects to receive the MELT data in the expected format so that applications can apply business logic and create value for users of the platform

### Solution Package

Applications in FSO are represented as solution pakages. A solution package is a JSON file construct with multiple objects definitions. It contains core capabilities that the platform provides. Users have the ability to pull in optional capabilities that have been built on the platform as extensions.

Below is a list of core cababilities that are available to developers:

1. Dashui
2. FMM
3. K8s
4. Zodiac
5. Codex
6. Health Rule

### Entity

An entity to any component or aspect of an application or system that can be monitored or measured. In our example `spacecraft` is an entity.

![Entity](/assets/img/ERD-FSO.drawio.png)

Below is an example of how entities are represented on the FSO Platform

```json
{
  "kind": "entity",
  "name": "spacecraft",
  "displayName": "Spacecraft",
  "displayName": "Represents a spacecraft"
}
```

#### Entity Attributes

Attributes are specific characteristics or properties of an entity that can be observed or measured.

Examples entities are `name, launch_date, Launch_vehicle, Mission_type`

Below is an example of how entitiy attributes are represented on the FSO Platform

```json
{
  "kind": "entity",
  ...
  "attributeDefinitions": {
    "optimized": [
        "spacefleet.spacecraft.name"
        ],
    "required": [
        "spacefleet.spacecraft.name"
        ],
    "attributes": {
        "spacefleet.spacecraft.name": {
            "type": "string",
            "description": "Name of the spacecraft"
      }
    }
  }
}
```

Attributes can be classified as `required` or `optimized`. Optimized attributes are indexed. It is recommended if you need to build complex relationships through the attribute, or if your experience layer is going to send consideriable number of queries using the attribute.

#### Entity Associations

Associations are to create relationships between entities based on compositions. Example compositions are `consists_of` or `uses`.

Below is an example of how entitiy associations are represented on the FSO Platform

```json
{
  "kind": "entity",
  ...
  "associationTypes": {
    "common:consists_of": [
        "${sys.solutionId}:room",
        "${sys.solutionId}:torpedo_tube",
        "${sys.solutionId}:shield",
        "${sys.solutionId}:warp_drive",
    ]
      }
}
```

Associations also support inheritance. So one entity can have a parent entity denoted by a `"parentType"` attribute

#### Entity Metric Types

Associate the type of metrics you should expect from this entity. In our example for `spacecraft` we may want to measure how many torpedoes areleft, or what's our shield level. This is represented using the following syntax:

```json
{
  "kind": "entity",
  ...
  "metricTypes": [
    "${sys.solutionId}:torpedo_count",
    "${sys.solutionId}:shield_level",
    "${sys.solutionId}:speed",
    "${sys.solutionId}:earth_day",
    "${sys.solutionId}:newsfeed_type"
  ]
}
```

#### Entity Event Types

Events are defined similar to metrics since they are structured. The difference is that events are considered to be significant occurence and are captured seperately and sent to the platform. In our example we capture an event when a torpedo is launched, or when a newsfeed item shows up.

These events can be modeled on the platform as

```json
{
  "kind": "entity",
  ...
  "eventTypes": [
    "${sys.solutionId}:newsfeed",
    "${sys.solutionId}:torpedoes_launched",
    "${sys.solutionId}:torpedoes_reloaded"
  ]
}
```

### Event Types

FSO Platform supports Metrics, Logs, Events and Traces. The upcoming section will provide more details on Events and Metrics since these are more structured. Traces are currently only supported on APM modules and will be added to this blog once the feature is available for solution development

#### Event Modelling

Events are structured and thus can be modelled. Here is a sample view of how an event `suggestion` looks like on the platform

```json
{
  "kind": "event",
  "name": "suggestion",
  "displayName": "Suggestion",
  "attributeDefinitions ": {
    "attributes": {
      "sender": {
        "type": "string"
      },
      "suggestion_text": {
        "type": "string"
      },
      "receiver": {
        "type": "string"
      },
      "sensitive": {
        "type": "boolean"
      }
    }
  }
}
```

#### Metrics Modelling

Metrics are similar to events althogh they may contain much more attributes.

Here is a sample view of how a metric `shield_level` might look like

```json
{
  "kind": "metric",
  "name": "shield_level",
  "displayName": "Shield Level",
  "description": "Shield level of a room or spacecraft",
  "category": "current",
  "contentType": "guage",
  "type": "double",
  "unit": "%",
  "ingestGranularities ": [60]
}
```

### MELT collection

Metrics, Events, Logs and Traces need to be collected and sent to the platform. This can either be done by creating an Opentelemetry service that ingests OTEL data, or solution developers will need to create a service that reads this data, extracts telemetry and sends it to the platform.

Applications can then utilize the Unified Query Engine to access the data and build correlations or health rules.
