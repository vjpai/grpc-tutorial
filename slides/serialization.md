# Data Serialization

Data serialization is a fundamental protocol requirement for all
applications constructed with an
[indirect access naming model](../slides/parallel-models.md). 

* Problems that require data serialization
  - How can we take a data structure that may have memory address
    pointers and convert it to a pointer-free format that is suitable
    for transmission on a network or saving in storage?
  - Is it efficient or appropriate to use the same data format in memory
    as on a network or disk?
* Serialization systems
  - JSON (text-based human-readable format most commonly used in Web
    transactions)
  - **Protobuf (compact binary format, open-sourced by Google)**
    * Most of this course content assumes protobuf, but it is by no means
	  required in gRPC
  - Thrift (compact binary format, open-sourced by Facebook as part of
    Apache Thrift and FBThrift)
  - Flatbuffers (performance-oriented binary format, open-sourced by
    Google and targeted to game developers)
  - Many others
* Focus on Protobuf
  - Various scalar and structured data objects, plus the ability to
    add user-defined structures
	* An interesting feature is compactness-optimized integers
      where commonly used values such as 0 take only 1 byte but a
      32-bit integer could take up to 5 bytes depending on the value.
	* Each object field has a user-defined tag to allow for future expansion
	* All fields in a structure are optional and 
	  take only minimal space if unused.

  
