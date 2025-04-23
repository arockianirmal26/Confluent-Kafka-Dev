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

# Hands On: Basic Operations

The Basic Operations exercise demonstrates how to use Kafka Streams stateless operations such as filter and mapValues. Note that the next few steps, including setting up Confluent Cloud, creating the properties, adding the application ID, and creating the StreamsBuilder, apply to each exercise but will only be shown in this one <br/><br/>

- Clone the course's GitHub repository and load it into your favorite IDE or editor

```bash
git clone https://github.com/confluentinc/learn-kafka-courses.git
```

- Change to the directory

```bash
cd kafka-streams
```

- Compile the source

```bash
./gradlew build
```

The source code in this course is compatible with Java 11, and this module's code can be found in the source file **java/io/confluent/developer/basic/BasicStreams.java**

If you get error like **Build Failed**, then you must check the version of java that you are using. In my case I was using java 22 but the gradle supports until java 17. So I had to downgrade my java. I used sdkman for this and below are my exact commands

```bash
curl -s "https://get.sdkman.io" | bash
source "$HOME/.sdkman/bin/sdkman-init.sh"
sdk install java 17.0.9-tem
sdk use java 17.0.9-tem
```

To ensure SDKMAN is initialized in all terminal sessions, I had to edit my shell config

```bash
vi ~/.zshrc
```

Add this line at the bottom (if it's not already there):

```
source "$HOME/.sdkman/bin/sdkman-init.sh"
```

When I tried the **./gradlew build** again with Java 17 then the Build was successful.

- Create a new cluster in Confluent Cloud. For the purposes of all the exercise modules you can use the Basic type. Name the cluster 'kafka_streams_course'.

- On the cluster's overview page, click **Clients - Java**. Then create an API key for the cluster and default topic. Finally download the 'quick start template'. Upzip the downloaded file and copy the content of client.properties

![Java Client](assets/images/11.png)

- Earlier we cloned a github repo called 'LEARN-KAFKA-COURSES'. Create a file named ccloud.properties in the src/main/resources directory. Then paste the configurations which we copied earlier into the ccloud.properties file.

- Now set up properties for the exercises (you are taking a couple of minor extra steps to make sure that any sensitive information doesn't get accidentally checked into GitHub). First make sure you are in src/main/resources. Then run the following commands

```bash
cat streams.properties.orig > streams.properties
cat ccloud.properties >> streams.properties
```

- Now go to the file and complete it 'LEARN-KAFKA-COURSES' - 'kafka-streams' - src - main - java/io/Confluent/developer - basic - BasicStreams.java. Below is the complete code. (Logic: Input topic - Streams (basic operations) - Output topic)

```java
package io.confluent.developer.basic;

import org.apache.kafka.common.serialization.Serdes;
import org.apache.kafka.streams.KafkaStreams;
import org.apache.kafka.streams.StreamsBuilder;
import org.apache.kafka.streams.StreamsConfig;
import org.apache.kafka.streams.kstream.Consumed;
import org.apache.kafka.streams.kstream.KStream;
import org.apache.kafka.streams.kstream.Produced;

import java.io.FileInputStream;
import java.io.IOException;
import java.time.Duration;
import java.util.Properties;
import java.util.concurrent.CountDownLatch;

public class BasicStreams {

    public static void main(String[] args) throws IOException {
        Properties streamsProps = new Properties(); //create a properties object
        //Use a FileInputStream to load properties from the file that includes your Confluent Cloud properties; in addition,
        // add the application configuration ID to the properties
        // Make sure you've added your Confluent Cloud credentials as outlined in the README
        try (FileInputStream fis = new FileInputStream("src/main/resources/streams.properties")) {
            streamsProps.load(fis);
        }
        streamsProps.put(StreamsConfig.APPLICATION_ID_CONFIG, "basic-streams");

        //Create a StreamsBuilder instance, and retrieve the name of the inputTopic and outputTopic from the Properties
        StreamsBuilder builder = new StreamsBuilder();
        final String inputTopic = streamsProps.getProperty("basic.input.topic");
        final String outputTopic = streamsProps.getProperty("basic.output.topic");

        //Create an order number variable (you'll see where it comes into play soon), and then create the KStream instance
        // (note the use of the inputTopic variable):
        final String orderNumberStart = "orderNumber-";
        // Using the StreamsBuilder from above, create a KStream with an input-topic
        // and a Consumed instance with the correct
        // Serdes for the key and value HINT: builder.stream and Serdes.String()
        KStream<String, String> firstStream = builder.stream(inputTopic, Consumed.with(Serdes.String(), Serdes.String()));

        //Add a peek operator (it's expected that you don't modify the keys and values). Here, it's printing records as they come into the topology
        firstStream.peek((key, value) -> System.out.println("Incoming record - key " + key + " value " + value))
                // filter records by making sure they contain the orderNumberStart variable from above HINT: use filter
                .filter((key, value) -> value.contains(orderNumberStart))
                // map the value to a new string by removing the orderNumberStart portion HINT: use mapValues
                .mapValues(value -> value.substring(value.indexOf("-") + 1))
                // only forward records where the value is 1000 or greater HINT: use filter and Long.parseLong
                .filter((key, value) -> Long.parseLong(value) > 1000)
                //an additional peek method to display the transformed records:
                .peek((key, value) -> System.out.println("Outgoing record - key " + key + " value " + value))
                // Write the results to an output topic defined above as outputTopic HINT: use "to" and Produced and Serdes.String()
                .to(outputTopic, Produced.with(Serdes.String(), Serdes.String()));

        //Create the Kafka Streams instance:
        try (KafkaStreams kafkaStreams = new KafkaStreams(builder.build(), streamsProps)) {
            //A CountDownLatch is created with a count of 1. This means the latch will block until the count is reduced to 0 by calling countDown() once
            final CountDownLatch shutdownLatch = new CountDownLatch(1);
            //A shutdown hook is registered with the JVM. This hook runs when the JVM is terminating (e.g., due to Ctrl+C, System.exit(), or an OS signal like SIGTERM).
            Runtime.getRuntime().addShutdownHook(new Thread(() -> {
                kafkaStreams.close(Duration.ofSeconds(2)); //Closes the Kafka Streams application
                shutdownLatch.countDown(); //Decrements the latch / reduces the latch count from 1 to 0, signaling that the shutdown is complete.
            }));
            TopicLoader.runProducer(); //Use this utility method to create the required topics on the cluster and produce some sample records
            try {
                kafkaStreams.start(); //Start the application
                shutdownLatch.await(); //causes the main thread to block and wait until the latch count reaches 0. This keeps the application running indefinitely until the shutdown hook triggers countDown()
            } catch (Throwable e) {
                System.exit(1); //If an exception occurs (e.g., during await() due to interruption), the application exits with a status code of 1 to indicate an error.
            }
        }
        System.exit(0);
    }
}
```

- Now you can run the basic operations example with this command

```bash
./gradlew runStreams -Pargs=basic
```

and your output on the console should resemble like the below. Take note that it's expected to not have a corresponding output record for each input record due to the filters applied by the Kafka Steams application.

![Basic Operations](assets/images/12.png)

# KTable

## Update Streams

The module Basic Operations defined event streams and mentioned that keys across records in event streams are completely independent of one another, even if they are identical. <br/><br/>

Update Streams are the exact opposite: if a new record comes in with the same key as an existing record, the existing record will be overwritten. This means that when using a KTable, keys are required, although they aren't required when using a KStream. By overwriting records, a KTable creates a completely different data structure from a KStream, even given the same source records.

## Defining a KTable

To define a KTable, you use a **StreamsBuilder**, as with a KStream, but you call **builder.table** instead of **builder.stream**. With the **builder.table** method, you provide an **inputTopic**, along with a **Materialized** configuration object specifying your SerDes (this replaces the **Consumed** object that you use with a KStream):

```java
 StreamsBuilder builder = new StreamsBuilder();
 KTable<String, String> firstKTable =
    builder.table(inputTopic,
    Materialized.with(Serdes.String(), Serdes.String()));
```

## KTable Operations

The KTable API has operations similar to those of the KStream API, including mapping and filtering.

### Mapping

As with KStream, mapValues transforms values and map lets you transform both keys and values.

```java
firstKTable.mapValues(value -> ..)
firstKTable.map((key,value) -> ..)
```

### Filtering

As with KStream, the filter operation lets you supply a predicate, and only records that match the predicate are forwarded to the next node in the topology:

```java
firstKTable.filter((key, value) -> ..)
```

## GlobalKTable

A GlobalKTable is built using the **GlobalKTable** method on the **StreamBuilder**. As with a regular KTable, you pass in a **Materialized** configuration with the SerDes:

```java
 StreamsBuilder builder = new StreamsBuilder();
 GlobalKTable<String, String> globalKTable =
    builder.globalTable(inputTopic,
    Materialized.with(Serdes.String(), Serdes.String()));
```

The main difference between a KTable and a GlobalKTable is that a KTable shards data between Kafka Streams instances, while a GlobalKTable extends a full copy of the data to each instance. You typically use a GlobalKTable with lookup data. There are also some idiosyncrasies regarding joins between a GlobalKTable and a KStream;
