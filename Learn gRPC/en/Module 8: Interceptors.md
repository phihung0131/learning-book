### **Module 8: Interceptors**

#### **1. Explanation**
gRPC Interceptors are a core mechanism for observing and modifying gRPC calls. They provide a way to intercept incoming (server-side) or outgoing (client-side) RPCs and apply common, cross-cutting logic. Conceptually, they are very similar to middleware in web frameworks (like Express or ASP.NET Core) or Aspect-Oriented Programming (AOP).

*   **`ServerInterceptor`**: Intercepts incoming RPCs on the server before they reach the actual service implementation. When a client makes a call, the request passes through a chain of interceptors before the business logic is executed. This allows you to perform actions for *all* or *some* of your service methods in one central place.

*   **`ClientInterceptor`**: Intercepts outgoing RPCs on the client before they are sent over the network. This allows you to manipulate requests before they leave the client application.

Interceptors can be chained together. The order of execution is important; for example, a logging interceptor might be the outermost to time the entire request, while an authentication interceptor would be inside it to validate credentials.

```ascii
// Server-Side Interception Flow

Client Request --> [Interceptor 1] --> [Interceptor 2] --> [Your Service Logic]
                                                                   |
Client Response <-- [Interceptor 1] <-- [Interceptor 2] <----------+
```

#### **2. Why it matters in real systems**
Interceptors are fundamental to building clean, maintainable, and production-ready gRPC applications. They allow you to separate cross-cutting concerns from your core business logic, keeping your service implementations lean and focused.

*   **Authentication & Authorization**: The most common use case. A server interceptor can extract an authentication token from the request metadata, validate it, and check permissions. If the token is invalid, the interceptor can reject the call immediately with an `UNAUTHENTICATED` status before it ever touches your service code.
*   **Observability (Logging & Metrics)**: An interceptor can log every incoming request and its outcome (success or failure). It can also measure the duration of each call and export metrics to a monitoring system (like Prometheus), giving you visibility into the health and performance of your services.
*   **Distributed Tracing**: Interceptors are the perfect place to handle tracing context. A server interceptor can extract a trace ID from the metadata of an incoming call, and a client interceptor can inject that trace ID into any subsequent outgoing calls, allowing you to trace a single request as it flows through multiple microservices.
*   **Request Validation**: A server interceptor can perform initial, universal validation on incoming requests, such as checking for required metadata headers or validating common message fields, and reject invalid requests early.
*   **Error Handling & Normalization**: An interceptor can catch exceptions thrown by the service logic and transform them into standardized gRPC error responses.

#### **3. Spring Boot (Java) example**

The `grpc-spring-boot-starter` makes it easy to create and register global interceptors.

**1. Server-Side: A Robust Logging Interceptor**

This interceptor will log the start of a call, the end, its duration, and the final status.

```java
package com.example.grpc.server;

import io.grpc.*;
import io.grpc.ForwardingServerCall.SimpleForwardingServerCall;
import net.devh.boot.grpc.server.interceptor.GrpcGlobalServerInterceptor;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.core.annotation.Order;

@GrpcGlobalServerInterceptor
@Order(10) // Set the order of execution for interceptors
public class LoggingServerInterceptor implements ServerInterceptor {

    private static final Logger log = LoggerFactory.getLogger(LoggingServerInterceptor.class);

    @Override
    public <ReqT, RespT> ServerCall.Listener<ReqT> interceptCall(
            ServerCall<ReqT, RespT> call,
            Metadata headers,
            ServerCallHandler<ReqT, RespT> next) {

        final long startTime = System.nanoTime();
        String methodName = call.getMethodDescriptor().getFullMethodName();

        log.info("RPC started: {}", methodName);

        // Wrap the original call to intercept its events, like closing
        ServerCall<ReqT, RespT> wrappedCall = new SimpleForwardingServerCall<>(call) {
            @Override
            public void close(Status status, Metadata trailers) {
                long durationNanos = System.nanoTime() - startTime;
                long durationMillis = durationNanos / 1_000_000;

                if (status.isOk()) {
                    log.info("RPC finished successfully: {}, Duration: {}ms", methodName, durationMillis);
                } else {
                    log.error("RPC failed: {}, Status: {}, Description: {}, Duration: {}ms",
                            methodName, status.getCode(), status.getDescription(), durationMillis);
                }
                super.close(status, trailers);
            }
        };

        // Proceed with the call chain, but with our wrapped call
        return next.startCall(wrappedCall, headers);
    }
}
```

**2. Client-Side: An Interceptor to Add a Request ID**

This interceptor will automatically add a `x-request-id` header to every outgoing RPC, which is essential for traceability.

**First, the interceptor itself:**
```java
package com.example.grpc.client;

import io.grpc.*;
import java.util.UUID;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class ClientRequestIdInterceptor implements ClientInterceptor {

    private static final Logger log = LoggerFactory.getLogger(ClientRequestIdInterceptor.class);
    public static final Metadata.Key<String> REQUEST_ID_KEY =
            Metadata.Key.of("x-request-id", Metadata.ASCII_STRING_MARSHALLER);

    @Override
    public <ReqT, RespT> ClientCall<ReqT, RespT> interceptCall(
            MethodDescriptor<ReqT, RespT> method,
            CallOptions callOptions,
            Channel next) {
        
        return new ForwardingClientCall.SimpleForwardingClientCall<>(next.newCall(method, callOptions)) {
            @Override
            public void start(Listener<RespT> responseListener, Metadata headers) {
                String requestId = UUID.randomUUID().toString();
                log.info("Attaching request ID to outgoing call: {}", requestId);
                headers.put(REQUEST_ID_KEY, requestId);
                super.start(responseListener, headers);
            }
        };
    }
}
```

**Second, configure Spring Boot to apply it globally:**
We create a configuration bean that adds our client interceptor to the global list.

```java
package com.example.grpc.client;

import net.devh.boot.grpc.client.interceptor.GlobalClientInterceptorConfigurer;
import org.springframework.context.annotation.Configuration;

@Configuration
public class GrpcClientConfig {

    @Bean
    public GlobalClientInterceptorConfigurer globalClientInterceptorConfigurer() {
        return registry -> registry.add(new ClientRequestIdInterceptor());
    }
}
```

Now, every call made with a `@GrpcClient`-injected stub will automatically have the `x-request-id` header.

#### **5. Common pitfalls**
*   **Interceptor Ordering**: The order in which interceptors are applied is critical. An authentication interceptor must run before the business logic, and a logging interceptor might need to wrap everything else to measure total time. In Spring, you can use the `@Order` annotation to control this.
*   **Performance Overhead**: Interceptors add latency. While usually negligible, an interceptor that performs slow operations (like a database call or a network request) can become a major bottleneck. Keep interceptor logic fast and non-blocking.
*   **State Management**: Interceptors should ideally be stateless. Storing per-RPC state within an interceptor instance is dangerous because the same instance is shared across many concurrent calls. Use the `io.grpc.Context` for passing state along the lifecycle of a single RPC instead.
*   **Blocking in an Interceptor**: Never block in an interceptor. gRPC servers use a small number of threads to handle many requests. Blocking one of these threads can quickly exhaust the server's capacity.
*   **Forgetting to Delegate**: The most common mistake is to forget to call `next.startCall(...)` in a server interceptor or `next.newCall(...)` in a client interceptor. This will break the chain and the RPC will never be processed.

#### **6. Interview questions**

**Q1: What is a gRPC Interceptor and what is its main purpose?**
**A:** A gRPC Interceptor is a middleware mechanism that allows you to intercept and process RPC calls before they are dispatched to the service logic (on the server) or sent over the network (on the client). Its main purpose is to implement cross-cutting concerns—such as logging, authentication, metrics, and request tracing—in a centralized place, keeping the core service logic clean and decoupled from these infrastructure tasks.

**Q2: You need to implement authentication for all your gRPC services. How would you do it without putting auth logic in every single service method?**
**A:** I would use a `ServerInterceptor`. I'd create a global interceptor that runs for every incoming call. Inside this interceptor, I would:
1.  Extract the authentication token from the request `Metadata` (e.g., from the "Authorization" header).
2.  Validate the token (e.g., check its signature and expiration).
3.  If the token is invalid or missing, I would immediately terminate the call by returning an `UNAUTHENTICATED` status. The service method would never be executed.
4.  If the token is valid, I would proceed with the call chain by invoking `next.startCall()`, allowing the request to continue to the actual service implementation.

**Q3: In a server interceptor, how do you measure the total duration of an RPC and log its final status? Why can't you just use a simple try-finally block around `next.startCall()`?**
**A:** You can't use a simple `try-finally` block because gRPC calls, especially streaming ones, are asynchronous. The `next.startCall()` method returns immediately and does not block until the RPC is finished. To correctly measure duration and get the final status, you must wrap the `ServerCall` object. By creating a `ForwardingServerCall`, you can override its `close()` method. This `close()` method is guaranteed to be called exactly once at the very end of the RPC, whether it succeeds or fails. Inside this overridden method, you can calculate the duration from a start time captured earlier and inspect the final `Status` to log the outcome correctly.

**Q4: How can you automatically add a unique request ID to every outgoing client call for tracing purposes?**
**A:** The best way is to use a `ClientInterceptor`. I would create an interceptor that, in its `interceptCall` method, wraps the `ClientCall`. In the overridden `start()` method of the `ForwardingClientCall`, I would generate a unique ID (like a UUID), add it to the `Metadata` object under a key like `x-request-id`, and then call the original `start()` method to proceed. By registering this as a global client interceptor, it will be automatically applied to every RPC made by the client application.

