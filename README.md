# Integration Patterns

- A typical system contains, several applications that perform different tasks.
- These applications need to communicate and interact with each other.
- Enabling two applications, interact with each other can be called as integration
- There are several different types of application integrations.
  - File Based Integration
    - One of the application create files that need to be processed and puts them into a well-known folder.
    - Other application watches that folder for files, and if a new file appears in the folder, it is automatically grabbed and processed.
  - Shared Database Integration.
    - One app adds records other reads it.
  - DIrect connection
    - TCP/IP or named pipe connection
    - After initiating the connection, theys start sending messages to each other, directly.
    - Format of the message may be anything. It can be binary or text based, like XML or JSON.
  - Asynchronous Messaging Using a Message Broker
    - Applications send messages to each other in any format with help of an intermediate application
    - Those applications are mostly called as Message Brokers or Message Buses.
    - Message Brokers receive messages from the source or producer applications and forward them to target or consumer applications.

## Advantages of Using Messaging Systems.

- Decouples publishers and consumers.
- Provides a way for reliable, asynchronous task processing and communication.
- Implementing horizontally scalable architectures.
- More efficient than data store polling.

Simple Use Cases - 1

- E-Commerce website needs to update its search index when the product catalog changes.
  - Product Added
  - Product Removed
  - Product Updated
- Ecommerse website needs to send emails to customers asynchronously for various reasons.
  - To validate customers email address
  - To Reset password
  - To inform that an order has been created successfully.
  - To inform that the status of an order has been changed.
  - TO inform about current or upcoming promotions.

## What is RabbitMQ?

- It is a message broker (an intelligent message bus) that receives messages from producers and routes them ot one or more consumers.
- It is open source, written in Erlang
- It is simple and easy to use but powerful
- Its features can be extended with plugins
- Supports several messaging protocols
  - AMQP 0-9-1
  - STOMP 1.0 through 1.2
  - MQTT 3.1.1
  - AMQP 1.0
- Available on Windows, Linux and MAC
- There are also several ready to use Docker images on Docker hub.

Producer = COnnection, Channel, Message => Rabbit MQ =COnnection, Channel, Message => Consumer

## Installing RabbitMQ on Windows

- Following steps can be used to install RabbitMQ
  - See RabbitMQ and Erlang Compatible Versions at https://rabbitmq.com/which-erlang.html
  - Download and install Erlang VM
  - Download and install RabbitMQ
  - Allow Access to 2 Ports
    - 5672: For sending and receiving messages.
    - 15672: For management web interface
  - Enable management plugin in order to access its management web interface
    > Path\RabbitMQServer\rabbitmq_server-{VersionNum}\sbin>rabbitmq-plugins.bat enable rabbitmq_management
  - Restart the serveice and wait for few seconds after service restart (Management plugins need a few more seconds to run after service restart)
  - Login using guest as username and password

## Installing On Docker

- open hub.docker.com search for `rabbitmq`
- open docker desktop
- open command prompt type> docker pull <any management version>
- docker run --rm -d -p 15672:15672 -p 5672:5672 --name sample_rabbit rabbitmq:<version>-management
- open http://localhost:15672 login using guest as username and password.

## Elements of a Messaging System

- Message: Data that is processed. It may be a command, query or an event formation. Message (Routing Information + Actual Data)
- Producer/Consumer: Creates or consumes Message and sends to messga broker
- Broker, Router, Queue: Receive, Deliver and store messages.
- Connection Channel: Used for communication
- Message Queue: List of available messages that are sent by producers. There are usually more than one queue in a messaging system and every queue has a unique name.
- Message Broker: Intermediate element who transmits messages from senders to releated receivers.
- Router/Exchange: It is the component in a message broker that decides which received message should be sent to which queue or queues, based on the configuration. Routing elements are called as "exchanges" in RabbitMQ.
- Connection: Real TCP connection between producer, consumer and message broker
- Channel: Virtual connections in real TCP connections. Actual message transmission is done over channels. There must be at least one channel between a producer, message broker and consumer.
- Binding: Relationship between an exchange and a queue. Binding definition may contain arguments like "routing key" and "headers" that are used to filter messages that will be sent to the bound queue.

## Attributes of Messages, Queues and Exchanges

### Attributes of Messages

- Routing Key: Single or multiple words that are used when distributing a message to the queues.
- Headers: Key value
- Publishing Timestamp: Optional timestamp provided by publisher
- Expiration: Life time for the message in a queue.
- Delivery Mode: It can "Persistent" or "Transient", Persistent messages are written to disk and if RabbitMQ service restarts, they are re-loaded whereas transient messages are lost.
- Priority: Priority of message, 0-255.
- Message Id: Optional unique message id set by the publisher, to distinguish a message.
- Correlation ID: Optional ID for matching a request and response in a Remote Procedure Call (RPC) scenarios.
- Reply To: Optional Queue or exchange name used in request-response scenarios.

### Attributes of Queue

- Name: Unique queue name, max 255 characters UTF-8 String.
- Durable: Whether to preserve or delete this queue when RabbitMQ restarts.
- Auto Delete: Whether to delete this queue if no one is subscribed to it.
- Exclusive: Used only by one connection and deleted when the connection is closed.
- Max length: Maximum number of waiting messages in a queue. Overflow behavior can be set as drop the oldest message or reject the new messages.
- Max Priority: Value that Queue Supports (Upto 255)
- Message TTL: Life time for each message that is added to this queue. If both message and queue has a TTL value, the lowest will be chosen.
- Dead-Letter Exchange: Name of the exchange that expired or dropped messages will be automatically sent.
- Binding Configurations: Associations between queues and exchanges. A queue must be bound to an exchange, in order to receive messages from it.

### Attributes of an Exchange

- Name: Unique name of the exchange.
- Type: Type of the exchange. It can be, "fanout","direct", "topic" or "headers"
- Durability": Same as queue durability. Durable exchanges survice after a service restart.
- Auto Delete: If "true", exchange will be deleted when there is no bound queue left.
- Internal: Internal exchanges can only receive messages from other exchanges.
- Alternate Exchange: The name of the exchange that unroutable messages will be sent.
- Other Arguments (X-arguments): Other named arguments or settings that can be provided when creating an exchange. Their names start with "x-", so these arguments are also called "x-arguments", Mostly related to plugins

### Exchanges:

- Messgae router elements of RabbitMQ.
- Producers does not send messages directly to queues, they send them to exchanges.
- Queues are bound to one or more exchanges with a binding definition or configuration.
- Exchanges receive messages from producers and route them to zero or more queues which are bound to them.
- Exchanges can route a message only to the queues that are bound to them.
- There are four exchange types"
  - Fanout
  - Direct
  - Topic
  - Headers.
- There is atlest one exchagnge in RabbitMQ system. This predefined exchange is called "default exchange" and its type is "direct". Every newly created queue is implicitly bound to this exchange.

### Fanout Exchange

- Simplest Exchange type, It sends all the messages it receives to all the queues that are bound to it.
- It simply ignores the routing information and does not perform any filtering.
- Like a postman that photocopies all the mails and puts one copy into each mailbox.

### Fanout Exchange Demo Using Management Web Interface.

- Durable exchange are not lost, transient will be lost

### Direct Exchange

- Routes message to the queues based on the "routing key" specified in binding definition.
- In order to send a messge to a queue, routing key on the message and the routing key of the bound queue must be exactly the same.

### Topic Exchange

- Use routing key in order to route a message, but does not require full matchm checks the pattern instead.
- Checks whethere the routing key "pattern" of a queue matches the received message's routing key.
- Routing key may include more than one word, separated by dots.
- Routing key of a queue can contain wild cards for matching message's routing key.
- Available wild cards are as folllows:
  - \*: Matches exactly one word
    - "\*.image" will match "something.image", but not "something.someotherthing.image" or "image.jpg.
  - # : Matches zero or more words
    - "image.#" will match "image.jpg" and "image.bitmap.32bit" but not "convert.image"

### Headers Exchange

- Use message headers in order to route a message to the bound queues.
- Ignores the routing key value of the message.
- A message may have many different headers with different values.
- While binding to this type of exchange, every queue specifies which headers a message may contain and whether it requires "all" or "any". It determines the "match all" or "match any" logic for the matching process.
- Sample queue configurations:

  Queue 1 (All headers should match)
  Headers:
  {"x-match":"all"}
  {"job":"covert"}
  {"format":"jpeg"}

  Queue (Any Headers should match)
  Headers:
  {"x-match":"any"}
  {"job":"covert"}
  {"format":"jpeg"}

## Default Exchange

- When a new queue is created on RabbitMQ system, it is implicitly bound to a system exchange called "default exchange", with a routing key which is the same as the queue name.
- Default exchange has no name (empty string).
- The type of default exchange is "direct".
- When sending a message, if exchange name is left empty, it is handled by the "default exchange".

## Exchange to Exchange Binding

- Like binding a queue to an exchange, it is possible to bind an exchange to another exchange.
- Binding and message routing rules are the same.
- When an exchange is bound to another exchange, messages from the source exchange are outed to the destination exchange using the binding configuration.
- Finally, destination exchange routes these messages to its bound queues.

## Alternate Exchange

- SOme of the messages that are published to an exchange may not be suitable to route to any of the bound queus.
- They are unrouted messages.
- They are discarded by the exchange, so they are lost.
- In order to collect these messages, an "alternative exchange" can be defined for any exchange.
- Alternate exchange can be defined by setting the "alternate-exchange" key for an exchange.
- Any unrouted message is finally sent to defined "alternate-exchange".
- Any existing exchange can be set as an "alternate exchange" for another exchange.
- Fanout exchanges, which do not perform any filtering, are good for using as an "alternate exchange"

## Push Vs Pull

### Push Model

- Consumer application subscribes to the queue and waits for messages.
- If there is already a message on the queue.
- Or when a new message arrrves, it is automatically sent (Push) to the consumer application.
- This is the suggested way of getting messages from queue.

### Pull Message.

- Consumer application does not subscribe to the queue.
- But it constantly checks (polls) the queue for new messages.
- If there is a message available on the queue, it is manually fetched (pulled) by the consumer application.
- Even though the pull mode is not recommended, it is the only solution when there is no live connection between message broker and consumer applications.

## Work Queues

- Work queues are used to distribute tasks among multiple workers.
- Producers add tasks to a queue and these tasks are distributed to multiple worker applications.
- Pull or push models can be used to distribute tasks among the workers.
  - Pull Model: Workers get a message from the queue when they are available to perform a task.
  - Push Model: Message broker system sends (pushes) messages to the available workers automatically.
- Sample scenario: sending emails.
- Issues
  - 1. Messages must not be lost if the broker application crashes or restarts.
  - 2. Every message must be delivered and processed exactly one time.
  - 3. If the worker application crashes or fails to process a task, it must be re-added to the queue and delivered later.
  - 4. There must be a limit for retrying failed tasks, otherwise the queue may be filled up by constantly failing tasks.
  - 5. Task workload should be fairly distributed among the workers.

## Request/Reply

- Request-Reply or Request-Response pattern is used when the publisher of the message, which called the requestor, needs to get the response for its message.
- The request messgae mostly contains a query or a command.
- using request-reply pattern, remote procedure call (RPC) scenarios can also be implemented.
- In request-reply pattern , there are at least two queues;
  - 1 for the requests
  - 1 for the replies or responses. This queue is also named as the callback queue for RPC scenarios.

Requestor - Request -> Request Queue - Request -> Replier(Processor)  
Requestor <- Response - Response Queue <- Response - Replier(Processor)
