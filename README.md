# Basic Kafka Spring Boot Demo

A minimal example of using Apache Kafka with Spring Boot. The practical goal of this project is to build a simple producer-consumer workflow to learn the fundamentals of event-driven architecture.

This project demonstrates:

* Running Apache Kafka locally in **KRaft mode** (no ZooKeeper)
* Sending messages using `KafkaTemplate`
* Receiving messages using `@KafkaListener`
* Triggering message production via a REST endpoint

---

## Tech Stack

* Java 17
* Spring Boot 3.5.x
* Spring for Apache Kafka
* Gradle
* Apache Kafka (KRaft mode)

---

## Project Structure

```text
src/main/java/com/kafka/basic/
├── BasicApplication.java
├── controller/
│   └── MessageController.java
├── service/
│   └── KafkaProducerService.java
└── listener/
    └── KafkaConsumerListener.java

src/main/resources/
└── application.yml
```

---

## Prerequisites

Install the following:

* Java 17+
* Gradle (or use the included Gradle Wrapper)
* Apache Kafka

Verify Java:

```bash
java -version
```

---

# 1. Start Kafka Locally (KRaft Mode)

## Download Kafka

Download Kafka from the official website:

[https://kafka.apache.org/downloads](https://kafka.apache.org/downloads)

Extract it, for example to:

```text
C:\kafka\kafka_2.13-4.2.0
```

## Generate a Cluster ID

```cmd
bin\windows\kafka-storage.bat random-uuid
```

Example output:

```text
abjRYe9vQIuJwfroTBPjpA
```

## Format Storage

### Kafka 4.x

```cmd
bin\windows\kafka-storage.bat format --standalone -t abjRYe9vQIuJwfroTBPjpA -c config\server.properties
```

### Kafka 3.x

```cmd
bin\windows\kafka-storage.bat format -t abjRYe9vQIuJwfroTBPjpA -c config\kraft\server.properties
```

## Start Kafka

### Kafka 4.x

```cmd
bin\windows\kafka-server-start.bat config\server.properties
```

### Kafka 3.x

```cmd
bin\windows\kafka-server-start.bat config\kraft\server.properties
```

Kafka will start on:

```text
localhost:9092
```

## Verify Kafka Is Running

```cmd
bin\windows\kafka-topics.bat --list --bootstrap-server localhost:9092
```

---

# 2. Build the Spring Boot Application

```cmd
gradlew.bat clean build
```

---

# 3. Run the Application

```cmd
gradlew.bat bootRun
```

Stop the application with:

```text
Ctrl + C
```

---

# 4. Application Configuration

## `application.yml`

```yaml
spring:
  kafka:
    bootstrap-servers: localhost:9092
    consumer:
      group-id: demo-group
      auto-offset-reset: earliest
      key-deserializer: org.apache.kafka.common.serialization.StringDeserializer
      value-deserializer: org.apache.kafka.common.serialization.StringDeserializer
    producer:
      key-serializer: org.apache.kafka.common.serialization.StringSerializer
      value-serializer: org.apache.kafka.common.serialization.StringSerializer
```

---

# 5. REST API

## Send a Message

**POST** `http://localhost:8080/api/kafka/send?message=greetings`

### Example Using cURL

```bash
curl -X POST "http://localhost:8080/api/kafka/send?message=greetings"
```

### Example Response

```text
Message sent to Kafka: greetings
```

---

# 6. Expected Console Output

When the consumer receives the message:

```text
Received message: greetings
```

---

# 7. Gradle Dependencies

```gradle
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter'
    implementation 'org.springframework.boot:spring-boot-starter-web'
    implementation 'org.springframework.kafka:spring-kafka'

    testImplementation 'org.springframework.boot:spring-boot-starter-test'
    testImplementation 'org.springframework.kafka:spring-kafka-test'
}
```

---

# 8. Main Components

## `KafkaProducerService`

Uses `KafkaTemplate<String, String>` to send messages to Kafka.

## `KafkaConsumerListener`

Uses `@KafkaListener` to consume messages from `demo-topic`.

## `MessageController`

Exposes a REST endpoint to send messages.

---

# References

* [https://kafka.apache.org/](https://kafka.apache.org/)
* [https://spring.io/projects/spring-kafka](https://spring.io/projects/spring-kafka)
* [https://docs.spring.io/spring-kafka/reference/](https://docs.spring.io/spring-kafka/reference/)

---