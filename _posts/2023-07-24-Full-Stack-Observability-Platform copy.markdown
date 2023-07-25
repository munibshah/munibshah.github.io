---
layout: post
title: "Full Stack Observability Platform Architecture"
date: 2023-07-22 14:37:19 -0700
categories: Observability
---

![FSO Platform Architecture](/assets/img/FSOarch.png)

#### MELT Store

As a platform developer you should not have to worry about where and how to store or managedata. The MELT store acts as storage for all Metrics, Events, Logs and Traces. This frees up application developers to focus on business logic.

#### Knowledge Store

Allows developers to store non-MELT data models. Acts as the brain of the platform. A solution developer can define a type as a JSON schema with a few rules. A knowledge store is multi-data center globally replicable. Information from the knowledge store can repllicated across local cells, tenants or globally.

#### ABAC/RBAC

Solution developers get to define roles and permissions for existing and extended capabilities of the platform

#### Entity Model

MELT data is attached to an entity which ties it to a larger entity model. There are pre-existing models built for AWS, Kubernetes etc.

#### Subscription Model

Solution develops get to define which entities do they want to apply their solution to. They also get to choose to only allow access to views of their application if customers have subscribed to their service

#### Custom APIs

Add containers onto the platform (sort of like FaaS). So as a solution's developer you may have a Node server to create and delete investigation by talking to the Knowledge store. You can put your whole node server on the platform as a container and extend the platform APIs along with the roles required to run the APIs

#### Cloud Collector

Cloud collectors are containers that run as cron tabs. For example you might want to have a metric on the platform that shows a value everytime someone checks in code to a Git repository. You will create an entity for the Git repository and create a commit metric on the entity. Every commit will add to the metric.

Cloud Collectors are a way to download metrics from the cloud such as AWS. You will need to store AWS credentials in a secure manner using secrets. FSO platform allows you to define a tenant level secret in the Knowledge Store

#### Open Teletry Ingestor

Ingests MELT data in the Open Telemetry format using OTLP

#### Health Rules

Solution Developers can define health rules for thresholds or correlation

#### UQL

Unified Query Language that is used to query data from the MELT and Knowledge Store
