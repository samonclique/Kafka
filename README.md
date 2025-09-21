# Kafka
A comprehensive guide/cheatsheet for learning kafka
# Comprehensive Apache Kafka Learning Guide

## Table of Contents
- [What is Apache Kafka?](#what-is-apache-kafka)
- [Prerequisites](#prerequisites)
- [Learning Path](#learning-path)
- [Core Concepts](#core-concepts)
- [Installation and Setup](#installation-and-setup)
- [Basic Operations](#basic-operations)
- [Intermediate Topics](#intermediate-topics)
- [Advanced Topics](#advanced-topics)
- [Production Considerations](#production-considerations)
- [Tools and Ecosystem](#tools-and-ecosystem)
- [Hands-on Projects](#hands-on-projects)
- [Resources](#resources)
- [Certification](#certification)

## What is Apache Kafka?

Apache Kafka is a distributed event streaming platform designed to handle high-throughput, fault-tolerant, real-time data streams. Originally developed by LinkedIn, it's now used by thousands of companies for building real-time streaming data pipelines and applications.

**Key Use Cases:**
- Real-time analytics
- Event-driven architectures
- Log aggregation
- Data integration
- Stream processing
- Messaging systems

## Prerequisites

### Technical Background
- **Java/JVM Knowledge**: Basic understanding (Kafka runs on JVM)
- **Distributed Systems**: Fundamental concepts
- **Linux/Command Line**: Basic proficiency
- **Networking**: TCP/IP, ports, firewalls
- **Data Structures**: Understanding of logs, queues, and distributed data structures

### Recommended Experience
- Experience with databases
- Understanding of publish-subscribe patterns
- Basic knowledge of containers (Docker) - helpful but not required

## Learning Path

### Phase 1: Foundations (1-2 weeks)
1. Understand messaging patterns and event streaming
2. Learn Kafka architecture and core concepts
3. Set up local development environment
4. Practice basic producer/consumer operations

### Phase 2: Core Skills (2-3 weeks)
1. Deep dive into topics, partitions, and offsets
2. Master producer and consumer APIs
3. Understand serialization and deserialization
4. Learn about consumer groups and rebalancing

### Phase 3: Intermediate (3-4 weeks)
1. Kafka Connect for data integration
2. Schema Registry and Avro
3. Stream processing with Kafka Streams
4. Monitoring and troubleshooting

### Phase 4: Advanced (4-6 weeks)
1. Cluster management and operations
2. Security implementation
3. Performance tuning and optimization
4. Multi-cluster setups and disaster recovery

## Core Concepts

### 1. Topics and Partitions
```
Topic: "user-events"
├── Partition 0: [msg1] [msg2] [msg3] →
├── Partition 1: [msg4] [msg5] [msg6] →
└── Partition 2: [msg7] [msg8] [msg9] →
```
- **Topic**: Category of messages (like a table in a database)
- **Partition**: Ordered, immutable sequence of records
- **Offset**: Unique identifier for each record within a partition

### 2. Producers and Consumers
- **Producer**: Publishes records to topics
- **Consumer**: Subscribes to topics and processes records
- **Consumer Group**: Group of consumers working together to consume a topic

### 3. Brokers and Clusters
- **Broker**: Kafka server that stores and serves data
- **Cluster**: Collection of brokers working together
- **Leader/Follower**: Replication model for fault tolerance

### 4. Key Terminology
- **Record**: Key-value pair with timestamp and headers
- **Replication Factor**: Number of copies of each partition
- **ISR (In-Sync Replicas)**: Replicas that are caught up with the leader
- **Commit Log**: Durable, ordered sequence of records

## Installation and Setup

### Option 1: Local Installation
```bash
# Download Kafka
wget https://downloads.apache.org/kafka/2.8.2/kafka_2.13-2.8.2.tgz
tar -xzf kafka_2.13-2.8.2.tgz
cd kafka_2.13-2.8.2

# Start Zookeeper
bin/zookeeper-server-start.sh config/zookeeper.properties

# Start Kafka Server
bin/kafka-server-start.sh config/server.properties
```

### Option 2: Docker Setup
```yaml
# docker-compose.yml
version: '3.8'
services:
  zookeeper:
    image: confluentinc/cp-zookeeper:latest
    environment:
      ZOOKEEPER_CLIENT_PORT: 2181
      ZOOKEEPER_TICK_TIME: 2000

  kafka:
    image: confluentinc/cp-kafka:latest
    depends_on:
      - zookeeper
    ports:
      - "9092:9092"
    environment:
      KAFKA_BROKER_ID: 1
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://localhost:9092
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
```

### Option 3: Cloud Services
- **Confluent Cloud**: Fully managed Kafka service
- **Amazon MSK**: AWS managed Kafka
- **Azure Event Hubs**: Kafka-compatible service

## Basic Operations

### Command Line Interface
```bash
# Create a topic
kafka-topics.sh --create --topic my-topic \
  --bootstrap-server localhost:9092 \
  --partitions 3 --replication-factor 1

# List topics
kafka-topics.sh --list --bootstrap-server localhost:9092

# Describe topic
kafka-topics.sh --describe --topic my-topic \
  --bootstrap-server localhost:9092

# Send messages (producer)
kafka-console-producer.sh --topic my-topic \
  --bootstrap-server localhost:9092

# Consume messages
kafka-console-consumer.sh --topic my-topic \
  --from-beginning --bootstrap-server localhost:9092
```

### Java Producer Example
```java
Properties props = new Properties();
props.put("bootstrap.servers", "localhost:9092");
props.put("key.serializer", "org.apache.kafka.common.serialization.StringSerializer");
props.put("value.serializer", "org.apache.kafka.common.serialization.StringSerializer");

Producer<String, String> producer = new KafkaProducer<>(props);
producer.send(new ProducerRecord<>("my-topic", "key", "value"));
producer.close();
```

### Java Consumer Example
```java
Properties props = new Properties();
props.put("bootstrap.servers", "localhost:9092");
props.put("group.id", "my-group");
props.put("key.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
props.put("value.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");

Consumer<String, String> consumer = new KafkaConsumer<>(props);
consumer.subscribe(Arrays.asList("my-topic"));

while (true) {
    ConsumerRecords<String, String> records = consumer.poll(Duration.ofMillis(100));
    for (ConsumerRecord<String, String> record : records) {
        System.out.printf("Key: %s, Value: %s%n", record.key(), record.value());
    }
}
```

## Intermediate Topics

### 1. Serialization and Schema Management
- **Avro**: Efficient binary serialization
- **JSON**: Human-readable but larger
- **Schema Registry**: Centralized schema management
- **Schema Evolution**: Backward/forward compatibility

### 2. Kafka Connect
```json
{
  "name": "file-source-connector",
  "config": {
    "connector.class": "FileStreamSource",
    "tasks.max": "1",
    "file": "/tmp/input.txt",
    "topic": "file-topic"
  }
}
```

### 3. Consumer Group Management
- Partition assignment strategies
- Rebalancing protocols
- Offset management and commit strategies
- Handling consumer failures

### 4. Message Delivery Semantics
- **At most once**: May lose messages
- **At least once**: May duplicate messages
- **Exactly once**: Guaranteed single delivery

## Advanced Topics

### 1. Kafka Streams
```java
StreamsBuilder builder = new StreamsBuilder();
KStream<String, String> source = builder.stream("input-topic");
source.filter((key, value) -> value.length() > 5)
      .to("output-topic");

KafkaStreams streams = new KafkaStreams(builder.build(), props);
streams.start();
```

### 2. Security
- **Authentication**: SASL/SCRAM, SASL/GSSAPI, mTLS
- **Authorization**: ACLs (Access Control Lists)
- **Encryption**: SSL/TLS for data in transit
- **Network Security**: VPCs, security groups

### 3. Performance Tuning
- **Producer tuning**: batch.size, linger.ms, compression
- **Consumer tuning**: fetch.min.bytes, max.poll.records
- **Broker tuning**: num.network.threads, num.io.threads
- **Storage optimization**: Log compaction, retention policies

### 4. Monitoring and Observability
- **JMX Metrics**: Built-in metrics exposure
- **Kafka Manager/AKHQ**: Web-based management tools
- **Prometheus + Grafana**: Metrics collection and visualization
- **Log aggregation**: ELK stack integration

## Production Considerations

### Cluster Design
- **Multi-broker setup**: High availability
- **Replication factor**: Typically 3 for production
- **Partition strategy**: Based on throughput requirements
- **Hardware sizing**: CPU, memory, storage, network

### Operational Best Practices
- **Monitoring**: Set up comprehensive monitoring
- **Alerting**: Critical metrics and thresholds
- **Backup strategy**: Topic configuration and data
- **Capacity planning**: Growth projections
- **Security hardening**: Follow security best practices

### Disaster Recovery
- **Cross-datacenter replication**: MirrorMaker 2.0
- **Backup and restore procedures**
- **Failover strategies**
- **Recovery time objectives (RTO/RPO)**

## Tools and Ecosystem

### Management Tools
- **Confluent Control Center**: Enterprise management platform
- **AKHQ**: Open-source Kafka GUI
- **Kafka Tool**: Desktop application for Kafka management
- **LinkedIn Cruise Control**: Automated cluster management

### Integration Tools
- **Debezium**: Change data capture
- **Apache Airflow**: Workflow orchestration with Kafka
- **Elasticsearch**: Real-time search and analytics
- **Apache Spark**: Large-scale data processing

### Language Support
- **Java**: Native support, most mature
- **Python**: confluent-kafka-python, kafka-python
- **Go**: Sarama, confluent-kafka-go
- **Node.js**: kafkajs, node-rdkafka
- **.NET**: Confluent.Kafka

## Hands-on Projects

### Beginner Projects
1. **Simple Message Queue**: Basic producer-consumer setup
2. **Log Aggregation**: Collect logs from multiple sources
3. **Real-time Counter**: Count events in real-time using Kafka Streams

### Intermediate Projects
1. **E-commerce Event Pipeline**: Order processing workflow
2. **IoT Data Processing**: Sensor data ingestion and processing
3. **Social Media Analytics**: Tweet processing and sentiment analysis

### Advanced Projects
1. **Multi-region Data Replication**: Cross-datacenter setup
2. **Event Sourcing System**: Complete event-driven architecture
3. **Real-time Recommendation Engine**: ML pipeline with Kafka

## Resources

### Official Documentation
- [Apache Kafka Documentation](https://kafka.apache.org/documentation/)
- [Confluent Documentation](https://docs.confluent.io/)
- [Kafka Improvement Proposals (KIPs)](https://kafka.apache.org/improvement-proposals)

### Books
- **"Kafka: The Definitive Guide"** by Gwen Shapira et al.
- **"Kafka Streams in Action"** by William Bejeck
- **"Building Event-Driven Microservices"** by Adam Bellemare

### Online Courses
- Confluent Developer Courses
- Udemy Kafka courses
- Pluralsight Kafka learning paths
- LinkedIn Learning courses

### Community
- [Kafka Users Mailing List](https://kafka.apache.org/contact)
- [Confluent Community Slack](https://confluentcommunity.slack.com/)
- [Stack Overflow - Apache Kafka](https://stackoverflow.com/questions/tagged/apache-kafka)
- [Reddit r/apachekafka](https://reddit.com/r/apachekafka)

### Practice Environments
- [Confluent Cloud Free Tier](https://confluent.cloud/)
- [Kafka Tutorials](https://kafka-tutorials.confluent.io/)
- Local Docker setups
- Cloud provider managed services

## Certification

### Confluent Certifications
1. **Confluent Certified Developer for Apache Kafka (CCDAK)**
   - Focus: Application development with Kafka
   - Duration: 90 minutes, 60 questions
   - Prerequisites: 6-12 months Kafka development experience

2. **Confluent Certified Administrator for Apache Kafka (CCAAK)**
   - Focus: Kafka cluster administration
   - Duration: 90 minutes, 60 questions
   - Prerequisites: 12-18 months Kafka administration experience

### Preparation Tips
- Hands-on practice with real Kafka clusters
- Study official documentation thoroughly
- Take practice exams
- Join study groups and forums
- Build real-world projects

## Getting Started Checklist

- [ ] Set up local Kafka environment
- [ ] Complete basic producer/consumer tutorial
- [ ] Create your first topic and send messages
- [ ] Understand partitioning and consumer groups
- [ ] Try Kafka Connect with a simple connector
- [ ] Explore Kafka Streams with a basic example
- [ ] Set up monitoring for your local cluster
- [ ] Build a small end-to-end project
- [ ] Join Kafka community forums
- [ ] Plan your learning path based on your role

---

## Contributing
This guide is a living document. Contributions, corrections, and suggestions are welcome. Please submit issues or pull requests to help improve this resource for the Kafka learning community.

**Happy Streaming! 🚀**
