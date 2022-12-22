# Basic kafka setup on AWS using EC2

1.  Create an AWS account and launch an EC2 instance (virtual machine) in a public subnet with an appropriate security group that allows incoming and outgoing traffic on the required ports.
    
2.  Connect to the EC2 instance using a secure shell (SSH) client.
    
3.  Install Java on the EC2 instance. Kafka is written in Java, so you will need to have Java installed on your machine to run Kafka.
    
4.  Download and install Kafka. You can download the latest version of Kafka from the Apache Kafka website. Extract the downloaded tar file, and then navigate to the Kafka directory and start the Kafka server by running the following command:
    

```bash
codebin/kafka-server-start.sh config/server.properties
```

5.  Create a topic. Kafka uses topics to store and publish records. To create a topic, run the following command:
    

```bash
codebin/kafka-topics.sh --create --bootstrap-server localhost:9092 --replication-factor 1 --partitions 1 --topic my-topic
```

6.  Start a producer. A producer is a program that sends messages to a Kafka topic. To start a producer, run the following command:
    

```bash
codebin/kafka-console-producer.sh --broker-list localhost:9092 --topic my-topic
```

7.  Start a consumer. A consumer is a program that reads messages from a Kafka topic. To start a consumer, run the following command:
    

```bash
codebin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic my-topic --from-beginning
```

Here is a diagram illustrating the basic setup:

![](https://d2908q01vomqb2.cloudfront.net/b6692ea5df920cad691c20319a6fffd7a4a766b8/2018/03/02/Kafka3_png.png align="center")

You can also set up Kafka on AWS using managed services such as Amazon Managed Streaming for Apache Kafka (Amazon MSK) and Amazon Simple Queue Service (SQS).

Using Amazon MSK, you can create fully managed Apache Kafka clusters with just a few clicks in the AWS Management Console. Amazon MSK handles the heavy lifting of setting up, scaling, and managing Apache Kafka, including the Apache ZooKeeper cluster.

Using Amazon SQS, you can set up a fully managed message queue service that enables you to send, store, and receive messages between software systems at any volume. Amazon SQS integrates with other AWS services and supports a range of messaging use cases, including storing and transmitting large payloads using Amazon Simple Notification Service (SNS) and Amazon S3.

![](https://user-images.githubusercontent.com/23076/113829213-2147ec80-977d-11eb-8263-a4b5ebe30d14.png align="center")

I hope this helps! Let me know if you have any questions.