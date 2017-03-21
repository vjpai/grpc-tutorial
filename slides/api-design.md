# API Design and Implementation

## Application Programming Interfaces (APIs)
* Specify the interactions between clients and servers in a
  microservice-based system
* Expressed as a set of RPCs
  - The method names, and their request and response types
  
## Expressing APIs in Protobuf format

* Declaring the API
  - In the same `.proto` file used to define Protobuf messages
  - Each application-level service is declared along with its set of RPCs
  - Each RPC has one request message argument and one response message type
    * Streaming RPCs add the keyword `stream` to indicate a sequence
	  of messages of that same type

``` protobuf
Insert sample API code here
```

## Turning API into a service implementation

* Define a service in a .proto file using Protocol Buffers IDL
* Generate server and client stub code using the protocol buffer compiler
* Extend the generated server class in your language to fill in the logic of your service
* Invoke it using the generated client stubs

``` go
Insert go sample client code here
```

``` go
Insert go service sample here
```

