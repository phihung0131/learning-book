### **Module 1: What is gRPC and Why Use gRPC Instead of REST?**

#### **1. Explanation**
gRPC (gRPC Remote Procedure Call) is a modern, open-source, high-performance RPC framework developed by Google that can run in any environment. It allows a client application to directly call methods on a server application on a different machine as if it were a local object.

At its core, gRPC is built on two fundamental technologies:
*   **HTTP/2**: Unlike REST, which typically uses HTTP/1.1, gRPC leverages HTTP/2. This provides significant performance benefits like multiplexing (sending multiple requests over a single connection), server push, and header compression.
*   **Protocol Buffers (Protobuf)**: By default, gRPC uses Protocol Buffers as its Interface Definition Language (IDL) and message interchange format. Protobuf is a mechanism for serializing structured data into a compact binary format, which is much smaller and faster to parse than text-based formats like JSON used in REST.

The workflow is straightforward: you define a `service` in a `.proto` file, specifying the methods that can be called remotely along with their parameters and return types. The gRPC toolchain then generates client and server code (stubs) in any of gRPC's supported languages. This creates a strongly-typed, explicit contract between the client and server.

<br>

**Diagram: gRPC vs. REST**

```ascii
     gRPC                                     REST
┌──────────────┐                       ┌──────────────┐
│  Client App  │                       │  Client App  │
└───────┬──────┘                       └───────┬──────┘
        │                                      │
        │ Call Method (e.g., GetUser)          │ HTTP GET /users/123
        │                                      │
┌───────▼──────┐                       ┌───────▼──────┐
│  Client Stub │ (Generated Code)      │ HTTP Library │ (e.g., Axios, Fetch)
└───────┬──────┘                       └───────┬──────┘
        │                                      │
        │ Protobuf (Binary) over HTTP/2        │ JSON (Text) over HTTP/1.1
        │                                      │
┌───────▼──────┐                       ┌───────▼──────┐
│ gRPC Server  │                       │  REST API    │
└──────────────┘                       └──────────────┘
```

#### **2. Why it matters in real systems**
Choosing gRPC over REST is a significant architectural decision driven by the need for performance, efficiency, and robust contracts, especially in complex distributed systems.

*   **High-Performance Microservices:** In a microservices architecture, services communicate frequently. The overhead of REST's text-based JSON payloads and HTTP/1.1 connections can create significant latency. gRPC's use of binary Protocol Buffers and HTTP/2's connection multiplexing dramatically reduces network latency and CPU usage, making inter-service communication faster and more efficient.
*   **Real-Time Streaming:** gRPC has first-class support for bidirectional streaming, where both the client and server can send a sequence of messages on a single connection. This is ideal for applications like live financial data feeds, real-time location tracking in ride-sharing apps, or interactive chat services, which are cumbersome to implement with REST's request-response model.
*   **Polyglot Environments:** In large enterprises, different services are often written in different programming languages. gRPC's code generation capabilities create idiomatic client and server stubs for numerous languages from a single `.proto` file, ensuring seamless interoperability.
*   **Mobile and IoT Devices:** For battery and network-constrained devices, efficiency is critical. gRPC's smaller binary payloads consume less bandwidth and require less CPU to process compared to JSON, making it an excellent choice for mobile clients and IoT devices.

#### **3. Spring Boot (Java) example**
In Spring Boot, integrating gRPC is typically done using a third-party library, as there is no official starter. A popular choice is the `grpc-spring-boot-starter`.

**1. `pom.xml` Dependencies:**
You would add dependencies for gRPC, Protocol Buffers, and the starter.

```xml
<dependency>
    <groupId>net.devh</groupId>
    <artifactId>grpc-server-spring-boot-starter</artifactId>
    <version>2.15.0.RELEASE</version>
</dependency>
```

**2. Define Service in `.proto` file (`src/main/proto/greeting.proto`):**

```protobuf
syntax = "proto3";
option java_multiple_files = true;
package com.example.grpc;

// The greeting service definition.
service Greeter {
  // Sends a greeting
  rpc SayHello (HelloRequest) returns (HelloReply) {}
}

// The request message containing the user's name.
message HelloRequest {
  string name = 1;
}

// The response message containing the greetings
message HelloReply {
  string message = 1;
}
```

**3. Implement the Service:**
The build process (using a Maven plugin) generates Java classes from the `.proto` file. You then implement the server-side logic by extending the generated base class and annotating it with `@GrpcService`.

```java
import net.devh.boot.grpc.server.service.GrpcService;
import io.grpc.stub.StreamObserver;

@GrpcService
public class GreeterService extends GreeterGrpc.GreeterImplBase {

    @Override
    public void sayHello(HelloRequest req, StreamObserver<HelloReply> responseObserver) {
        HelloReply reply = HelloReply.newBuilder()
                .setMessage("Hello ==> " + req.getName())
                .build();
        responseObserver.onNext(reply);
        responseObserver.onCompleted();
    }
}
```

#### **4. Go example**
Go has excellent, first-class support for gRPC.

**1. Get Go gRPC packages:**
```shell
go get google.golang.org/grpc
go get google.golang.org/protobuf/cmd/protoc-gen-go
go get google.golang.org/grpc/cmd/protoc-gen-go-grpc
```

**2. Define Service in `.proto` file (`protos/greeting.proto`):**
(Same `.proto` file as the Java example, but with a Go package option)
```protobuf
syntax = "proto3";
option go_package = "github.com/your-repo/project/protos";
package protos;

service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply) {}
}

message HelloRequest {
  string name = 1;
}

message HelloReply {
  string message = 1;
}
```

**3. Generate Go code:**
Run the `protoc` compiler to generate the necessary Go files.
```shell
protoc --go_out=. --go_opt=paths=source_relative \
    --go-grpc_out=. --go-grpc_opt=paths=source_relative \
    protos/greeting.proto
```
This generates `greeting.pb.go` and `greeting_grpc.pb.go`.

**4. Implement and Run the Server:**

```go
package main

import (
    "context"
    "log"
    "net"
    "google.golang.org/grpc"
    pb "github.com/your-repo/project/protos" // Import generated package
)

// server is used to implement the Greeter service.
type server struct {
    pb.UnimplementedGreeterServer
}

// SayHello implements the Greeter service SayHello method.
func (s *server) SayHello(ctx context.Context, in *pb.HelloRequest) (*pb.HelloReply, error) {
    log.Printf("Received: %v", in.GetName())
    return &pb.HelloReply{Message: "Hello " + in.GetName()}, nil
}

func main() {
    lis, err := net.Listen("tcp", ":50051")
    if err != nil {
        log.Fatalf("failed to listen: %v", err)
    }
    s := grpc.NewServer()
    pb.RegisterGreeterServer(s, &server{})
    log.Printf("server listening at %v", lis.Addr())
    if err := s.Serve(lis); err != nil {
        log.Fatalf("failed to serve: %v", err)
    }
}
```

#### **5. Common pitfalls**
*   **Initial Setup Complexity:** The need to define services in `.proto` files and run a code generation step adds an initial layer of complexity compared to quickly spinning up a REST endpoint.
*   **Limited Browser Support:** Browsers cannot directly call a gRPC service because it relies on HTTP/2 features that are not exposed through standard browser APIs. This requires a proxy layer like gRPC-Web with Envoy to translate requests.
*   **Less Human-Readable:** The binary nature of Protobuf makes it difficult to inspect payloads on the wire for debugging without specialized tools (like Wireshark with Protobuf dissectors or gRPC-specific debug tools), unlike REST's plain-text JSON.
*   **Tight Coupling:** The client and server are tightly coupled through the `.proto` contract. While this ensures type safety, it also means that changes to the service definition often require both client and server to be updated and redeployed.

#### **6. Interview questions**

**Q1: When would you choose gRPC over REST, and why?**
**A:** I would choose gRPC over REST primarily for high-performance internal microservice communication. The key reasons are: 1) **Performance:** gRPC uses HTTP/2 and binary Protocol Buffers, resulting in significantly lower latency and network usage compared to REST over HTTP/1.1 with text-based JSON. 2) **Streaming:** gRPC natively supports bidirectional streaming, which is essential for real-time applications like live updates or data feeds, a feature not available in standard REST. 3) **Strict API Contracts:** The use of Protocol Buffers enforces a strongly-typed contract between services, which reduces runtime errors and improves reliability in a complex, polyglot system.

**Q2: What are the main disadvantages of using gRPC?**
**A:** The main disadvantages are: 1) **Limited Browser Support:** Direct browser-to-gRPC communication isn't possible without a proxy like gRPC-Web, which adds architectural complexity. 2) **Learning Curve:** The workflow involving `.proto` files, code generation, and understanding HTTP/2 concepts presents a steeper learning curve than traditional REST. 3) **Payload Readability:** Debugging can be more challenging because the binary payloads are not human-readable without specific tooling, unlike JSON. 4) **Tight Coupling:** The strict contract means both client and server must be updated if the API definition changes.

