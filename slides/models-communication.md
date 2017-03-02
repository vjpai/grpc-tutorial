# Application Design and Communication Structure

## Flavors of Application Design

As discussed in the context of
[work partitioning](parallel-models.md#partitioning), there are
different approaches and motivations to splitting the work of a
job. Communication between the subtasks is required to coordinate
their actions, and there are various ways to do so. That said, there
are several commonly-used application design paradigms that should be
mentioned specifically, as these software architectures are
widely-used and represent a large class (and possibly a majority) of
real applications across parallel and distributed computing domains:

* Bulk synchronous
* Client-server
* Publish-subscribe
* Controller-worker
* Streaming data
* Pipeline

The following describes the purposes and applicability of each.

## Bulk synchronous

Bulk synchronous models are those where the subtasks can work on entirely
separate pieces of data during each _phase_ of the computation. At the end of
each phase, they typically engage in a collective coordination step such as a
barrier, all-gather, or reduce-scatter to provide the needed data to the other
subtasks for the next phase.

Embarassingly parallel (sometimes called scalably
parallel) represents a class of bulk-synchronous with just one phase.

## Client-server

The client-server model exists when there are two classes of systems in an
application:

* clients, that request data or work from servers
  - e.g., a web browser
* servers, that provide data or completed work to clients
  - e.g., a web server
    * Can just provide data (as in static web content)
	* Or can do work on behalf of client (as in personalized or dynamic data generation)
  - in multi-tier systems, servers can themselves be clients of other servers.

The client is typically less interested in the location or
other physical characteristics of the server and more so in the service that it
provides.

* Client initiates work by naming the server
* That server name must be _resolved_
  - Possibly to a job on a specific backend machine known to the client
  - Or perhaps to a collection of machines that perform this service
	* Load-balanced within a data center to provide scalable performance
	* And/or distributed across multiple locations to provide fast communication
* Client makes a _request_ to the server and the server provides an appropriate _response_
* Requests are typically considered a single atomic unit of work 
  - Doesn't form a long-term association between any particular client
    or server.

Because of the dichotomy between clients and servers, these often have very
different hardware characteristics and use different application-design models
internally.

* Servers often optimized for high scalability and throughput
  - Don't want them bogged down by any one request.

## Streaming data

In client-server communication, a client requests information from a server but
does not set up a longer-term association. In some types of workflows, though,
different subtasks have a long-term predictable pattern of communication
flow. 

* Stream of data flow between associated subtasks
  - A stream is a reliable full-duplex mode of communication, with
    messages on each side of the stream being delivered to the other
    side in order. 
  - Can be more efficient than client-server since the startup
    overhead of the communication only takes place at the beginning of
	a long-lived stream of messages rather than on each new message
* No hierarchy or dichotomy required between different subtasks
  - All can be equal logical participants
  - The data flowing in these streams enables them to coordinate and
    perform appropriate work
* Can be merged with client-server model
  - The client initiates the stream, but after that, both sides are equal
    participants (bidirectional/bidi streaming)
  - or client-only streaming: the client initiates the stream and sends many 
    mesages, after which the server sends a single response
  - or server-only streaming: the client sends a request and the server
	responds with a stream of messages
* Disadvantage
  - Long-term association may make it difficult to dynamically change the
    back-end server associated with a particular stream of communication
	* Limits load-balance opportunities to once per stream instead of once
	  per mesage

## Controller-worker

* Another model with a dichotomy of subtask classes
  - Workers
    - Execute a work loop where each iteration 
	  - requests to do a unit of work from the controller's work queue
	  - does the work associated to process that unit
  - Controller
    - sends back a description (and possibly also data) for one piece of work to do each time it is requested to do so
  - Very different from client-server in work splitup
* Model used for dynamic assignment of work to workers based on worker
  availability and service time. 
  - Minimal work done by controller
	- Just responsible for sending data and responses to the workers
	- Actual processing of the data is done by the workers.
* Long-term association between each worker and its controller
  - Streaming may be a good choice

## Publish-subscribe

* A different dichotomy of subtask classes
  - Subscribers
    - Want information from the publisher
  - Publisher
    - Pushes information to subscribers in a broadcast fashion
* Server-only streaming is highly appropriate
  - Subscriber sends a request for updates on a stream
  - Publisher pushes updates to each of its subscribers

## Pipeline

* Each subtask (_stage_) performs a distinct operation on its data input
  - And then passes the transformed data to the next stage
  - Goal is to keep each stage busy at the same time doing its own work
	- Requires well-balanced stages
	  - Control work at each stage
	  - Load-balance compute resources across different stages
* Streaming communication can be appropriate
* So can asynchronous processing of network activity
