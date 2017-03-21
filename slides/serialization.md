# Data Serialization

Data serialization is a fundamental protocol requirement for all
applications constructed with an
[indirect access naming model](../slides/parallel-models.md). 

## Problems that require data serialization

- How can we take a data structure that may have memory address
  pointers and convert it to a pointer-free format that is suitable
  for transmission on a network or saving in storage?
  * Because standard memory address pointers are meaningless across
    process, on network, or on disk
- Is it efficient or appropriate to use the same data format in memory
  as on a network or disk?

## Commonly-used serialization systems

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
  
## Focus on Protobuf

- Various scalar and structured data objects, plus the ability to
  add user-defined structures
  * An interesting feature is compactness-optimized integers
    where commonly used values such as 0 take only 1 byte but a
    32-bit integer could take up to 5 bytes depending on the value.
- Each object field has a user-defined tag to allow for future expansion
- All fields in a structure are optional and 
  take only minimal space if unused.

## Example of Protobuf Data Specification

``` protobuf
syntax = “proto3”;
message Person {
  string name = 1;
  int32 id = 2;
  string email = 3;

  enum PhoneType {
    MOBILE = 0;
    HOME = 1;
    WORK = 2;
  }

  message PhoneNumber {
    string number = 1;
    PhoneType type = 2;
  }

  repeated PhoneNumber phone = 4;
}
```

* There are some primitive types like int32, string
* User can define `enum` values just as in most programming languages
* User can structure data in the form of `message`s
  - Each field has a numerical tag that is used to identify its field number
    * Field numbers can remain constant for a given field even as a given
      data specification evolves
    * This is because all fields are optional
      - If a primitive field is not present, it gets its default value (e.g.,
        integer 0, empty string)
  - A `message` can contain other `message`s as their fields
* A field can indicate that it is `repeated` which means that there
  are 0 or more entries of that data type
* The protobuf code generator turns messages into objects in any one of a number of languages
  - Provides language-appropriate getter and setter methods
  - as well as field-presence detectors for message types
