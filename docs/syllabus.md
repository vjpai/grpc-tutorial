# Principles and Practices of Microservice-based Programming

At the end of the course, you will be able to:

* Understand conceptual foundations of Microservice-based Applications
  and RPC-based design
  - [Issues in parallel and distributed programming models (work
    partitioning, naming, synchronization) and platforms (from
    multicore to cloud)](../slides/parallel-models.md)
  - [Data serialization](../slides/serialization.md)
  - Monolithic applications or microservices
    * Scalability
    * Facilitating maintenance
    * Multilingual development
  - Examples of multi-layer and peer-to-peer system design
  - Models of communication (e.g., client-server, pub-sub, streaming)
  - RPC-based transport: the why and the how
    * Canonical history (RFCs, etc)
    * Arguments for and against
      - And how modern systems navigate those decisions
    * Models of communication to support (e.g., unary, streaming,
      sync, async)
    * Level of abstraction to support (examples like ZeroMQ)
    * Scalability level to target
    * Polyglot or monolingual
    * API design
* Use gRPC to build microservices:
  - Why are we choosing gRPC
    * NOTE: The class is open-source and we will accept contributed
      lectures on how to use some other RPC platform instead if that’s
      what people want
  - Write simple microservices and clients:
    * Apply the design concepts through realistic and interesting
      course homework assignments
    * Understand design practices on how to define services at the
      right level of abstraction.
  - Know how to load balance and scale micro services
  - Troubleshooting a microservices architecture, common patterns
  - Scaling this out to mobile devices and outside of your
    infrastructure
  - Test, validate and benchmark microservices (or simply rewrite the
    service in C++ or Go)
    * Design of unit tests, integration tests, and benchmarks for
      microservice-based applications
    * Real world example on how to optimize
  - Basic Deployment concepts (epilogue):
    * Proxying, L4/L7
    * Service discovery
    * How do you deploy and manage a fleet of services - tie this into
      the kubernetes course.
