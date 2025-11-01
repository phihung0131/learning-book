### **Module 5: Server-Streaming RPC**

#### **1. Explanation**
Server-Streaming RPC is a communication pattern where the client sends a single request to the server, and in return, the server sends back a sequence (a stream) of messages. The client can process each message as it arrives. The server concludes the stream by sending a status notification.

This pattern is ideal for situations where the server needs to return a large amount of data or push updates to the client over a period of time.

**Flow:**
1.  The client sends a single request message to the server.
2.  The server receives the request and can immediately start sending back response messages.
3.  The server sends multiple response messages over the same connection as they become available.
4.  After sending all its messages, the server calls a special method to signal that the stream is complete.
5.  The client processes messages as they arrive and is notified when the stream has ended.

```ascii
   Client                      Server
     |                           |
     |---- Send Request ---->    | (Processes Request)
     | (e.g., SubscribeRequest)  |
     |                           |
     |<--- Send Message 1 ----   |
     |                           |
     |<--- Send Message 2 ----   |
     |                           |
     |<--- Send Message 3 ----   |
     |                           |
     |   ... (and so on) ...     |
     |                           |
     |<--- End of Stream ----    |
     |                           |
```

#### **2. Why it matters in real systems**
Server-streaming RPC enables efficient and responsive communication for data-intensive or real-time applications.

*   **Real-Time Notifications & Subscriptions**: A client subscribes to a feed (e.g., "new comments on a post"). The server keeps the connection open and streams a new message every time a new comment is added. This is far more efficient than the client repeatedly polling the server for updates (the REST approach).
*   **Large Dataset Retrieval**: Imagine a request that returns a million database records. A Unary RPC would require the server to load all one million records into memory, serialize them into a massive response, and send it. This is slow and memory-intensive. With server-streaming, the server can fetch records in batches and stream them one by one or in small chunks, keeping memory usage low on both the server and the client.
*   **Progress Reporting for Long-Running Tasks**: A client can initiate a long-running operation like a data analysis job or a video export. The server can then stream back progress updates (e.g., `{"status": "processing", "percent_complete": 10}`, `{"status": "processing", "percent_complete": 20}`, etc.) until the job is finished.

#### **3. Spring Boot (Java) example**

**1. Define the Server-Streaming RPC in `.proto` (`src/main/proto/notification_service.proto`):**
The `stream` keyword on the *response* type `Notification` indicates this is a server-streaming RPC.

```protobuf
syntax = "proto3";

package com.example.grpc;

option java_package = "com.example.grpc.notification";
option java_multiple_files = true;

service NotificationService {
  // Client subscribes to notifications and server streams them back.
  rpc Subscribe(SubscriptionRequest) returns (stream Notification);
}

message SubscriptionRequest {
  // e.g., could specify topics of interest
  string client_id = 1;
}

message Notification {
  string notification_id = 1;
  string message_text = 2;
  int64 timestamp = 3;
}
```

**2. Server-Side Implementation (`NotificationServiceImpl.java`):**
The implementation method iterates or waits for events, sending a message to the client for each one using `responseObserver.onNext()`. When there are no more messages to send, it calls `responseObserver.onCompleted()`.

```java
package com.example.grpc.server;

import com.example.grpc.notification.Notification;
import com.example.grpc.notification.NotificationServiceGrpc;
import com.example.grpc.notification.SubscriptionRequest;
import io.grpc.stub.StreamObserver;
import net.devh.boot.grpc.server.service.GrpcService;

@GrpcService
public class NotificationServiceImpl extends NotificationServiceGrpc.NotificationServiceImplBase {

    @Override
    public void subscribe(SubscriptionRequest request, StreamObserver<Notification> responseObserver) {
        System.out.println("Server received subscription request from: " + request.getClientId());

        try {
            // Simulate sending a stream of 5 notifications
            for (int i = 1; i <= 5; i++) {
                Notification notification = Notification.newBuilder()
                        .setNotificationId("id-" + i)
                        .setMessageText("This is notification number " + i)
                        .setTimestamp(System.currentTimeMillis())
                        .build();

                // Send the notification to the client stream
                responseObserver.onNext(notification);
                System.out.println("Server sent notification #" + i);

                // Wait for a second to simulate a real-time stream
                Thread.sleep(1000);
            }
        } catch (InterruptedException e) {
            // If an error occurs, notify the client
            responseObserver.onError(e);
        }

        // After sending all messages, mark the stream as completed.
        System.out.println("Server finished streaming notifications.");
        responseObserver.onCompleted();
    }
}
```

**3. Client-Side Implementation (`NotificationClientService.java`):**
The simplest way to consume a server stream is with a **blocking stub**. The RPC call returns an `Iterator`, which blocks on `hasNext()` until a message arrives or the stream is closed.

```java
package com.example.grpc.client;

import com.example.grpc.notification.Notification;
import com.example.grpc.notification.NotificationServiceGrpc;
import com.example.grpc.notification.SubscriptionRequest;
import io.grpc.StatusRuntimeException;
import net.devh.boot.grpc.client.inject.GrpcClient;
import org.springframework.stereotype.Service;
import java.util.Iterator;

@Service
public class NotificationClientService {

    @GrpcClient("notification-service")
    private NotificationServiceGrpc.NotificationServiceBlockingStub blockingStub;

    public void receiveNotifications() {
        System.out.println("Client subscribing to notifications...");
        SubscriptionRequest request = SubscriptionRequest.newBuilder()
                .setClientId("client-abc-123")
                .build();

        Iterator<Notification> responseIterator;
        try {
            // This call initiates the RPC and returns an iterator for the stream.
            responseIterator = blockingStub.subscribe(request);

            // The hasNext() call will block until a message is received or the stream is closed.
            while (responseIterator.hasNext()) {
                Notification notification = responseIterator.next();
                System.out.println("Client received notification: " + notification.getMessageText());
            }
            System.out.println("Client: Stream finished.");

        } catch (StatusRuntimeException e) {
            System.err.println("gRPC failed: " + e.getStatus());
        }
    }
}
```

#### **5. Common pitfalls**
*   **Server Forgetting to Call `onCompleted`**: If the server finishes sending data but never calls `onCompleted()`, the client (especially a blocking client) will hang forever, waiting for the next message. This is a common cause of resource leaks.
*   **Blocking the gRPC Thread on the Server**: The server implementation should not perform long, blocking operations within the `subscribe` method itself before starting to stream. This would delay the entire response. If setup work is needed, it should be done asynchronously to avoid blocking the gRPC worker thread.
*   **Ignoring Client Cancellation**: A client might decide to cancel the RPC midway through a stream (e.g., the user navigates to a different page). A well-behaved server should detect this cancellation (gRPC makes this possible through its context API) and stop its work (e.g., stop querying the database) to save resources.
*   **Unbounded Data Streaming**: If the server is streaming from a source with a very large number of items (like a database table with millions of rows), it should implement some form of flow control or be prepared for the client to cancel the stream. Simply streaming everything without limits can overwhelm the client and the network.

#### **6. Interview questions**

**Q1: What is Server-Streaming RPC and can you give a real-world example where it's more suitable than a Unary RPC?**
**A:** Server-Streaming RPC is a pattern where a client sends a single request and the server responds with a stream of multiple messages. It's perfect for a "live stock ticker" application. With a Unary RPC, the client would have to poll the server every second, creating huge overhead. With server-streaming, the client sends one `SubscribeToStock("AAPL")` request, and the server keeps the connection open, pushing a new `StockPriceResponse` message down the stream every time the price of AAPL changes. This is far more efficient and provides true real-time updates.

**Q2: In a Java gRPC client, what is the difference in how a blocking stub and an async stub handle a server stream?**
**A:**
*   A **blocking stub** makes it simple and synchronous. The call to the RPC method returns an `Iterator<ResponseMessage>`. The client can then use a standard `while(iterator.hasNext())` loop. The `hasNext()` method blocks the thread until the next message arrives from the server or the stream is closed.
*   An **async stub** is non-blocking and callback-driven. When you initiate the call, you must provide a `StreamObserver<ResponseMessage>` implementation. The gRPC client will then invoke your observer's `onNext()` method for each message that arrives, `onError()` if an error occurs, and `onCompleted()` when the stream finishes. This is suitable for event-driven applications or UIs where you cannot block the main thread.

**Q3: If a server is streaming data, what happens if it encounters an error halfway through? How should it inform the client?**
**A:** If the server encounters a critical error after it has already sent some messages, it should not call `onCompleted()`. Instead, it must call `responseObserver.onError(throwable)`. This immediately terminates the stream and sends an error status to the client. The client will not receive any more messages, and its `Iterator` (if blocking) will throw an exception, or its `StreamObserver.onError()` method (if async) will be invoked, allowing it to handle the failure gracefully.

