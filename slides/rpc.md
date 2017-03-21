# Remote Procedure Call (RPC)

The mechanism for communication in Microservice-based Applications

## Motivation for RPC

* Recall [fundamentals of parallel and distributed application design](parallel-models.md)
  - [Microservices](microservices.md) are a common answer to [work partitioning](parallel-models.md#partitioning)
  - [Coordination](parallel-models.md#coordination) needs an answer
    * Explicit messaging only, even if the applications run on the same box
* Programmers understand procedure calls well
  - But not directly possible across address spaces or machines
* Provide runtime support to enable procedure call-like data communication interface

## History and Background

* Remote procedure calls have existed in some form since the 1970s
* Key concepts explained and formalized in 1981 PhD dissertation of [Bruce Jay Nelson](https://en.wikipedia.org/wiki/Bruce_Jay_Nelson)
* ["Implementing Remote Procedure Calls"](http://dl.acm.org/citation.cfm?doid=2080.357392) (ACM ToCS, 1983) commonly considered fundamental paper
* Proposed by Sun as an [informational IETF RFC in 1988](https://www.ietf.org/rfc/rfc1057.txt)
* Common feature of those RPC systems:
  - Unary (request-response) data communication in client-server form
  - Synchronous data coordination: client thread blocks between request and response
  
## Modern views of RPC

* Support many models of communication 
* How to determine completion
  * Synchronous
  * Asynchronous, event-driven
  * Asynchronous, promise-driven
  * Choice affected by language and concurrency requirements
- How to deliver data
  * Unary (single request, single response)
  * Streaming
    - Clients and servers can each send and receive multiple messages
  * Choice relates to [parallelism model](models-communication.md) of
    the application

## Components of an RPC system

* Method to associate a client with a server for sending RPCs
  * e.g., socket connect/accept in TCP, channel creation in gRPC
* An _interface definition language_ gives a mechanism to specify
  the supported procedures and their arguments
  * Parsed by a code generator
  * Converted into code that uses the RPC library to coordinate subtasks
* [Serialization layer](serialization.md)
  - Packages language-specific data structures into bytes of raw sequential
    memory for transmission
  - Deserializes those bytes back into data structures on receive
  
## Anatomy of an RPC
* Client gets a _stub_ object for invoking remote procedures on a server
* Client invokes a member function of that stub with the desired procedure 
  name
  - Unary
    1. client sends the method name and serialized argument to the server
    2. server deserializes it, passes it to a _method handler_ that processes it
    3. method handler produces a response
    4. server serializes the response and sends it to client
    5. client deserializes it and uses result
  - Bidirectional Streaming
    1. Client sends method name to the server
    2. Server invokes the appropriate method handler
    3. Both sides can send serialized data to each other as they choose
       - Must have a mechanism to indicate to the other side that work
         is complete
  - Single-sided streaming
    * Hybrid of above versions depending on which side is streaming

## Arguments For RPCs

* Simple and familiar procedure-call-like model
* Well-defined semantics
  - Encompass both coordination and data sharing
* Most communication complexity hidden from application programmer
  - Serialization
  - Network event notification
* Generality
  - Can support variety of [application architectures](models-communication.md)

## Arguments Against RPCs

* A network call is not at all like a procedure call
  - Latency
  - Reliability
* Synchronous processing can create deadlocks
  - For example, when servers are also clients and a cycle arises between RPCs  

## Mitigations

* Latency
  - Streaming can help to tolerate latency for some application architectures
  - So can asynchronous notification
* Reliability
  - Some systems (e.g., gRPC) can restart channels automatically
    * Interacts with TCP connection state transitions (e.g., reset)
	* Can also work with load balancer dynamically
* Deadlocks in synchronous processing
  - Some languages are well-suited for asynchronous processing
  - Use dynamic thread pools at servers
  - Reject an RPC rather than allowing it to get queued in a cycle

## Other questions in RPC systems

* Underlying transport and data framing choices
  - Levels closer to network (e.g., UDP) may offer performance advantages
    * Require customized reliability solutions
	* Will not work in some network configurations (e.g., firewalls)
  - [Levels with higher abstraction (e.g., HTTP/2) may offer better
    generality, ease of use, and applicability](http_transport.md)
	* Designed to work with firewalls and load balancers
	* But has additionial protocol-level data overheads
* Level of abstraction to support
  - Similar tradeoff between performance benefits of minimal abstraction
    vs ease of use with higher abstraction
* Scalability level to target
  - Higher scalability requires tighter interaction with load balancers
    to select servers for best performance
* Polyglot or monolingual?
  - Performance and analysis benefits from tighter integration with
    language implementation and runtime
  - Polyglot systems enable true multilingual microservice implementation
