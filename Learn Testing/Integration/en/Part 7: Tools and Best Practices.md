### **Part 7: Tools and Best Practices**

Knowing the theory and annotations is half the battle. The other half is using the right tools for the job and following established conventions to keep your test suite fast, readable, and maintainable. This section covers the best practices that distinguish a professional testing strategy.

#### **Topic 7.1: Comparing MockMvc, TestRestTemplate, and RestAssured**

We've used these three tools, but now let's put them side-by-side to make the choice clear.

| Feature | MockMvc | TestRestTemplate | RestAssured |
| :--- | :--- | :--- | :--- |
| **Test Type** | Slice Test (`@WebMvcTest`) | Full E2E Test (`@SpringBootTest`) | Full E2E Test (`@SpringBootTest`) |
| **Server** | Mock Servlet (No real server) | Real Embedded Server (Tomcat) | Real Embedded Server (Tomcat) |
| **Network Call** | No (Internal dispatch) | Yes (HTTP call on `localhost`) | Yes (HTTP call on `localhost`) |
| **Speed** | 🚀 **Fastest** | 🐢 Slower | 🐢 Slower |
| **Primary Goal** | Test controller logic in isolation | Test the full stack end-to-end | Test the full stack end-to-end |
| **Syntax** | Fluent Java API | Standard Java methods | BDD-style (`given/when/then`) |
| **Best For** | Validation, exception handling, request mapping, JSON serialization. | Basic E2E tests, scenarios where you need direct HTTP client control. | Highly readable E2E tests, complex JSON/XML validation. |
| **Typical Context**| `@WebMvcTest` | `@SpringBootTest(webEnvironment=RANDOM_PORT)` | `@SpringBootTest(webEnvironment=RANDOM_PORT)` |

**When to Use Each: A Decision Tree**

1.  **Are you testing the logic *inside* a controller?** (e.g., "Does it call the right service method?", "Does it return 400 on invalid input?")
    *   **Yes** -> Use **`MockMvc`** with `@WebMvcTest`. It's the fastest and most focused tool for this job.

2.  **Are you testing the entire application flow, from an HTTP request all the way to the database?**
    *   **Yes** -> You need a full E2E test with `@SpringBootTest(webEnvironment=RANDOM_PORT)`. Now choose your client:
        *   Do you prefer a simple, standard Spring-integrated client and plain Java assertions? -> Use **`TestRestTemplate`**.
        *   Do you prioritize test readability and want a rich, BDD-style syntax with powerful built-in JSON assertions? -> Use **`RestAssured`**.

**Professional Recommendation:** Use both `MockMvc` and `RestAssured` in your project. Use `MockMvc` for comprehensive testing of all controller edge cases. Use `RestAssured` for a smaller set of critical "happy path" E2E tests that verify your most important user journeys work from start to finish.

---

#### **Topic 7.2: Combining JUnit 5, Mockito, and Spring Boot Effectively**

These three libraries form the "holy trinity" of testing in the Spring ecosystem. It's crucial to understand their distinct roles.

*   **JUnit 5 (The Runner):** This is the testing framework itself. It provides the core annotations (`@Test`, `@BeforeEach`, `@Disabled`), the assertion library (`org.junit.jupiter.api.Assertions`), and the extension model that allows other frameworks to plug in. When you run your tests, you are running them with JUnit.

*   **Mockito (The Mocking Library):** This is the library for creating "test doubles" or mocks. Its job is to create fake implementations of your dependencies so you can test a single unit in isolation. The key annotations are `@Mock`, `@InjectMocks`, and the core methods are `when()`/`given()`, `verify()`. Spring Boot provides the `@MockBean` annotation to seamlessly integrate Mockito mocks into the Spring `ApplicationContext`.

*   **Spring Boot Test (The Integration Layer):** This is the glue that brings everything together for integration testing. It provides the annotations (`@SpringBootTest`, `@WebMvcTest`, etc.) that load the `ApplicationContext`. It uses a JUnit 5 extension (`SpringExtension`) to manage the context lifecycle. It provides test utilities like `MockMvc` and `TestRestTemplate`.

**How they work together in a `@WebMvcTest`:**
1.  **JUnit 5** sees the `@Test` annotation and decides to run your method.
2.  It sees the `@ExtendWith(SpringExtension.class)` (which is meta-annotated on `@WebMvcTest`) and hands control to Spring.
3.  **Spring Boot Test** loads the minimal web-slice `ApplicationContext`.
4.  It sees the `@MockBean` annotation and uses **Mockito** to create a mock of your `ProductService`.
5.  It injects this mock into the `ProductController` bean inside the context.
6.  The test method runs. You use **Mockito's** `given()` to set up the mock's behavior.
7.  You use **Spring's** `MockMvc` to perform a request.
8.  You use `MockMvcResultMatchers` (which often use Hamcrest or AssertJ internally) or **JUnit 5's** `Assertions` to check the result.

---

#### **Topic 7.3: Organizing Tests in Project Structure**

A clean separation between unit and integration tests is vital for a scalable project. The standard Maven/Gradle directory layout is: `src/test/java`.

The best practice is to mirror your main package structure and add sub-packages for test types.

```
src
├── main
│   └── java
│       └── com
│           └── myapp
│               ├── controller
│               ├── service
│               └── repository
└── test
    └── java
        └── com
            └── myapp
                ├── unit
                │   ├── controller
                │   │   └── ProductControllerUnitTest.java  // Plain JUnit/Mockito
                │   └── service
                │       └── ProductServiceUnitTest.java      // Plain JUnit/Mockito
                └── integration
                    ├── controller
                    │   └── ProductControllerWebMvcTest.java // @WebMvcTest
                    └── repository
                        └── ProductRepositoryDataJpaTest.java // @DataJpaTest
                        └── ProductControllerE2ETest.java     // @SpringBootTest
```

**Benefits of this structure:**
*   **Clarity:** It's immediately obvious what type of test you are looking at.
*   **CI/CD Configuration:** It allows you to configure your build pipeline to run tests in stages. For example:
    1.  **On every commit to a feature branch:** Run only the fast unit tests (`/unit/**`).
    2.  **On a pull request to the main branch:** Run all unit tests *and* all integration tests (`/integration/**`).

---

#### **Topic 7.4: Reusing Testcontainers Across Tests (Singleton Containers)**

This is a critical performance best practice that is worth repeating. Starting a Docker container is the most expensive part of a Testcontainers-based test. **You must avoid starting a new container for every test class.**

The solution is the **Singleton Pattern**, achieved by declaring the container as a `static` field.

```java
// In a shared base class like AbstractIntegrationTest
@Container
private static final PostgreSQLContainer<?> postgresqlContainer =
        new PostgreSQLContainer<>("postgres:14.5");
```

When JUnit runs the first test class that uses this base class, Testcontainers starts the container. When subsequent test classes are run, Testcontainers sees the container is `static` and already running, so it simply reuses the connection. The container is only stopped once the entire JVM process (the test suite run) is complete.

---

#### **Topic 7.5: Integrating Tests with CI/CD (GitHub Actions, Jenkins)**

Your tests provide the most value when they are run automatically in a Continuous Integration / Continuous Delivery pipeline. This acts as a quality gate, preventing bugs from reaching production.

Here is a sample workflow for **GitHub Actions** that runs both unit and integration tests.

```yaml
# .github/workflows/ci.yml
name: Spring Boot CI

on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

jobs:
  build:
    runs-on: ubuntu-latest

    services:
      # This starts a Docker container for PostgreSQL that can be accessed
      # by the testcontainers library from within the main job runner.
      # This is often faster than testcontainers downloading the image itself.
      postgres:
        image: postgres:14.5
        env:
          POSTGRES_USER: test
          POSTGRES_PASSWORD: test
          POSTGRES_DB: test
        ports:
          - 5432:5432

    steps:
    - name: Checkout code
      uses: actions/checkout@v4

    - name: Set up JDK 17
      uses: actions/setup-java@v4
      with:
        java-version: '17'
        distribution: 'temurin'
        cache: 'maven'

    - name: Run Unit Tests
      # This command tells Maven to run only tests in the 'unit' package
      run: mvn -B test -Dtest="com/myapp/unit/**"

    - name: Run Integration Tests
      # This command runs the remaining tests (or you can be more specific)
      # The environment variables are needed if your tests expect them.
      run: mvn -B verify -Dtest="com/myapp/integration/**"
      env:
          SPRING_DATASOURCE_URL: jdbc:postgresql://localhost:5432/test
          SPRING_DATASOURCE_USERNAME: test
          SPRING_DATASOURCE_PASSWORD: test
```

---

#### **Topic 7.6: Test Naming Conventions and Parameterized Testing**

**Naming Conventions**
Readable test names are crucial. They act as living documentation for your code. A popular convention is `methodName_should_expectedBehavior_when_stateUnderTest`.

*   `findById_should_returnProduct_when_productExists`
*   `createProduct_should_throwValidationException_when_nameIsBlank`

**Parameterized Testing with JUnit 5**

When you need to test the same logic with multiple different inputs, avoid copy-pasting tests. Use `@ParameterizedTest`.

```java
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.CsvSource;

@ParameterizedTest
@CsvSource({
    "100.0, 10, 90.0",   // price, discount, expected
    "50.0,  50, 25.0",
    "20.0,  100, 0.0"
})
void calculateDiscount_should_returnCorrectPrice(double price, double discount, double expected) {
    // Act
    double result = priceCalculator.calculate(price, discount);
    // Assert
    assertThat(result).isEqualTo(expected);
}
```

---

#### **Topic 7.7: Measuring Code Coverage with Jacoco**

Code coverage tells you what percentage of your code is executed by your tests. It's a useful metric for identifying untested parts of your application, but it is **not a measure of test quality**. 100% coverage doesn't mean your code is bug-free.

**Setup with Maven:**
Add the Jacoco plugin to your `pom.xml`. The key is to configure it to create a report that includes both unit and integration tests.

```xml
<plugin>
    <groupId>org.jacoco</groupId>
    <artifactId>jacoco-maven-plugin</artifactId>
    <version>0.8.12</version>
    <executions>
        <execution>
            <id>prepare-agent</id>
            <goals>
                <goal>prepare-agent</goal>
            </goals>
        </execution>
        <execution>
            <id>report</id>
            <phase>verify</phase>
            <goals>
                <goal>report</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```

When you run `mvn clean verify`, Jacoco will:
1.  Attach a Java agent to the JVM during the test execution phase.
2.  This agent tracks which lines of your code are executed by both your unit and integration tests.
3.  During the `verify` phase, it generates an HTML report in `target/site/jacoco/index.html` that you can open in a browser to explore your code coverage.

***

Next, we will cover a set of **Edge Cases and Advanced Scenarios**, such as testing asynchronous code, caching, and security.