# Using HTTP as an RPC transport

## The Why and the How

* Network infrastructure well-designed to support HTTP
  - Firewalls, load balancers, encryption, authentication, compression, ...
* Basic idea: treat RPCs as references to HTTP objects
  - Encode request method name as URI
  - Encode request parameters as content
  - Encode return value in HTTP response

```
POST /upload HTTP/1.1
Host: www.example.org
Content-Type: application/json

{"msg": "hello"}
```

## HTTP/1.1 and its limitations

* Request-Response protocol
  - Each connection supports pipelining
  - ... but not parallelism (in-order only)
  - Need multiple connections per client-server pair to avoid in-order stalls
    across multiple requests
    * multiple CPU-intensive TLS handshakes, higher memory footprint
* Content may be compressed
  - but headers are text format
* Naturally supports single-direction streaming
  - but not bidirectional

## HTTP/2 in a Nutshell

* One TCP connection for each client-server pair
* Request maps to HTTP/2 Stream
  - Streams are multiplexed using framing
* Compact binary framing layer
  - Messages can be split into HEADERS frames and DATA frames
  - Frames from different streams can be interleaved
  - Prioritization
  - Flow control
  - Server push
* Header compression
* Directly supports bidirectional streaming

## Benefits of HTTP/2

* Case study (anonymized)
  - Service needs to support bidirectional streaming with clients
  - Attempt 1: Directly use TCP sockets
    * Functional in production data center, but not on Internet (firewalls, etc)
    * Programmer responsible for all network management and data transfer
  - Attempt 2: JSON-based RPC over two HTTP/1.1 connections
    * Start two connections: one for request streaming and one for response streaming
    * But they might end up load-balanced to different back-end servers, so the backing servers require shared state
    * Can only support 1 streaming RPC at a time for a client-server pair
  - Attempt 3: HTTP/2 based transport
    * Natural fit

* Performance benefits visible at [HTTP/2 Demo Site](http://www.http2demo.io)
