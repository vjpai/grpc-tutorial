# Constructing Parallel and Distributed Applications

## Work Partitioning

The fundamental requirement when building any parallel or distributed
application is some form of work partitioning into subtasks.

* Concurrency
  - Executing different parts of the job at the same time
  - Requires work to be possible without continuous tight interactions
    between subtasks
* Resource availability
  - Not all resources are available everywhere
	* e.g., mobiles are very light clients and often store/process data elsewhere
  - So some parts of the job must run closer to those resources

## Coordination

But any time we have different subtasks, we must have some way of
coordinating their work.

* How to name resources that others want to access?
* How to coordinate access to those resources?
* How to let subtasks notify each other about their progress?
* These are answers that directly affect the programming model (the
  programmer's view of the platform)
  - But, efficiency should impact the choice of coordination model

## Choices in naming
* Direct access
  - Programmer sees the common resource as though it were a local
    variable, function, etc. within the same program scope
	* e.g., shared variable in multi-threaded application
* Indirect access
  - Program explicitly indicates that particular resource comes from
    somewhere else
	* Obvious example: a URL
	* Less obviously: a `chan` in `golang` or a `Queue` in ruby
* Models are not exclusive
  - Some parts of the system may communicate with direct access and others with indirect
  - e.g., a client communicating with a multithreaded database server
	* The client must explicitly request data from the server, but the server subtasks will share data implicitly with each other
* Note that these are programming issues, not necessarily system issues
  - A distributed system may implement direct access with appropriate
    language and/or runtime support
  - A runtime could implement indirect access on top of an actual
    shared memory platform

## Coordination and notification

Both coordination and notification are part of a broader category
called *concurrency control* or *synchronization*. The mechanisms for
these are often tied to the underlying naming model.

* Indirect access
  - Coordination and notification are essentially implicit in the
    request from one subtask to access the resource at another subtask
* Direct access
  - Coordination and notification must usally be done separately from the resource access
  - Common paradigms
	- Mutual exclusion (locking): use a special data object (called a mutex) to ensure that only one subtask can access this resource at the same time
	- Condition variables: use a special data object (called a condition variable, typically jointly with a mutex) to wait on or signal another subtask
	- More advanced variations: lock-free access, atomic access, etc.

## Platforms issues

Parallel and distributed programs are often tightly tied to the
platforms used for running them. These platforms are described in
increasing order of scale, along with their special characteristics.

* Multicore (a processor chip)
  - Different running tasks can be scheduled on different cores
  - Hardware support for direct access to shared memory
  - Shared cache for extremely fast fine-grained sharing
* Coherent multi-socket (a box/node)
  - Hardware support for direct access to shared memory
  - Cache sharing within a single chip, but coherent direct access to shared memory across the platform
	* Excessive sharing between tasks scheduled on different chips may slow down performance as data ping-pongs between caches
	* But scheduling across chips should help improve scalability, so there is a tradeoff
	* Aim to reduce sharing
* Cluster
  - Nodes connected through network switches or routers, depending on size
  - Typically use commodity interconnect (Ethernet)
	* No hardware support for direct access across nodes
  - Some clusters use special-purpose (Infiniband)
	* May provide hardware support for direct access and synchronization
* Data-center
  - Multiple clusters colocated in a single facility
  - Complex multi-layer network topologies
	* Often with high-connectivity like Fat Trees or flattened butterflies
  - Often shielded from public Internet
* Cloud
  - Cloud servers are typically virtual machine instances in a data center
  - Diversity of client platforms: IoT, mobile, virtual machine instances
  - Often connected to public Internet

  
  
