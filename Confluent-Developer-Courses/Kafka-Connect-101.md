# Getting started

Here I documented the practical things that I have done in the below course<br/>
[Kafka Connect 101](https://developer.confluent.io/courses/kafka-connect/intro/)

# Introduction

## Ingest Data from Upstream Systems

![Ingest Data from Upstream Systems](assets/images/24.png)

- Kafka Connect is a component of Apache Kafka® that’s used to perform streaming integration between Kafka and other systems such as databases, cloud services, search indexes, file systems, and key-value stores

- Kafka Connect makes it easy to stream data from numerous sources into Kafka, and stream data out of Kafka to numerous targets. The diagram you see here shows a small sample of these sources and sinks (targets). There are literally hundreds of different connectors available for Kafka Connect

## Confluent Cloud Managed Connectors

You can choose to run Kafka Connect yourself or take advantage of the numerous fully managed connectors provided in Confluent Cloud for a totally cloud-based integration solution. In addition to managed connectors, Confluent provides fully managed Apache Kafka, Schema Registry, and stream processing with ksqlDB

## How Kafka Connect Works

![Kafka Connect](assets/images/25.png)

- Kafka Connect runs in its own process, separate from the Kafka brokers. It is distributed, scalable, and fault tolerant, giving you the same features you know and love about Kafka itself

- But the best part of Kafka Connect is that using it requires no programming. It’s completely configuration-based, making it available to a wide range of users—not just developers. In addition to ingest and egress of data, Kafka Connect can also perform lightweight transformations on the data as it passes through

- Anytime you are looking to stream data into Kafka from another system, or stream data from Kafka to elsewhere, Kafka Connect should be the first thing that comes to mind. Let’s take a look at a few common use cases where Kafka Connect is used

### Streaming Pipelines

![Streaming Pipelines](assets/images/26.png)

Kafka Connect can be used to ingest real-time streams of events from a data source and stream them to a target system for analytics. In this particular example, our data source is a transactional database. <br/><br/>

We have a Kafka connector polling the database for updates and translating the information into real-time events that it produces to Kafka. That in and of itself is great, but there are several other useful things that we get by adding Kafka to the mix:

- First of all, having Kafka sits between the source and target systems means that we’re building a loosely coupled system. In other words, it’s relatively easy for us to change the source or target without impacting the other.
- Additionally, Kafka acts as a buffer for the data, applying back pressure as needed.
- And also, since we’re using Kafka, we know that the system as a whole is scalable and fault tolerant.

Because Kafka stores data up to a configurable time interval per data entity (topic), it’s possible to stream the same original data to multiple downstream targets. This means that you only need to move data into Kafka once while allowing it to be consumed by a number of different downstream technologies for a variety of business requirements or even to make the same data available to different areas of a business.

### Writing to Datastores from Kafka

![Writing to Datastores from Kafka](assets/images/27.png)

- As another use case, you may want to write data created by an application to a target system. This of course could be a number of different application use cases, but suppose that we have an application producing a series of logging events, and we’d like those events to also be written to a document store or persisted to a relational database

- Imagine that you added this logic to your application directly. You’d have to write a decent amount of boilerplate code to make this happen, and whatever code you do add to your application to achieve this will have nothing to do with the application’s business logic. Plus, you’d have to maintain this extra code, determine how to scale it along with your application, how to handle failures, restarts, etc

- Instead, you could add a few simple lines of code to produce the data straight to Kafka and allow Kafka Connect to handle the rest. As we saw in the last example, by moving the data to Kafka, we’re free to set up Kafka connectors to move the data to whatever downstream datastore that we need, and it’s fully decoupled from the application itself

### Evolve Processing from Old Systems to New

![Evolve Processing from Old Systems to New](assets/images/28.png)

By utilizing change data capture (CDC), it’s possible to extract every INSERT, UPDATE, and even DELETE from a database into a stream of events in Kafka. And we can do this in near-real time. Using underlying database transaction logs and lightweight queries, CDC has a very low impact on the source database, meaning that the existing application can continue to run without any changes, all the while new applications can be built, driven by the stream of events captured from the underlying database. When the original application records something in the database—for example, an order is accepted—any application subscribed to the stream of events in Kafka will be able to take an action based on the events—for example, a new order fulfillment service.

## Why Not Write Your Own Integrations?

- All of this sounds great, but you’re probably asking, “Why Kafka Connect? Why not write our own integrations?”

- Apache Kafka has its own very capable producer and consumer APIs and client libraries available in many languages, including C/C++, Java, Python, and Go. So it makes sense for you to wonder why you wouldn’t just write your own code to move data from a system and write it to Kafka—doesn’t it make sense to write a quick bit of consumer code to read from a topic and push it to a target system?

- The problem is that if you are going to do this properly, then you need to be able to account for and handle failures, restarts, logging, scaling out and back down again elastically, and also running across multiple nodes. And that’s all before you’ve thought about serialization and data formats. Of course, once you’ve done all of these things, you’ve written something that is probably similar to Kafka Connect, but without the many years of development, testing, production validation, and community that exists around Kafka Connect. Even if you have built a better mousetrap, is all the time that you’ve spent writing that code to solve this problem worth it? Would your effort result in something that significantly differentiates your business from anyone else doing similar integration?

- The bottom line is that integrating external data systems with Kafka is a solved problem. There may be a few edge cases where a bespoke solution is appropriate, but by and large, you’ll find that Kafka Connect will become the first thing you think of when you need to integrate a data system with Kafka.

**Create a cluster in the confluence cloud for the hands on exercises**
