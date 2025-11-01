### **Module 3: Unary RPC**

#### **1. Explanation**
A Unary RPC is the simplest type of remote procedure call and the most common communication pattern in gRPC. It mirrors a standard function call: the client sends a single request message to the server and waits for a single response message to be returned.

The flow is as follows:
1.  The client invokes a method on the generated client stub.
2.  The stub serializes the request message (using Protocol Buffers) and sends it to the server over an HTTP/2 connection.
3.  The server receives the request, deserializes it, and invokes the corresponding service implementation method.
4.  The server's logic processes the request and constructs a response message.
5.  The server serializes the response and sends it back to the client as a single message.
6.  The client receives and deserializes the response, completing the call.

This request-response pattern is conceptually identical to a typical REST API call, such as a `GET` or `POST` request.

```ascii
   Client                      Server
     |                           |
     |---- Send Request ---->    |
     | (e.g., GetUserRequest)    |
     |                           |
     |  (Waiting for response)   | Processes Request
     |                           |
     |<--- Send Response ----    |
     | (e.g., GetUserResponse)   |
     |                           |
```

#### **2. Why it matters in real systems**
Unary RPCs are the foundation of most API designs and form the backbone of microservice communication. They are used for the vast majority of standard, state-querying, or command-based interactions.

*   **Resource Retrieval**: Fetching a specific entity, like getting a user's profile by their ID (`GetUser`), retrieving product details (`GetProduct`), or looking up an account balance (`GetBalance`).
*   **Resource Creation/Modification**: Creating new entities, such as registering a new user (`CreateUser`) or updating an existing order (`UpdateOrder`).
*   **Action Triggers**: Performing a specific action that returns a simple success/failure confirmation, like `SendMessage` or `ValidatePayment`.
*   **Direct Replacement for REST Endpoints**: For any standard `GET`, `POST`, `PUT`, or `DELETE` operation in a RESTful system, a Unary RPC is the direct gRPC equivalent, but with the added benefits of performance and strong typing.

#### **3. Spring Boot (Java) example**

**1. Define the Unary RPC in `.proto` (`src/main/proto/user_service.proto`):**
Here, `GetUser` is a classic Unary RPC. It takes one `GetUserRequest` and returns one `GetUserResponse`.

```protobuf
syntax = "proto3";

package com.example.grpc;

option java_package = "com.example.grpc.user";
option java_multiple_files = true;

// The user service definition.
service UserService {
  // Retrieves a user by their ID.
  rpc GetUser (GetUserRequest) returns (GetUserResponse);
}

// Request message for GetUser.
message GetUserRequest {
  string user_id = 1;
}

// Response message for GetUser.
message GetUserResponse {
  string user_id = 1;
  string name = 2;
  string email = 3;
}
```

**2. Server-Side Implementation (`UserService.java`):**
The service implementation extends the gRPC-generated base class. For a Unary RPC, you populate the response, call `responseObserver.onNext()` exactly once, and then call `responseObserver.onCompleted()` to finish the call.

```java
package com.example.grpc.server;

import com.example.grpc.user.GetUserRequest;
import com.example.grpc.user.GetUserResponse;
import com.example.grpc.user.UserServiceGrpc;
import io.grpc.stub.StreamObserver;
import net.devh.boot.grpc.server.service.GrpcService;

import java.util.Map;

@GrpcService
public class UserServiceImpl extends UserServiceGrpc.UserServiceImplBase {

    // Dummy database
    private static final Map<String, String> userDatabase = Map.of(
        "1", "Alice",
        "2", "Bob"
    );

    @Override
    public void getUser(GetUserRequest request, StreamObserver<GetUserResponse> responseObserver) {
        String userId = request.getUserId();
        String userName = userDatabase.get(userId);

        if (userName == null) {
            // Handle user not found case
            responseObserver.onError(io.grpc.Status.NOT_FOUND
                .withDescription("User not found with ID: " + userId)
                .asRuntimeException());
            return;
        }

        GetUserResponse response = GetUserResponse.newBuilder()
            .setUserId(userId)
            .setName(userName)
            .setEmail(userName.toLowerCase() + "@example.com")
            .build();

        // Send the single response back to the client.
        responseObserver.onNext(response);

        // Notify the client that the call is completed.
        responseObserver.onCompleted();
    }
}
```

**3. Client-Side Implementation:**
Using `net.devh.boot.grpc.client.inject.GrpcClient`, we can inject a client stub directly into any Spring bean.

```java
package com.example.grpc.client;

import com.example.grpc.user.GetUserRequest;
import com.example.grpc.user.GetUserResponse;
import com.example.grpc.user.UserServiceGrpc;
import io.grpc.StatusRuntimeException;
import net.devh.boot.grpc.client.inject.GrpcClient;
import org.springframework.stereotype.Service;

@Service
public class UserClientService {

    // Injects a gRPC client stub managed by Spring.
    // "user-service" should match the name configured in application.properties
    @GrpcClient("user-service")
    private UserServiceGrpc.UserServiceBlockingStub blockingStub;

    public void getUser() {
        System.out.println("--- Client: Requesting user with ID 1 ---");
        try {
            GetUserRequest request = GetUserRequest.newBuilder()
                .setUserId("1")
                .build();
            
            // Make the blocking Unary RPC call.
            GetUserResponse response = blockingStub.getUser(request);
            
            System.out.println("Client received response: " + response);

        } catch (StatusRuntimeException e) {
            System.err.println("gRPC failed: " + e.getStatus());
        }

        System.out.println("\n--- Client: Requesting user with ID 99 (not found) ---");
        try {
            GetUserRequest request = GetUserRequest.newBuilder()
                    .setUserId("99")
                    .build();
            blockingStub.getUser(request);
        } catch (StatusRuntimeException e) {
            System.err.println("gRPC failed as expected: " + e.getStatus());
        }
    }
}
```
**`application.properties` for the client:**
```properties
grpc.client.user-service.address=static://127.0.0.1:9090
grpc.client.user-service.negotiationType=PLAINTEXT
```

#### **5. Common pitfalls**
*   **Blocking the Server Event Loop**: On the server side, if the Unary RPC implementation performs a long-running, blocking operation (e.g., a slow database query or a call to another slow service), it can tie up the server's worker threads. This can degrade the server's ability to handle other incoming requests, reducing overall throughput.
*   **Improper Error Handling**: Failing to catch exceptions in the server implementation and convert them into proper gRPC status codes (like `NOT_FOUND`, `INVALID_ARGUMENT`, `UNAUTHENTICATED`). If an uncaught exception occurs, the client will receive a generic and unhelpful `UNKNOWN` or `INTERNAL` error.
*   **Client-Side Blocking**: Using a blocking stub (`.newBlockingStub()`) on a UI thread or another critical, non-blocking thread in the client application will cause it to freeze until the RPC call completes. For responsive applications, an asynchronous stub (`.newFutureStub()`) should be used instead.
*   **Ignoring Deadlines**: Clients should set deadlines on their calls. Without a deadline, a client might wait indefinitely for a response from a slow or unresponsive server, potentially leading to resource exhaustion on the client side.

#### **6. Interview questions**

**Q1: What is a Unary RPC and how does it compare to a REST API call?**
**A:** A Unary RPC is the most basic gRPC communication pattern where a client sends a single request and receives a single response, just like a standard function call. It is functionally equivalent to a typical REST API call, like an HTTP `GET` to fetch data or `POST` to create data. The key differences are in the underlying technology: Unary RPC uses HTTP/2 and Protocol Buffers for higher performance and a strictly-typed contract, whereas REST typically uses HTTP/1.1 and JSON.

**Q2: In a Java gRPC server, the Unary RPC method signature includes a `StreamObserver`. Why is a "stream" observer used for a single, non-streaming response?**
**A:** This is a design choice in the Java gRPC API to maintain a consistent, asynchronous programming model across all RPC types (Unary, client-streaming, server-streaming, and bidirectional). Even for a Unary call, the server uses the `StreamObserver` to send its response. The implementation calls `onNext()` once with the single response message and then immediately calls `onCompleted()` to signal that the RPC is finished. This unified interface simplifies the framework's internal handling of different RPC types.

**Q3: What is the difference between a blocking and a non-blocking (or async) stub in a gRPC client? When would you use each?**
**A:**
*   **Blocking Stub (`.newBlockingStub()`):** This stub provides methods that block the calling thread until the RPC call is complete and the response is received from the server. The method call directly returns the response message. You would use a blocking stub in simple command-line tools, background worker threads, or in situations where the logic is inherently sequential and you need the result before proceeding.
*   **Non-Blocking/Async Stub (`.newFutureStub()` or `.newStub()`):** This stub returns immediately without waiting for the server's response. It returns a future object (e.g., `ListenableFuture` in Java) or requires a callback to handle the response when it arrives. You should always use a non-blocking stub in performance-sensitive or UI-based applications to avoid freezing the main thread, and in any asynchronous programming model.

