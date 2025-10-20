### **Part 6: Test Profiles and Configuration**

Properly managing configuration is what separates a brittle, hard-to-maintain test suite from a robust and flexible one. In this section, we'll master the tools Spring Boot provides for controlling the environment in which your tests run.

#### **Topic 6.1: How to Create and Activate Separate Profiles for Tests**

We've touched on this, but let's formalize the concept. A **profile** is a named set of configuration properties. By default, your application runs with no active profiles, loading properties from `application.properties` or `application.yml`.

**Why create a "test" profile?**

*   **Isolation:** To ensure your test configuration (like connecting to a test database) never accidentally leaks into your production build.
*   **Control:** To enable or disable features specifically for the test environment. For example, you might disable an email-sending service or a connection to a live payment gateway during tests.
*   **Performance:** To use settings optimized for testing, like using a faster password encoder or disabling expensive startup tasks.

**How to Implement:**

1.  **Create the Profile-Specific File:** In `src/test/resources/`, create a file named `application-{profileName}.yml` (or `.properties`). The standard convention is `application-test.yml`.
2.  **Activate the Profile in Your Test:** Use the `@ActiveProfiles("test")` annotation on your test class.

```java
@SpringBootTest
@ActiveProfiles("test") // This annotation is the key
public class MyServiceIntegrationTest {
    // ...
}
```

When this test runs, Spring will load properties from `application.yml` first, and then load properties from `application-test.yml`, with the test-specific properties overriding the default ones.

---

#### **Topic 6.2: Property Overrides and Using `@TestPropertySource`**

While `@ActiveProfiles` is great for a shared configuration across many tests, sometimes a single test class or even a single test method needs a unique property override. This is the job of `@TestPropertySource`.

`@TestPropertySource` is an annotation that lets you add or override properties for a specific test. **Properties defined here have higher precedence than those in profile-specific files.**

**Two ways to use it:**

**1. Inline Properties (`properties` attribute)**

This is perfect for overriding one or two simple values.

```java
@SpringBootTest
@TestPropertySource(properties = {
    "feature-flags.new-algorithm.enabled=true",
    "some.api.timeout=500"
})
class NewAlgorithmTest {
    // In this test, the new algorithm feature is enabled,
    // regardless of what's in application.yml or application-test.yml
}
```

**2. External Properties File (`locations` attribute)**

This is useful for grouping a set of related overrides into a dedicated file. The path is resolved from the classpath.

```java
// src/test/resources/test-configs/disable-caching.properties
// caching.enabled=false

@SpringBootTest
@TestPropertySource(locations = "/test-configs/disable-caching.properties")
class NoCacheTest {
    // In this test, caching will be disabled.
}
```

**Property Precedence (Highest to Lowest):**

1.  `@DynamicPropertySource` (for Testcontainers - highest priority)
2.  `@TestPropertySource` (inline `properties`)
3.  `@TestPropertySource` (`locations`)
4.  Profile-specific properties (`application-test.yml`)
5.  Default properties (`application.yml`)

---

#### **Topic 6.3: Managing Environment-Specific Configurations**

Let's summarize the strategies for handling dynamic configurations like database URLs.

| Strategy | When to Use | Example |
| :--- | :--- | :--- |
| **Static (`application-test.yml`)** | For properties that are known and fixed for the entire test suite. | `logging.level.com.myapp=DEBUG` `server.port=0` |
| **Dynamic (`@DynamicPropertySource`)** | For properties whose values are not known until runtime, typically provided by a Testcontainer. | `registry.add("spring.datasource.url", container::getJdbcUrl);` |
| **Per-Test (`@TestPropertySource`)** | For overriding a global test setting for a very specific test scenario. | `properties="caching.enabled=false"` |

The combination of a baseline `application-test.yml` and dynamic overrides for external services like databases provides the most powerful and maintainable setup.

---

#### **Topic 6.4: Logging Configuration and Conditional Beans**

**Logging in Tests**

Debugging failing integration tests can be difficult. Having verbose logs is essential. You can set specific logging levels in your `application-test.yml` that will only apply when the `test` profile is active.

```yaml
# src/test/resources/application-test.yml
logging:
  level:
    # Set your application's base package to DEBUG
    com.example.myapp: DEBUG
    # See the exact SQL queries Hibernate is running
    org.hibernate.SQL: DEBUG
    # See the values being bound to prepared statements
    org.hibernate.type.descriptor.sql: TRACE
    # See which transactions are being created and rolled back
    org.springframework.transaction.interceptor: TRACE
```

**Conditional Beans in Testing (`@Profile`)**

This is an advanced but clean alternative to using `@MockBean`. Imagine you have a service that interacts with a real, external S3 bucket for file storage. Calling this in tests would be slow, costly, and flaky.

Instead, you can create a "fake" or in-memory implementation of that service's interface that is only active during tests.

**1. The Interface**
```java
public interface FileStorageService {
    void saveFile(String name, byte[] content);
    byte[] loadFile(String name);
}
```

**2. The "Real" Implementation**
```java
@Service
@Profile("!test") // Active when the profile is NOT "test"
public class S3FileStorageService implements FileStorageService {
// ... real S3 client logic ...
}
```

**3. The "Fake" Test Implementation**

```java
@Service
@Profile("test") // ONLY active when the profile is "test"
public class InMemoryFileStorageService implements FileStorageService {
    private final Map<String, byte[]> storage = new ConcurrentHashMap<>();

    @Override
    public void saveFile(String name, byte[] content) {
        storage.put(name, content);
    }
    // ... implement loadFile and maybe a clear() method for tests
}
```

**The Result:**

When you run your application normally, the `S3FileStorageService` is loaded. But when you run any test annotated with `@ActiveProfiles("test")`, Spring's component scan will see the `@Profile` annotations and will create and inject the `InMemoryFileStorageService` instead.

This is incredibly powerful because:
*   You don't need to add `@MockBean` to every test class that uses the `FileStorageService`.
*   Your test implementation can have realistic behavior, unlike a simple mock.
*   It keeps your test logic clean and focused on the integration, not on mocking infrastructure.

---

### **Interview-Level Questions**

1.  **Question:** "What is the order of precedence for properties defined in `@TestPropertySource`, `application-test.yml`, and `application.yml`?"
    *   **Answer:** "Properties from `@TestPropertySource` have the highest precedence and will override properties from all other sources. Next are the profile-specific properties from `application-test.yml`, which override the default properties found in `application.yml`."

2.  **Question:** "When would you choose to use `@Profile` on a bean versus using `@MockBean` in a test?"
    *   **Answer:** "I would use `@Profile` to create a test-specific implementation of a bean when that bean represents a major external infrastructure component, like a file storage service, a message queue client, or a payment gateway. This is ideal when I want a fully functional 'fake' that can be used across the entire test suite. I would use `@MockBean` for more fine-grained control within a single test class, typically to mock a dependency of the component I am testing (like mocking a repository in a web layer test) so I can verify specific interactions and define behavior on a per-test basis."

3.  **Question:** "You have a `@SpringBootTest` that is failing. What is the first thing you would configure in your `application-test.yml` to help diagnose the problem?"
    *   **Answer:** "The first thing I would do is increase the log verbosity. I would set the logging level for my application's root package to `DEBUG` and, if it's a database-related issue, I would set `org.hibernate.SQL` to `DEBUG` and `org.hibernate.type.descriptor.sql` to `TRACE`. This allows me to see the exact SQL queries being generated and the parameters being bound, which is often the key to solving persistence-related integration test failures."

---

### **Practical Exercise**

1.  **Objective:** Use profiles and property sources to control a bean's behavior.
2.  **Setup:**
    *   Create a simple `@Component` called `GreetingService`.
    *   Give it a method `public String getGreeting()` that returns "Hello, World!".
    *   Inject this service into a controller and create an endpoint `/greeting` that returns the string.
3.  **Task:**
    *   In your main `application.yml`, add a property: `greeting.message: "Hello from application.yml"`. Modify `GreetingService` to read this property and return it.
    *   Create an `application-test.yml` and set `greeting.message: "Hello from the test profile"`.
    *   Write a `@SpringBootTest` with `@ActiveProfiles("test")` and assert that the `/greeting` endpoint returns "Hello from the test profile".
    *   In the *same test class*, write a second test method. Annotate **only this method** with `@TestPropertySource(properties = "greeting.message=Hello from a single test!")`. Assert that the endpoint now returns this new message for this test, but the first test still passes with its own message.

This exercise will give you hands-on experience with the configuration precedence rules and demonstrate the power of profile-specific and test-specific properties.

***

Next, we will cover the tools of the trade in our **Tools and Best Practices** section, comparing testing clients and discussing how to organize and run your tests effectively.