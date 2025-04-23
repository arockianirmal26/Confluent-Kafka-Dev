# Getting started

Here I documented the practical things that I have done in the below course<br/>
[Kafka Streams 101](https://developer.confluent.io/courses/kafka-streams/get-started/)

# Basic Operations

## Event Streams

An event represents data that corresponds to an action, such as a notification or state transfer. An event stream is an unbounded collection of event records.

## Key-Value Pairs

Apache Kafka® works in key-value pairs, so it’s important to understand that in an event stream, records that have the same key don't have anything to do with one another. For example, the image below shows four independent records, even though two of the keys are identical:

![Key-Value Pairs](assets/images/9.png)

## Topologies

To represent the flow of stream processing, Kafka Streams uses topologies, which are directed acyclic graphs ("DAGs"). Each Kafka Streams topology has a source processor, where records are read in from Kafka. Below that are any number of child processors that perform some sort of operation. A child node can have multiple parent nodes, and a parent node can have multiple child nodes. It all feeds down to a sink processor, which writes out to Kafka.

![Kafka Streams Topology](assets/images/10.png)

## Streams

You define a stream with a **StreamBuilder**, for which you specify an input topic as well as SerDes (Serialization/Deserialization) configurations, via a **Consumed** configuration object. These settings—covered in detail in one of the later Modules — tells Kafka Streams how to read the records, which have **String**-typed keys and values in the below example. A **KStream** is part of the Kafka Streams DSL, and it’s one of the main constructs you'll be working with.

## Stream Operations

Once you've created a stream, you can perform basic operations on it, such as mapping and filtering.

### Mapping

With mapping, you take an input object of one type, apply a function to it, and then output it as a different object, potentially of another type. For example, using mapValues, you could convert a string to another string that omits the first five characters:

```java
mapValues(value -> value.substring(5))
```

**Map**, on the other hand, lets you change the key and the value:

```java
map((key, value) -> ..)
```

### Filtering

With a filter, you send a key and value, and only the records that match a predicate make it through to the other side.

```java
filter((key, value) -> Long.parseLong(value) > 1000)
```

# Important Note

A final note to you as we wrap up the exercises for this course: Don’t forget to delete your resources and cluster in order to avoid exhausting the free Confluent Cloud usage that is provided to you.
