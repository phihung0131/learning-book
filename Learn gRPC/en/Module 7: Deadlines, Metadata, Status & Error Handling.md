### **Module 7: Deadlines, Metadata, Status & Error Handling**

#### **1. Explanation**

These three components are critical for building robust, production-ready gRPC services. They provide the necessary context and control for communication that goes beyond the simple exchange of business data.

*   **Deadlines**: A deadline allows a gRPC client to specify how long it is willing to wait for an RPC to complete. It is an absolute point in time. If the deadline is exceeded before the server responds, the RPC is cancelled on both the client and the server. The client receives a `DEADLINE_EXCEEDED` error, and the server can check for cancellation to stop processing and save resources. This is a core feature for preventing cascading failures in a distributed system. A client can also specify a timeout (e.g., "wait for 5 seconds"), which gRPC converts into an absolute deadline.

*   **Metadata**: Metadata is a list of key-value pairs, conceptually similar to HTTP headers. The keys are strings, and the values can be strings or binary data. It's used to send information about the RPC itself, rather than the business data contained in the Protobuf message. Common use cases include sending authentication tokens, tracing IDs for distributed tracing, and client-specific routing information. Metadata can be sent from the client (headers) and returned from the server (headers and trailers).

*   **Status & Error Handling**: gRPC has a well-defined framework for communicating errors. When an RPC fails, the server sends a status code and an optional descriptive string message. Unlike REST where you might overload HTTP status codes, gRPC has its own set of more specific codes (e.g., `NOT_FOUND`, `INVALID_ARGUMENT`, `UNAUTHENTICATED`, `UNAVAILABLE`). On the client side, a gRPC error is received as a `StatusRuntimeException` which contains the status code, description, and any trailing metadata. This provides a structured and language-agnostic way to handle failures.

```ascii
Client App                         Server
   |                                  |
   |---- Request ------------------> |
   |  - Deadline: 5 seconds          |
   |  - Metadata (Headers):          |
   |    "authorization": "jwt..."   |
   |  - Protobuf Message             |
   |                                  |
   | (Waits...)                       | (Processing fails...)
   |                                  |
   |<--- Response (Error) -----------|
   |  - Status: NOT_FOUND            |
   |  - Description: "User not found"|
   |  - Metadata (Trailers):         |
   |    "server-id": "srv-prod-3"    |
   |                                  |
```

#### **2. Why it matters in real systems**

*   **Resilience and Stability (Deadlines)**: In a microservice architecture, one service often calls another, which may call another (a call chain). If a downstream service `C` is slow and does not respond, it can cause service `B` to hang, which in turn causes `A` to hang. This cascading failure can bring down the entire system. By setting deadlines, service `A` can "fail fast" and give up on its request to `B` after a configured timeout, allowing it to handle the error gracefully (e.g., return a cached response, show an error message) instead of becoming unresponsive.

*   **Observability and Security (Metadata)**: Production systems require cross-cutting concerns to be handled uniformly.
    *   **Authentication**: A client must pass an identity token (like a JWT) to the server on every call. Metadata is the standard way to do this.
    *   **Distributed Tracing**: To trace a request across multiple microservices, a unique trace ID must be passed from one service to the next. Metadata is used to propagate this context.
    *   **Multi-tenancy**: To specify which tenant's data a request is for, a `tenant-id` can be passed in the metadata.

*   **Predictable Client Behavior (Status & Error Handling)**: A structured error model allows client applications to build robust logic. For example:
    *   If the client receives `UNAVAILABLE`, it knows the server is temporarily down or unreachable and can safely retry the request after a short backoff period.
    *   If the client receives `INVALID_ARGUMENT`, it knows the request was malformed, and retrying the exact same request will fail again. The client should not retry without fixing the request.
    *   If the client receives `UNAUTHENTICATED`, it knows its credentials have expired and it needs to trigger a re-authentication flow.

#### **3. Spring Boot (Java) example**

**1. `pom.xml` (no new dependencies needed if you are using `grpc-spring-boot-starter`)**

**2. Client-Side: Setting Deadline and Metadata, Catching Errors**

Here we modify the `UserClientService` to demonstrate all three concepts.

```java
package com.example.grpc.client;

import com.example.grpc.user.GetUserRequest;
import com.example.grpc.user.GetUserResponse;
import com.example.grpc.user.UserServiceGrpc;
import io.grpc.Metadata;
import io.grpc.Status;
import io.grpc.StatusRuntimeException;
import io.grpc.stub.MetadataUtils;
import net.devh.boot.grpc.client.inject.GrpcClient;
import org.springframework.stereotype.Service;

import java.util.concurrent.TimeUnit;

@Service
public class UserClientService {

    @GrpcClient("user-service")
    private UserServiceGrpc.UserServiceBlockingStub blockingStub;

    public void getUserWithContext() {
        System.out.println("\n--- Client: Requesting user with ID 1 (with deadline and metadata) ---");
        try {
            // 1. Prepare Metadata
            Metadata headers = new Metadata();
            headers.put(Metadata.Key.of("Authorization", Metadata.ASCII_STRING_MARSHALLER), "Bearer my-secret-jwt");
            headers.put(Metadata.Key.of("x-request-id", Metadata.ASCII_STRING_MARSHALLER), "client-req-123");

            // 2. Attach metadata and set a deadline on the stub
            UserServiceGrpc.UserServiceBlockingStub stubWithContext = MetadataUtils.attachHeaders(blockingStub, headers)
                    .withDeadlineAfter(3, TimeUnit.SECONDS);

            GetUserRequest request = GetUserRequest.newBuilder().setUserId("1").build();
            GetUserResponse response = stubWithContext.getUser(request);
            
            System.out.println("Client received response: " + response.getName());

        } catch (StatusRuntimeException e) {
            Status status = e.getStatus();
            Metadata trailers = e.getTrailers(); // Trailers are metadata sent with the error
            System.err.println("gRPC failed with status: " + status.getCode());
            System.err.println("Description: " + status.getDescription());
            if (trailers != null) {
                System.err.println("Trailers: " + trailers);
            }
        }
    }

    public void getUserThatWillTimeout() {
        System.out.println("\n--- Client: Requesting user with a very short deadline (expecting DEADLINE_EXCEEDED) ---");
        try {
            // Set a deadline that is too short for the server to respond
            UserServiceGrpc.UserServiceBlockingStub stubWithDeadline = blockingStub
                    .withDeadlineAfter(50, TimeUnit.MILLISECONDS);

            GetUserRequest request = GetUserRequest.newBuilder().setUserId("timeout").build();
            stubWithDeadline.getUser(request);

        } catch (StatusRuntimeException e) {
            System.err.println("gRPC failed as expected: " + e.getStatus().getCode());
        }
    }
}
```

**3. Server-Side: Reading Metadata and Returning Errors**

We need an interceptor to read the headers and a modified service to return errors.

**Server Interceptor to read Metadata:**

```java
package com.example.grpc.server;

import io.grpc.*;
import net.devh.boot.grpc.server.interceptor.GrpcGlobalServerInterceptor;

@GrpcGlobalServerInterceptor
public class LogGrpcInterceptor implements ServerInterceptor {

    public static final Context.Key<String> REQUEST_ID_KEY = Context.key("requestId");
    
    @Override
    public <ReqT, RespT> ServerCall.Listener<ReqT> interceptCall(
            ServerCall<ReqT, RespT> call, Metadata headers, ServerCallHandler<ReqT, RespT> next) {

        String requestId = headers.get(Metadata.Key.of("x-request-id", Metadata.ASCII_STRING_MARSHALLER));
        if (requestId != null) {
            System.out.println("Server Interceptor: Found x-request-id in metadata: " + requestId);
            // Put it in the gRPC context to be available downstream
            Context context = Context.current().withValue(REQUEST_ID_KEY, requestId);
            return Contexts.interceptCall(context, call, headers, next);
        }

        System.out.println("Server Interceptor: No x-request-id found.");
        return next.startCall(call, headers);
    }
}
```

**Modified `UserServiceImpl` to handle errors and timeouts:**

```java
package com.example.grpc.server;

import com.example.grpc.user.GetUserRequest;
import com.example.grpc.user.GetUserResponse;
import com.example.grpc.user.UserServiceGrpc;
import io.grpc.Context;
import io.grpc.Status;
import io.grpc.stub.StreamObserver;
import net.devh.boot.grpc.server.service.GrpcService;

@GrpcService
public class UserServiceImpl extends UserServiceGrpc.UserServiceImplBase {

    @Override
    public void getUser(GetUserRequest request, StreamObserver<GetUserResponse> responseObserver) {
        String userId = request.getUserId();

        // Special case to simulate a slow operation for deadline test
        if ("timeout".equals(userId)) {
            try {
                System.out.println("Server: Simulating long running task for 100ms...");
                Thread.sleep(100);
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        }
        
        // Check if the client has already cancelled the request (e.g., deadline exceeded)
        if (Context.current().isCancelled()) {
            System.out.println("Server: Client cancelled the request. No need to proceed.");
            responseObserver.onError(Status.CANCELLED
                    .withDescription("Request was cancelled by the client")
                    .asRuntimeException());
            return;
        }

        // Handle user not found
        if (!"1".equals(userId)) {
            responseObserver.onError(Status.NOT_FOUND
                .withDescription("User not found with ID: " + userId)
                .asRuntimeException());
            return;
        }

        GetUserResponse response = GetUserResponse.newBuilder()
            .setUserId(userId)
            .setName("Alice")
            .setEmail("alice@example.com")
            .build();

        responseObserver.onNext(response);
        responseObserver.onCompleted();
    }
}
```

#### **5. Common pitfalls**
*   **Not Setting Deadlines**: The most common pitfall is clients not setting deadlines. This is dangerous and can lead to resource exhaustion and cascading failures. A best practice is to always set a reasonable default deadline.
*   **Throwing Raw Exceptions**: In the server implementation, throwing a standard `java.lang.RuntimeException` or a checked exception will result in the client receiving a generic `UNKNOWN` or `INTERNAL` error. Always catch internal exceptions and map them to a specific `io.grpc.Status` to provide meaningful errors to the client.
*   **Using Metadata for Business Logic Data**: Metadata should be for cross-cutting concerns. Don't pass something like `product_id` in metadata if it's a core parameter for the RPC. Core parameters belong in the Protobuf message body to be part of the formal API contract.
*   **Ignoring `Context.isCancelled()`**: If an RPC is computationally expensive, the server should periodically check `Context.current().isCancelled()`. If it returns true, the server should abort its work to avoid wasting CPU cycles on a result that will never be used.

#### **6. Interview questions**

**Q1: What is a gRPC Deadline and why is it crucial in a microservices architecture?**
**A:** A deadline is a client-specified time limit for an RPC. It's crucial because it prevents cascading failures. In a chain of service calls (A -> B -> C), if C is slow, a deadline set by A ensures it will give up after a certain time instead of waiting indefinitely. This keeps service A responsive and prevents a single slow service from bringing down the entire system. It's a fundamental pattern for building resilient, production-grade distributed systems.

**Q2: How would you pass an authentication token from a client to a server in gRPC?**
**A:** The standard way is to use Metadata. On the client, I would create a `Metadata` object, add the token as a key-value pair (e.g., key: `"Authorization"`, value: `"Bearer <token>"`), and attach it to the outgoing RPC call. On the server side, I would implement a `ServerInterceptor` to read the "Authorization" key from the metadata of incoming requests, validate the token, and if it's invalid, reject the call with an `UNAUTHENTICATED` status.

**Q3: Compare gRPC error handling with REST (HTTP status codes). What are the advantages of gRPC's approach?**
**A:** REST uses HTTP status codes (like 404 for Not Found, 400 for Bad Request). While functional, they can be ambiguous. gRPC has a more granular and standardized set of status codes specifically designed for RPCs. The advantages are:
1.  **Specificity**: gRPC has codes like `UNAVAILABLE` (suggesting a retry is safe) and `FAILED_PRECONDITION` (a state-related error), which are more descriptive than generic HTTP codes.
2.  **Consistency**: The meaning of each code is well-defined within the gRPC ecosystem, regardless of the programming language.
3.  **Rich Payloads**: A gRPC error status includes not only a code but also a mandatory description string and optional error details via trailing metadata, providing more context to the caller.

**Q4: What is the difference between the `CANCELLED` and `DEADLINE_EXCEEDED` status codes?**
**A:** Both indicate that the RPC did not complete, but they have different causes.
*   `DEADLINE_EXCEEDED` is an automatic, time-based cancellation. The client sets a deadline, and if that time is passed before a response is received, the system automatically cancels the RPC and returns this status.
*   `CANCELLED` indicates an explicit cancellation. This can happen for various reasons, for example, the client application explicitly cancelled the call before the deadline was hit, or a proxy/load balancer in the middle terminated the connection. The server can also receive this if it checks the context after the client has given up for any reason.

