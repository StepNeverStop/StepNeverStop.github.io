---
title: 学习gRPC过程点滴记录
copyright: true
mathjax: false
top: 1
date: 2020-04-05 11:14:26
categories: Python
tags:
- python
keywords:
description:
---

![](./learn-grpc/gRPC.svg)

此博客用于记录学习gRPC(python)的过程。

<!--more-->

# gRPC

gRPC默认使用协议缓冲池(Protocol Buffers)进行服务器与客户端之间的交互，它是用于序列化结构化数据的成熟的谷歌开源机制。

大致流程是三步：

- Define a service in a `.proto` file.
- Generate server and client code using the `protocol buffer compiler`.
- Use the Python gRPC API to write a simple client and server for your service.

# 安装 gRPC

```shell
$ python -m pip install --upgrade pip
$ python -m pip install grpcio
$ python -m pip install grpcio-tools	# 这个用来编译proto文件
```

# Protocal Buffer 3

官方推荐使用**proto3**语法，因为它更简洁、强大，也支持更多语言。

一个简单的`.proto`文件示例：

```protobuf
syntax = "proto3";

/* SearchRequest represents a search query, with pagination options to
 * indicate which results to include in the response. */
message SearchRequest {
  string query = 1;
  int32 page_number = 2;
  int32 result_per_page = 3;
  repeated int32 int_list=4;
}
```

- 第一行的`syntax`指定portobuf的语法版本，必须在第一行，不过不指定，默认为`proto2`
- `message`用来定义客户端与服务器交互的数据结构，在上边例子中，`string query=1;`称为`field`，string/query/1分别为type/name/value
- 每一个`field`有一个独特的值，用于在二进制消息格式中标识`field`。这个值的范围是[1, 2^29-1(536870911)]，其中，[19000,19999]是禁用的，是为协议缓冲区实现保留的。1-15使用1个字节编码，16-2047使用2个字节，所以常出现的`filed`应该尽量给予小的值
- 注释是C/C++样式，使用`//`和`/*...*/`
- `repeated`字段用于传输**列表数据**，比如上边的`int_list`，我就可以从客户端feed [1,2,3]，或者从客户端返回[1,2,3]

proto与python数据类型对应表：

|                        proto                        |   Python    |
| :-------------------------------------------------: | :---------: |
|                    float/double                     |    float    |
|                int32/sint32/sfixed32                |     int     |
| int64/uint32/uint64/sint64/fixed32/fixed64/sfixed64 |  int/long   |
|                        bool                         |    bool     |
|                       string                        | str/unicode |
|                        Bytes                        |     str     |

默认值：

- strings, empty string
- bytes, empty bytes
- bools, false
- numeric types, 0
- enums, 默认值为第一个定义的枚举值，必须为0

在`message`中使用枚举，可以这么定义：

```protobuf
message Cooking {
    enum VegeType {
        CAULIFLOWER = 0;
        CUCUMBER = 1;
    }
    required VegeType type = 1;
}
```

在python端可以用**以下三种方式**定义枚举字段：

```python
name_pb2.Cooking.CUCUMBER
name_pb2.Cooking.VegeType.CUCUMBER
name_pb2.Cooking.VegeTypeValue.Value('CUCUMBER')
```



对于客户端可RPC调用服务器的方式主要有四种，可以这样写：

```protobuf
syntax = "proto3";

service RouteGuide {
	// 最简单方式，请求->响应
  rpc GetFeature(Point) returns (Feature) {}
	// A server-to-client streaming RPC. 服务器流式响应
  rpc ListFeatures(Rectangle) returns (stream Feature) {}
	// A client-to-server streaming RPC. 客户端流式请求
  rpc RecordRoute(stream Point) returns (RouteSummary) {}
  // A Bidirectional streaming RPC. 双向流式交互
  rpc RouteChat(stream RouteNote) returns (stream RouteNote) {}
}
```

重点在`stream`修饰符。

就拿`rpc GetFeature(Point) returns (Feature) {}`来说，它生成的服务器端函数接口应该是这样的：

`def GetFeature(self, request, context):`

其中，request即为客户端发送的`Point`消息类，context提供特定于rpc的信息，如超时限制，该方法返回给客户端`Feature`类。

# 下载官方示例

```shell
# Clone the repository to get the example code:
$ git clone -b v1.28.1 https://github.com/grpc/grpc
# Navigate to the "hello, world" Python example:
$ cd grpc/examples/python/helloworld
```

```shell
# server
$ python greeter_server.py
# client
$ python greeter_client.py
```

# 生成服务器与客户端代码

```shell
$ python -m grpc_tools.protoc -I ../../protos --python_out=. --grpc_python_out=. ../../protos/[name].proto
```

- `--python_out`：指定编译生成处理 protobuf 相关的代码的路径
- `--grpc_python_out`：指定编译生成处理 grpc 相关的代码的路径
- `-I`：指定proto 文件的**目录**，然后再写文件路径

这条命令会生成两个文件：

- `name_pb2.py`包含我们定义的请求(request)、响应(response)类。
- `name_pb2_grpc.py`包含服务器(server)、客户端(client)类。
  - `&Stub`类被客户端调用服务端RPC
  - `&Servicer`类定义实现服务端代码的接口
  - 方法`add_&Servicer_to_server`，把我们定义的服务器类添加到`grpc.Server`



# 编写服务器

主要步骤：

- 实现服务端接口函数。Implementing the servicer interface generated from our service definition with functions that perform the actual “work” of the service.
- 开启服务器并监听客户端请求，产生响应。Running a gRPC server to listen for requests from clients and transmit responses.

简而言之，就是重载`name_pb2_grpc.py`中`&Servicer`类的所有RPC方法。比如:

```python
class RouteGuideServicer(route_guide_pb2_grpc.RouteGuideServicer):
    """Provides methods that implement functionality of route guide server."""
```

接下来就是开启服务器，监听客户端请求了。

```python
def serve():
    server = grpc.server(futures.ThreadPoolExecutor(max_workers=10))
    route_guide_pb2_grpc.add_RouteGuideServicer_to_server(RouteGuideServicer(), server)
    server.add_insecure_port('[::]:50051')
    server.start()
    server.wait_for_termination()
```

# 编写客户端

创建存根(stub)，从`name_pb2_grpc.py`中实例化`&Stub`类。

```python
channel = grpc.insecure_channel('localhost:50051')
stub = route_guide_pb2_grpc.RouteGuideStub(channel)
```

或者使用`with`上下文管理器：

```python
with grpc.insecure_channel('localhost:50051') as channel:
        stub = route_guide_pb2_grpc.RouteGuideStub(channel)
```

使用存根调用服务器端的方法，可以有同步与异步两种。

同步：`feature = stub.GetFeature(point)`

异步：

```python
feature_future = stub.GetFeature.future(point)
feature = feature_future.result()
```

# 参考

- [gRPC](https://www.grpc.io/docs/quickstart/python/)
- [https://developers.google.com/protocol-buffers/docs/proto3](https://developers.google.com/protocol-buffers/docs/proto3)
- [How to access python enums in protobufs](https://stackoverflow.com/questions/34407696/how-to-access-python-enums-in-protobufs)

