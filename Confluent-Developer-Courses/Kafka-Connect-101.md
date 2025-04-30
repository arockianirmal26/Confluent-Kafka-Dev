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

Cluster created with a basic configuration

# Hands On: Getting Started with Kafka Connect

## Create a New Topic

- From the cluster overview page go to the 'Topics' in the left navigation bar and then click 'Create Topic'
- Name the topic 'orders' and ensure that the Number of partitions is set to 6 and Click on 'Create with defaults'

## Create a Data Generator with Kafka Connect

In reality, our Kafka topic would probably be populated from an application using the producer API to write messages to it. Here, we’re going to use a data generator that’s available as a connector for Kafka Connect.

- From the cluster overview page go to the 'Connectors' in the left navigation bar and In the search box, enter datagen.
- Select the Sample Data (Datagen Source) connector
- Select 'Orders' template.
- Go to additional configuration and under the Topic selection, choose the exiting topic 'orders'
- Under Kafka credentials, click on Generate API key & download and click continue
- Under Configuration, choose 'JSON' for output record value format and 'Orders' for 'Select a schema' and click continue
- Under Sizing stay with the defaults
- Review and launch the connector now
- From the Topics page of your cluster, select the orders topic and then Messages. You should see a steady stream of new messages arriving

![Datagen Source Connector](assets/images/29.png)

Keep in mind that this Datagen Source Connector is only a jumping off point for your Kafka Connect journey. As a final step, head on over to the Connectors page and take a look at the other connectors. Dont forget to Delete the created connector.

# Running Kafka Connect

## Connectors

![Connectors](assets/images/30.png)

When running Kafka Connect, instances of connector plugins provide the integration between external data systems and the Kafka Connect framework. These connector plugins are reusable components that define how source connectors ought to capture data from data sources to a Kafka topic and also how sink connectors should copy data from Kafka topics to be recognized by a target system. By taking care of all of this boilerplate logic for you, the plugins allow you to hit the ground running with Kafka Connect and focus on your data <br/>

There are hundreds of connector plugins available for a variety of data sources and sinks. There are dozens of fully managed connectors available for you to run entirely through Confluent Cloud. Plus, connectors can also be downloaded from Confluent Hub for use with self-managed Kafka Connect.

## Confluent Cloud Managed Connectors

![Managed Connectors](assets/images/31.png)

Confluent Cloud offers pre-built, fully managed, Apache Kafka connectors that make it easy to instantly connect to popular data sources and sinks. With a simple UI-based configuration and elastic scaling with no infrastructure to manage, Confluent Cloud connectors make moving data in and out of Kafka an effortless task, giving you more time to focus on application development.<br/>

To start, you simply select the connector and fill in a few configuration details about your source or target system. This can be done using the Confluent Cloud console, the Confluent CLI, or the Confluent Connect API.

From there, Confluent takes care of the rest on your behalf:

- Using the configuration settings you specified, your connector instance is provisioned and run
- The execution of the connector instance is monitored
- Should the connector fail, you’ll have access to troubleshooting to help identify the root cause, correct the issue, and restart the connector and its tasks

All in all, you can relax knowing that all of these tasks are being handled for you. That said, there are a few limitations regarding managed connectors:

- Some self-managed connectors that are available on Confluent Hub for installation in self-managed Kafka Connect clusters are not yet available in Confluent Cloud
- Some fully managed Confluent Cloud connectors are not available for all cloud providers
- Some configuration settings available for self-managed connectors may not be available for Confluent managed connectors
- Some single message transformations (SMTs) that are available for use in self-managed Kafka Connect clusters are not available in Confluent Cloud

Be sure to keep those things in mind as you choose which connector options are best for you

## Self-Managed Kafka Connect

![Self-Managed Kafka Connect](assets/images/32.png)

So long as you have access to a Kafka cluster, Kafka Connect can also be run as a self-managed Kafka Connect cluster, but as you can see from the diagram, there is a lot more involved with doing so:

- Self-managed Kafka Connect consists of one or more Connect clusters depending upon the requirement
- Each cluster consists of one or more Connect worker machines on which the individual connector instances run

Regardless of how you choose to run Kafka Connect, it’s helpful to understand the individual Kafka Connect components and how they work together

## Kafka Connect Workers

Ultimately, Kafka Connect workers are just JVM processes that you can deploy on bare metal or containers.

A few options present themselves:

- You’re free to run a bare-metal, on-premises install of Confluent Platform
- For those leveraging infrastructure as a service, you may install Confluent Platform on those resources
- Terraform is an option on a couple cloud providers
- And of course, there’s Docker which you can use for both on-prem and cloud-based installations

## Managing a Kafka Connect Cluster

Once your Kafka Connect cluster is up and running, there’s a bit of management that needs to be done:

- Connect workers have a number of default configuration settings that you may need to alter
- Depending on the needs of your systems, you might need to scale the Connect cluster up or down to suit demand changes
- And of course, you’ll be monitoring for problems and fixing those that occur

# Connectors, Configuration, Converters, and Transforms

## Inside Kafka Connect

![Inside Kafka Connect](assets/images/33.png)

Kafka Connect is built around a pluggable architecture of several components, which together provide very flexible integration pipelines. To get the most out of Kafka Connect it’s important to understand these components and their roles:

- Connectors are responsible for the interaction between Kafka Connect and the external technology it’s being integrated with
- Converters handle the serialization and deserialization of data
- Transformations can optionally apply one or more transformations to the data passing through the pipeline

## Connectors

The key component of any Kafka Connect pipeline is a connector instance which is a logical job that defines where data should be copied to and from. All of the classes that implement or are used by a connector instance are defined in its connector plugin. Written by the community, a vendor, or occasionally written bespoke by the user, the plugin integrates Kafka Connect with a particular technology. These plugins are reusable components that you can download, install, and use without writing code.

For example:

- The Debezium MySQL source connector uses the MySQL bin log to read events from the database and stream these to Kafka Connect
- The Elasticsearch sink connector takes data from Kafka Connect, and using the Elasticsearch APIs, writes the data to Elasticsearch
- The S3 connector from Confluent can act as both a source and sink connector, writing data to S3 or reading it back in

A SOURCE connector plugin knows how to talk to a specific SOURCE data system and generate records that Kafka Connect then writes into Kafka. On the downstream side, the connector instance configuration specifies the topics to be consumed and Kafka Connect reads those topics and sends them to the SINK connector that knows how to send those records to a specific SINK data system.

So the connectors know how to work with the records and talk to the external data system, but Kafka Connect workers act as the conductor and take care of the rest. We will define what a worker is shortly.

### Add a Connector Instance with the REST API

Here’s an example that creates an Elasticsearch sink connector instance with a call to Kafka Connect’s REST API

```bash
curl -X PUT -H  "Content-Type:application/json" http://localhost:8083/connectors/sink-elastic-01/config \
    -d '{
    "connector.class": "io.confluent.connect.elasticsearch.ElasticsearchSinkConnector",
    "topics"         : "orders",
    "connection.url" : "http://elasticsearch:9200",
    "type.name"      : "_doc",
    "key.ignore"     : "false",
    "schema.ignore"  : "true"
}'
```

### Add a Connector Instance with ksqlDB

You can also use ksqlDB to manage connectors. Here is the syntax for adding the previous Elasticsearch sink connector instance:

```sql
CREATE SINK CONNECTOR sink-elastic-01 WITH (
  'connector.class' = 'io.confluent.connect.elasticsearch.ElasticsearchSinkConnector',
  'topics'          = 'orders',
  'connection.url'  = 'http://elasticsearch:9200',
  'type.name'       = '_doc',
  'key.ignore'      = 'false',
  'schema.ignore'   = 'true'
);
```

### Add a Connector Instance with the Console UI

In addition to using the Kafka Connect REST API directly and with ksqlDB, you can add connector instances using the Confluent Cloud console

## What is the Role of the Connector?

It’s important to understand that the connector plugins themselves don't read from or write to (consume/produce) Kafka itself. The plugins just provide the interface between Kafka and the external technology. This is a deliberate design.

- Source connectors interface with the source API and extract the payload + schema of the data, and pass this internally as a generic representation of the data

- Sink connectors work in reverse—they take a generic representation of the data, and the sink connector plugin writes that to the target system using its API

Kafka Connect and its underlying components take care of writing data received from source connectors to Kafka topics as well as reading data from Kafka topics and passing it to sink connectors. Now, this is all hidden from the user—when you add a new connector instance, that’s all you need to configure and Kafka Connect does the rest to get the data flowing. Converters are the next piece of the puzzle and it is important to understand them to help you avoid common pitfalls with Kafka Connect. Technically, transforms sit between connectors and converters, but we’ll visit those later.

## Converters Serialize/Deserialize the Data

![Converters](assets/images/34.png)

Converters are responsible for the serialization and deserialization of data flowing between Kafka Connect and Kafka itself. You’ll sometimes see similar components referred to as SerDes (“SerializerDeserializer”) in Kafka Streams, or just plain old serializers and deserializers in the Kafka Client libraries.

There are a ton of different converters available, but some common ones include:

```
Avro – io.confluent.connect.avro.AvroConverter
Protobuf – io.confluent.connect.protobuf.ProtobufConverter
String – org.apache.kafka.connect.storage.StringConverter
JSON – org.apache.kafka.connect.json.JsonConverter
JSON Schema – io.confluent.connect.json.JsonSchemaConverter
ByteArray – org.apache.kafka.connect.converters.ByteArrayConverter
```

While Kafka doesn’t care about how you serialize your data (as far as it’s concerned, it’s just a series of bytes), you should care about how you serialize your data! In the same way that you would take a carefully considered approach to how you design your services and model your data, you should also be deliberate in your serialization approach

### Serialization and Schemas

![Serialization and Schemas](assets/images/35.png)

- As well as managing the straightforward matter of serializing data flowing into Kafka and deserializing it on its way out, converters have a crucial role to play in the persistence of schemas. Almost all data that we deal with has a schema; it’s up to us whether we choose to acknowledge that in our designs or not. You can consider schemas as the API between applications and components of a pipeline. Schemas are the contract between one component in the pipeline and another, describing the shape and form of the data.

- When you ingest data from a source such as a database, as well as the rows of data, you have the metadata that describes the fields—the data types, their names, etc. Having this schema metadata is valuable, and you will want to retain it in an efficient manner. A great way to do this is by using a serialization method such as Avro, Protobuf, or JSON Schema. All three of these will serialize the data onto a Kafka topic and then store the schema separately in the Confluent Schema Registry. By storing the schema for data, you can easily utilize it in your consuming applications and pipelines. You can also use it to enforce data hygiene in the pipeline by ensuring that only data that is compatible with the schema is stored on a given topic

- You can opt to use serialization formats that don’t store schemas like JSON, string, and byte array, and in some cases, these are valid. If you use these, just make sure that you are doing so for deliberate reasons and have considered how else you will handle schema information

Converters are specified separately for the value of a message, and its key. Note that these converters are set as a global default per Connect worker, but they can be overridden per connector instance.

```config
key.converter=org.apache.kafka.connect.storage.StringConverter
value.converter=org.apache.kafka.connect.storage.StringConverter
value.converter.schema.registry.url=http://localhost:8081
```

## Single Message Transforms

The third and final key component in Kafka Connect is the transform piece. Unlike connectors and converters, these are entirely optional. You can use them to modify data from a source connector before it is written to Kafka, and modify data read from Kafka before it’s written to the sink. Transforms operate over individual messages as they move, so they’re known as Single Message Transforms or SMTs.

![Serialization and Schemas](assets/images/36.png)

Common uses for SMTs include:

- Dropping fields from data at ingest, such as personally identifiable information (PII) if specified by the system requirements
- Adding metadata information such as lineage to data ingested through Kafka Connect
- Changing field data types
- Modifying the topic name to include a timestamp
- Renaming fields

For more complex transformations, including aggregations and joins to other topics or lookups to other systems, **a full stream processing layer in ksqlDB or Kafka Streams is recommended**.

## Obtaining Plugins and Writing Your Own

- Connectors, transforms, and converters are all specified as part of the Kafka Connect API, and you can consult the Javadoc to write your own.
- Apache Kafka and Confluent have several converters and transforms built in already, but you can install more if you need them. You will find these along with hundreds of connectors in Confluent Hub.
