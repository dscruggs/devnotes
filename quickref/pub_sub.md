# Pub/Sub Architecture

## Overview

- pub/sub systems are opposite of point-to-point systems
- point to point means the sender is aware of the receiver and things go directly between them
- pub sub means the senders of messages go to a single broker, and the publisher doesn't care where it's going. The receivers just subscribe and get those messages from the broker
- pub sub can be thought of by a few other names:
    - publisher, producer, sender
    - subscriber, consumer, receiver
- pub/sub enables event driven design, where events and triggers cause a chain of events between decoupled systems. This can help with a microservice architecture
- and basically the receivers/subscribers can do whatever they want with the things they're subscribed to. The broker doesn't care either it just gives the messages to subscribers
- `RabbitMQ` is a popular message broker that implements the `AMQP` protocol
    - AMQP is an interoperable protocol that's used by a lot of implementations. Things like Kafka and SQS and google pub/sub don't really use this
- `MQTT` and `STOMP` are two other popular protocols
    - MQTT is designed for small IoT devices and as such is optimized to be lightweight and energy efficient.
    - STOMP is designed for web applications and is designed to be simple and easy to use.
    - AMQP is a sophisticated, feature-rich protocol that's great for complex routing, filtering, and delivery requirements.

---

## Publishers and queues

Some rabbitMQ terminology which applies to pub/sub:

- **Exchange:** Routes incoming messages to one or more queues.
- **Binding:** The rule/link between an exchange and a queue, using a routing key to decide where messages go.
- **Queue:** A server-side buffer that holds messages until consumers process them.
- **Channel:** A lightweight virtual session inside a connection for declaring queues/exchanges and publishing messages.
- **Connection:** The underlying TCP connection to the RabbitMQ server.

### Exchange types

- **Direct:** Routes messages only when the routing key exactly matches a queue's binding key.
- **Topic:** Routes messages using wildcard pattern matching between the routing key and binding patterns.
- **Fanout:** Broadcasts messages to every bound queue, ignoring routing keys.
- **Headers:** Routes based on message header values instead of the routing key.

- queues can be durable or transient (meaning surviving restarts or not). There can also be exclusive or auto delete properties
- the big benefit of a pub/sub with a queue and exchange is you can decouple from a direct point-to-point messaging system

---

## Consumers

- so basically publishers just put things in a queue and forget it. Consumers need to find what's relevant to them in the queue (depending on the type of exchange)
- queues can be set up to work with 0, 1, or many consumers. It can be exclusive or non exclusive. If there's many things are usually done in round robin fashion unless there's priority
- there's flexible pattern matching that can be used to find items of interest in the queue

---

## Delivery

- what about when messages fail? The producer doesn't really know
- `dead letter queue` is a concept of a separate queue for things that fail which can then be processed separately
- basically a consumer has to acknowledge receipt of the item from the queue, and typically your options to acknowledge are:
    - ack: yes I got it
    - nack and requeue (negative ack): did not process successfully but put it back in the same queue
    - nack and discard: did not process successfully but get rid of it
- you also need to decide how to handle if you don't get any response from the consumer (deliver again, or only deliver once and discard)
- basically the decision for discard vs requeue depends on the consumer's errors. If its transient you want to retry, if it's logical you want to discard since it won't work with retry

---

## Serialization

- `serialization` just means to convert a struct/dict/whatever language-specific structure into something ready to be sent over a network
- JSON is a common serialization format, but for AMQP you can use raw bytes. JSON isn't very efficient so you can use something like `MessagePack` to do binary
- `protocol buffers` or `avro` are other alternatives. Basically just make sure to think about your schema and the use case when deciding a serialization format

---

## Scalability

- advantages of distributed systems / large clusters are high availability, scalability, and redundancy
- you have to think about resources in terms of compute (cpu/GPU), memory (RAM), disk, and network bandwidth
- distributed systems means more bandwidth but more management overhead and complexity
- `backpressure` is something to worry about where your producers are creating things faster than consumers are consuming. This can lead to OOM. mostly empty queues = healthy queues
- consumers can `prefetch` messages in batches and store them on basically local queue for processing later
- `quorum queues` are like many classic queues distributed over multiple nodes. useful for resiliency but slower than classic queues
