https://github.com/grpc/ - A high performance, open source, general-purpose RPC framework

[rest vs grpc](https://code.tutsplus.com/tutorials/rest-vs-grpc-battle-of-the-apis--cms-30711)

- protobuf vs json
- http/1.1 vs http/2 

The conceptual model used by gRPC is to have services with clear interfaces and structured messages for requests and responses. This model translates directly from programming language concepts like interfaces, functions, methods, and data structures. It also allows gRPC to automatically generate client libraries for you. 

REST supports only the request-response model available in HTTP 1.x. But gRPC takes full advantage of the capabilities of HTTP/2 and lets you stream information constantly. There are several types of streaming.

Server-Side Streaming: The server sends back a stream of responses after getting a client request message. After sending back all its responses, the server’s status details and optional trailing metadata are sent back to complete on the server side. The client completes once it has all the server’s responses.

Client-Side Streaming: The client sends a stream of multiple requests to the server. The server sends back a single response, typically but not necessarily after it has received all the client’s requests, along with its status details and optional trailing metadata.

Bidirectional Streaming: In this scenario, the client and the server send information to each other in pretty much free form (except the client initiates the sequence). Eventually, the client closes the connection.

Strong Typing vs. Serialization: The gRPC service contract has strongly typed messages that are converted automatically from their Protobuf representation to your programming language of choice both on the server and on the client. JSON, on the other hand, is theoretically more flexible because you can send dynamic data and don't have to adhere to a rigid structure. 

browser support: Support for gRPC in the browser is not as mature. Today, gRPC is used primarily for internal services which are not exposed directly to the world. You can use a grpc gateway.

[grpc Guide](https://grpc.io/docs/guides/)
[nginx grpc](https://www.nginx.com/blog/nginx-1-13-10-grpc/)

[faq](https://grpc.io/faq/)

What is gRPC?

gRPC is a modern, open source remote procedure call (RPC) framework that can run anywhere. It enables client and server applications to communicate transparently, and makes it easier to build connected systems.

Read the longer Motivation & Design Principles post for background on why we created gRPC.
What does gRPC stand for?

gRPC Remote Procedure Calls, of course!
Why would I want to use gRPC?

The main usage scenarios:

    Low latency, highly scalable, distributed systems.
    Developing mobile clients which are communicating to a cloud server.
    Designing a new protocol that needs to be accurate, efficient and language independent.
    Layered design to enable extension eg. authentication, load balancing, logging and monitoring etc.

Who’s using this and why?

gRPC is a CNCF project.

Google has been using a lot of the underlying technologies and concepts in gRPC for a long time. The current implementation is being used in several of Google’s cloud products and Google externally facing APIs. It is also being used by Square, Netflix, CoreOS, Docker, Cockroachdb, Cisco, Juniper Networks and many other organizations and individuals.


[Golang tutorial](https://grpc.io/docs/quickstart/go.html)
