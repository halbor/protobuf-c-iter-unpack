## Description

protobuf-c-iter-unpack - fast unpacker for [protobuf-c](https://github.com/protobuf-c/protobuf-c).

Focused on unpacking messages in the same pre-allocated buffer.

## Specification

* Uses protobuf-c structures
* Generates simple for read and use source code
* Supports all field types
* Does not check the required fields during the parse (as protobuf-c)
* Supports packed repeated data (as protobuf-c)
* Supports merge sub-messages (as protobuf-c)
* Written for protobuf-c 1.0.0

## Installation

You must have installed google protobuf library (and python binding)

Install protoc-gen-c-iter script into any dir accessible via $PATH:
```sh
sudo install -m 755 protoc-gen-c-iter /usr/local/bin/
```

## Usage

Generate source and headers:
```sh
# create test.pb-c.h and test.pb-c.c
protoc-c --c_out=. test.proto
# create test.pb-iter.h and test.pb-iter.c
protoc --c-iter_out=. test.proto
```

Example:
```c
// pre-allocate buffer
uint8_t iter_msg_buffer[65535];
size_t iter_msg_buffer_size = sizeof(iter_msg_buffer);

TestMessage* msg = (TestMessage*)iter_msg_buffer;

int ret = test_message__iter_unpack(input_buffer, input_buffer_length, iter_msg_buffer, iter_msg_buffer_size);

if (ret == -2) {
  printf("iter_msg_buffer to small for unpack\n");
} else if (ret == -1) {
  printf("can't unpack message from input_buffer\n");
} else if (ret < 0) {
  printf("unknown error: %d\n", ret);
} else {
  printf("message unpacked sucessful. used %d bytes of iter_msg_buffer\n", ret);
  // handle message
  // ...
}

```

## Benchmark

Tested on:
CentOS 6.3 x86-64, Intel(R) Xeon(R) CPU E5620 @ 2.40GHz 

Competitors:
* 1. protobuf-c with default malloc allocator
* 2. protobuf-c with fastest linear allocator, don't execute free_unpacked method.
* 3. original google c++ parser
* 4. protobuf-c-iter-unpack

Test data:
* 1. message with 10 fields of uint32 type
* 2. 10 x fixed32
* 3. 10 x fixed64
* 4. 10 x string
* 5. [openx test message](http://bid.openx.net/ssrtb_tester)

Result in millions of unpacking per second. Higher is better.

| test    | 1. c, malloc | 2. c, linear | 3. c++, google | 4. iter-unpack |
|:--------|-------------:|-------------:|---------------:|---------------:|
| uint32  |     2.16 m/s |     2.48 m/s |       6.87 m/s |       7.72 m/s |
| fixed32 |     2.80 m/s |     3.37 m/s |      12.78 m/s |      13.02 m/s |
| fixed64 |     2.55 m/s |     3.26 m/s |      12.70 m/s |      11.30 m/s |
| string  |     0.98 m/s |     1.64 m/s |       0.89 m/s |       7.01 m/s |
| openx   |     0.19 m/s |     0.37 m/s |       0.27 m/s |       2.34 m/s |



