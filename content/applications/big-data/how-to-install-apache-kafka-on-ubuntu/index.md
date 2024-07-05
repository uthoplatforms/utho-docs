---
slug: "Installing Apache Kafka on Ubuntu: A Step-by-Step Guide"
description: "Discover the process of installing and configuring Apache Kafka, a widely-used open-source platform for stream management and processing originally created by LinkedIn."
keywords: ['Apache','Kafka','streaming','processing','events']
tags: ['ubuntu', 'kafka', 'apache']
published: 2024-05-15
modified_by:
  name: Utho
title: "Setting Up Apache Kafka on Ubuntu"
title_meta: "Installing Apache Kafka on Ubuntu: A Step-by-Step Guide"
authors: ["Pawan Kumar"]
---

[Apache Kafka](https://kafka.apache.org/) commonly referred to as Kafka, is a widely-used open-source platform designed for managing and processing streams of data. Kafka revolves around the concept of events. External entities can send and receive event notifications to and from Kafka independently and asynchronously. Kafka can handle a constant flow of events from various clients, store them, and potentially pass them on to another set of clients for additional processing. It's known for its flexibility, robustness, reliability, self-sufficiency, and ability to provide low latency and high throughput. Originally developed by LinkedIn, Kafka is now maintained by the Apache Software Foundation as an open-source project.

## Before You Begin

1.  If you haven't yet, make sure to create a Utho account and a Compute Instance.

1.  Refer to our Setting Up and Securing a Compute Instance guide for instructions on updating your system. You might also consider setting the timezone, configuring your hostname, creating a limited user account, and strengthening SSH access.

Note: This guide is intended for users who don't have root access. Commands that need higher privileges are marked with `sudo`. If you're unsure about using the `sudo` command, you can refer to the Linux Users and Groups [Linux Users and Groups](/docs/guides/linux-users-and-groups/) guide.
{{< /note >}}

## An Overview of Installing Apache Kafka

Below are the key steps for installing Kafka, each explained in its own section. These instructions are tailored for Ubuntu 20.04 but are applicable to most Debian-based Linux distributions.

1. Install Java
1. Download and Install Apache Kafka
1. Run Kafka
1. Create a Kafka Topic
1. Write and Read Kafka Events
1. Use Kafka Streams for Data Processing
1. Set Up System Files for Zookeeper and Kafka

## Install Java

Before using Apache Kafka, Java must be installed. This guide illustrates how to install OpenJDK, which is an open-source variant of Java.

1. Update your Ubuntu packages by executing the following command.

        sudo apt update

1. Install `OpenJDK` using the apt package manager with the following command.

        sudo apt install openjdk-11-jdk

1. Verify that you have installed the desired version of Java.

             java -version

Java provides basic information about the installation, which may vary depending on the version you have installed.

           {{< output >}}

           openjdk version "11.0.9.1" 2020-11-04
           OpenJDK Runtime Environment (build 11.0.9.1+1-Ubuntu-0ubuntu1.20.04)
           OpenJDK 64-Bit Server VM (build 11.0.9.1+1-Ubuntu-0ubuntu1.20.04, mixed mode, sharing)
           {{< /output >}}

## Obtain and Install Apache Kafka

To install Apache Kafka, you can download the tar archives directly from the Apache website and follow the steps outlined below. The name of the Kafka download may vary depending on the release version. Replace `kafka_2.23-2.8.0.tgz` with the name of your own file.

1. Navigate to the [Apache Kafka Downloads page](https://kafka.apache.org/downloads)and choose the Kafka release you want. We recommend choosing the latest version, which is currently Apache Kafka 2.7. This link takes you to a landing page where you can use either HTTP or FTP to download the tar file.

1. If you downloaded the software onto a different computer than the host, transfer the Apache Kafka files to the host using methods such as `scp`, `ftp`, or another file transfer method. Replace `user` and `yourhost` with your username and host IP address respectively.

        scp /localpath/kafka_2.13-2.7.0.tgz user@192.0.2.0:~/
        {{< note respectIndent=false >}}

Note: If the transfer is not working, ensure that your firewall isn't blocking the connection. Run `sudo ufw allow 22/tcp` to permit `ufw` to enable `scp` transfers.

1. (Optional) To ensure that you have downloaded the file correctly, you can verify it using a SHA512 checksum. You can obtain the checksum file from the [Apache Kafka Downloads page](https://kafka.apache.org/downloads). Each release provides a link to a corresponding `sha512` file. Download this file and transfer it to your Kafka host using `scp`. Put the checksum file in the same directory as your tar file.

Use this command to create a checksum for the tar file:

        gpg --print-md SHA512 kafka_2.13-2.7.0.tgz

Compare the output of this command with the contents of the `SHA512` file. Both checksums should be identical. Please note that this step verifies the integrity of the file, not its authenticity. The checksum output follows this format:

     {{< output >}}
    kafka_2.13-2.7.0.tgz: F3DD1FD8 8766D915 0D3D395B 285BFA75 F5B89A83 58223814 90C8428E 6E568889 054DDB5F ADA1EB63 613A6441 989151BC
    7C7D6CDE 16A871C6 674B909C 4EDD4E28
     {{< /output >}}

1. For added security, verify the file's signature. Download the `.asc` file and the signing keys corresponding to the release from the [Apache Kafka Downloads page](https://kafka.apache.org/downloads). You can find the link to the `KEYS` file at the top of the page. Each release has a link to its `asc` file. After downloading these files, transfer them to your Kafka host using `scp`. Ensure that these files are placed in the same directory as your tar file.

- Import the keys from the KEYS file to install the complete set of keys.

          gpg --import KEYS

- Utilize gpg to authenticate the signature.

          gpg --verify kafka_2.13-2.7.0.tgz.asc  kafka_2.13-2.7.0.tgz

- The output should display the RSA key itself and the individual who signed it.

          {{< output >}}
          gpg: Signature made Wed Dec 16 14:03:36 2020 UTC
          gpg: using RSA key DFB5ABA9CD50A02B5C2A511662A9813636302260
          gpg: issuer "bbejeck@apache.org"
          gpg: Good signature from "Bill Bejeck (CODE SIGNING KEY) <bbejeck@apache.org>" [unknown]
          {{< /output >}}

Note: When using `Gpg`, you may receive a warning stating "key is not certified with a trusted signature." Confirming the authenticity of the signer isn't straightforward, and in most cases, it's unnecessary for regular deployments. However, for enhanced security requirements, follow the steps outlined in Validating Authenticity of a Key on the [Apache Kafka Authentication page](https://www.apache.org/info/verification.html).

1. Use the `tar` utility to extract the files. Once extraction is finished, you can either delete the archive or keep it stored securely in another location on your system.

        tar -zxvf kafka_2.13-2.7.0.tgz

1. (Optional) You can create a new central directory for Kafka and relocate the extracted files to this new Kafka home directory.

        sudo mkdir /home/kafka
        sudo mv kafka_2.13-2.7.0 /home/kafka

## Run Kafka

You can start Kafka directly from the command line. However, make sure to launch the Zookeeper module before running Kafka.

1. Check the configurations in the `kafka_2.13-2.8.0/config/server.properties` file located in your Kafka directory. The default settings should suffice for now. However, we suggest setting the `delete.topic.enable` attribute to `true` at the end of the file. This enables you to delete any topics you create during testing.

       {{< file "/home/kafka/kafka_2.13-2.8.0/config/server.properties" >}}
        ...
       delete.topic.enable = true
       {{< /file >}}

1. Navigate to the Kafka home directory and initiate Zookeeper.

        cd /home/kafka/kafka_2.13-2.8.0/
        bin/zookeeper-server-start.sh config/zookeeper.properties

Note: In most deployments, keep all settings in `zookeeper.properties` at their default values.

1. Open a new terminal session and start Kafka.

        cd /home/kafka/kafka_2.13-2.8.0/
        bin/kafka-server-start.sh config/server.properties

## Creating a Kafka Topic

1. Launch a new terminal session.

1. Navigate to your Kafka directory and create a new topic named `test-events`.

        cd /home/kafka/kafka_2.13-2.8.0/
        bin/kafka-topics.sh --create --topic test-events --bootstrap-server localhost:9092

Kafka confirms the creation of the topic.

           {{< output >}}
            Created topic test-events.
           {{< /output >}}

1. Use the `--list` option to generate a list of all topics in the cluster. You should observe `test-events` listed in the output.

        bin/kafka-topics.sh --list --zookeeper localhost:2181

1. Utilize the `--describe` flag to showcase all details regarding the new topic.

       bin/kafka-topics.sh --describe --topic test-events --zookeeper localhost:2181

Kafka provides a summary of the topic, including details such as the number of partitions and the replication factor.

       {{< output >}}
       Topic: test-events PartitionCount: 1 ReplicationFactor: 1 Configs:
       Topic: test-events Partition: 0 Leader: 0 Replicas: 0 Isr: 0
       {{< /output >}}

## Writing and Reading Kafka Events

Kafka's command-line interface enables you to swiftly test the new topic. Utilize the API to establish a Producer and input some events into the topic. Then, create a consumer to read the events you've written.

1. Launch a new terminal session for the producer and navigate to the Kafka root directory.

        cd /home/kafka/kafka_2.13-2.7.0/

1. Set up a producer and designate a topic for its events. At this point, you're not generating any events; you're merely creating a client capable of sending events. Kafka will display a prompt > to signal that the producer is prepared.

        bin/kafka-console-producer.sh --topic test-events --bootstrap-server localhost:9092

1.Transmit a few key-value pairs to Kafka. Use a `:` to separate the keys and values. You have the option to compose messages with different keys or with the same key. If you provide only a value without specifying a key, the event is allocated a NULL key.

        >key1: This is event 1
        >key2: This is event 2
        >key1: This is event 3

1. Launch a new terminal session for the consumer and navigate to the Kafka root directory.

        cd /home/kafka/kafka_2.13-2.7.0/

1. Set up the consumer, specifying the `test-events` topic it should read from. Use the `--from-beginning` flag to instruct it to read all events starting from the beginning of the topic.

        bin/kafka-console-consumer.sh --topic test-events --from-beginning --bootstrap-server localhost:9092

Note: The Kafka Consumer API offers various options for formatting incoming events. Execute the following command to see the complete list.

    bin/kafka-console-consumer.sh

1. The consumer promptly retrieves any pending events from Kafka in the topic and presents them on the screen. You should observe all the events you previously sent.

       {{< output >}}
       key1: This is event 1
       key2: This is event 2
       key1: This is event 3
       {{< /output >}}

1. Go back to the producer console (ensure the producer is still active) and create another new event.

        >key2: This is event 4

1. The event promptly shows up in the consumer console.

       {{< output >}}
       key2: This is event 4
       {{< /output >}}

1. You can halt the producer or consumer whenever you wish by using the `Ctrl-C` command.

Note: Events are persistent and can be read multiple times. You can create a second consumer for the same topic, and it will read all the events again.

## Processing Data Using Kafka Streams

Kafka Streams is a library designed for executing real-time transformations and analysis on a stream of data. A Kafka Streams application usually functions as both a consumer and a producer. It retrieves new events from a topic, processes the data, and sends its results as events to another topic. Other applications then consume these events from the second topic.

You can utilize the `WordCountDemo` Java application bundled with Kafka Streams to conduct a fast demonstration. This demo consumes events from the `streams-plaintext-input` topic, processes the lines by parsing and counting the words, and stores the word counts in a table. The updated word counts are then transformed into a stream of events and dispatched to the `streams-plaintext-input` topic. The complete code is provided below.

      {{< file "WordCountDemo.java" java >}}
      // Serializers/deserializers (serde) for String and Long types
      final Serde<String> stringSerde = Serdes.String();
      final Serde<Long> longSerde = Serdes.Long();

// Create a `KStream` from the input topic "streams-plaintext-input", where the values of the messages are represented.
// Represent lines of text. For this example, we'll ignore whatever content may be stored.
// in the message keys).

        KStream<String, String> textLines = builder.stream(
        "streams-plaintext-input",
        Consumed.with(stringSerde, stringSerde)
        );

       KTable<String, Long> wordCounts = textLines
    // Split each text line, by whitespace, into words.
    .flatMapValues(value -> Arrays.asList(value.toLowerCase().split("\\W+")))

    // Group the text words as message keys
    .groupBy((key, value) -> value)

    // Count the occurrences of each word (message key).
    .count();

    // Save the running counts as a changelog stream to the output topic..
    wordCounts.toStream().to("streams-wordcount-output", Produced.with(Serdes.String(), Serdes.Long()));
    {{< /file >}}

1. Establish a topic on the Kafka cluster to store the sample word count data.

        cd /home/kafka/kafka_2.13-2.7.0/
        bin/kafka-topics.sh --create --bootstrap-server localhost:9092 --topic streams-plaintext-input

Kafka confirms the creation of the topic.

1. Generate a second topic to store the output of the Kafka Streams application. Set the cleanup policy to compact entries, ensuring that only the updated word counts are retained.

        bin/kafka-topics.sh --create --bootstrap-server localhost:9092 --topic streams-wordcount-output --config cleanup.policy=compact
        Kafka again confirms it has created the topic.

1. Execute the `WordCountDemo` application.

        bin/kafka-run-class.sh org.apache.kafka.streams.examples.wordcount.WordCountDemo

1. Start a producer to send test data to the WordCountDemo stream as `streams-plaintext-input` events.

        bin/kafka-console-producer.sh --bootstrap-server localhost:9092 --topic streams-plaintext-input

1. Set up a consumer to monitor the `streams-wordcount-output` stream. This stream contains the updated results from the `WordCountDemo` application. Configure the formatting properties as follows to produce more readable output.

        bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic streams-wordcount-output --from-beginning --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property print.value=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer

1. Input some test data at the producer prompt.

        This is not the end

1. Verify the word counts are displayed in the consumer window.
       {{< output >}}
       this   1
       is 1
       not 1
       the 1
       end 1
       {{< /output >}}

1. Utilize the producer to input additional test data.

        The end of the line

1. Check the latest output from the consumer. Observe how the word counts have been adjusted.

       {{< output >}}
       the 2
       end 2
       of 1
       the 3
       line   1
       {{< /output >}}

1. To conclude the demo, stop the producer, consumer, and the WordCountDemo application by using `Ctrl-C`.

## Generating System Files for Zookeeper and Kafka

Up to this point, you've been launching Zookeeper and Kafka from the command line within the Kafka directory. While this works fine, it's more convenient to create entries for them in `/etc/systemd/system/` and initiate them using `systemctl` enable.

1. Generate a system file for Zookeeper named `/etc/systemd/system/zookeeper.service`.

        sudo vi /etc/systemd/system/zookeeper.service

1. Edit the file and include the following details. Make sure to use the path names corresponding to your Kafka directory location.

       {{< file "/etc/systemd/system/zookeeper.service" >}}
       [Unit]
       Description=Apache Zookeeper Server
       Requires=network.target remote-fs.target
       After=network.target remote-fs.target

       [Service]
       Type=simple
       ExecStart=/home/kafka/kafka_2.13-2.7.0/bin/zookeeper-server-start.sh /home/kafka/kafka_2.13-2.7.0/config/zookeeper.properties
       ExecStop=/home/kafka/kafka_2.13-2.7.0/bin/zookeeper-server-stop.sh

       Restart=on-abnormal

       [Install]
       WantedBy=multi-user.target
       {{< /file >}}

1. Create another file for the Kafka server named `/etc/systemd/system/kafka.service`.

        sudo vi /etc/systemd/system/kafka.service

1. Edit the file and input the following details. Ensure to provide the full path to your Java application and enter it as the `JAVA_HOME` path.

       {{< file "/etc/systemd/system/kafka.service" >}}
       [Unit]
       Description=Apache Kafka Server
       Requires=zookeeper.service
       After=zookeeper.service

       [Service]
       Type=simple
       Environment="JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64"
       ExecStart=/home/kafka/kafka_2.13-2.7.0/bin/kafka-server-start.sh /home/kafka/kafka_2.13-2.7.0/config/server.properties
       ExecStop=/home/kafka/kafka_2.13-2.7.0/bin/kafka-server-stop.sh

       Restart=on-abnormal

       [Install]
       WantedBy=multi-user.target
       {{< /file >}}

1. Refresh the systemd daemon and launch both applications.

        sudo systemctl daemon-reload
        sudo systemctl enable --now zookeeper
        sudo systemctl enable --now kafka

1. Ensure that both Kafka and Zookeeper are running properly. Check the status of both processes using `systemctl status`.

        sudo systemctl status kafka zookeeper

Both entries should appear as active      

          {{< output >}}
          kafka.service - Apache Kafka Server
          Loaded: loaded (/etc/systemd/system/kafka.service; enabled; vendor preset: enabled)
          Active: active (running) since Thu 2021-01-21 15:13:45 UTC; 4s ago
          ...
          {{< /output >}}

## Shutting Down the Kafka Environment

When you're done with Kafka, it's advisable to gracefully shut down all components and remove any unnecessary logs.

1. Terminate any Kafka consumers, producers, and Kafka Streams applications using the `ctrl-C` command.

1. Shut down Kafka and then Zookeeper using `systemctl stop` commands. If you haven't registered your Kafka application with the `systemd` daemon, shut them down using the `Ctrl-C` command.

        sudo systemctl stop kafka
        sudo systemctl stop zookeeper

1. Remove any test data using the following command:

        rm -rf /tmp/kafka-logs /tmp/zookeeper
