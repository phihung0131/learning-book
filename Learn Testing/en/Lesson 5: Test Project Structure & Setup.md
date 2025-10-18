Excellent. Now that you've mastered the tools for writing tests, let's zoom out and look at how to organize them within a professional Java project. A clean, conventional structure is key for maintainability and collaboration.

### **Phase 1, Lesson 5: Test Project Structure & Setup**

We will focus on the structure used by **Maven**, which is the most common build tool in the Java ecosystem. Gradle and other tools use a very similar layout.

### **1️⃣ Maven Project Structure for Testing**

Maven enforces a "convention over configuration" approach. This means it has a standard directory layout that works out of the box without needing much setup.

A typical Maven project looks like this:

```
my-awesome-project/
├── pom.xml                   <-- The heart of the Maven project: configuration, dependencies.
└── src/
    ├── main/
    │   ├── java/             <-- YOUR PRODUCTION CODE GOES HERE.
    │   │   └── com/
    │   │       └── mycompany/
    │   │           └── app/
    │   │               └── service/
    │   │                   └── UserService.java
    │   └── resources/        <-- Production resources (config files, images, etc.).
    │
    └── test/
        ├── java/             <-- YOUR TEST CODE GOES HERE.
        │   └── com/
        │       └── mycompany/
        │           └── app/
        │               └── service/
        │                   └── UserServiceTest.java
        └── resources/        <-- Test-only resources (test data, mock JSON files, etc.).
```

### **2️⃣ How to Separate Test Sources: `src/main/java` vs. `src/test/java`**

This separation is the most fundamental concept of project organization.

*   **`src/main/java`**: Contains all your application's production code. This is the code that gets compiled, packaged (into a JAR or WAR file), and shipped to users.
*   **`src/test/java`**: Contains all your test code.

**Why is this separation so important?**

1.  **Keeps Test Code Out of Production:** Code in `src/test/java` is only used during the test phase of your build. It is **never** included in the final application artifact. This means your production JAR file isn't bloated with thousands of test classes and testing libraries.
2.  **Different Dependencies:** You often need libraries for testing (like JUnit, Mockito, AssertJ) that you don't need in your production application. This separation allows you to manage these "test-scoped" dependencies cleanly.
3.  **Clarity:** It creates a clear boundary. Anyone looking at the project knows exactly where to find the application code and where to find the tests for it.
4.  **Mirrored Package Structure:** The package structure inside `src/test/java` should **mirror** the structure in `src/main/java`. If your production class is in `com.mycompany.app.service`, its test should be in the exact same package inside the test source folder. This makes it incredibly easy to find the test for any given class.

### **3️⃣ Naming Conventions for Test Classes**

As we've discussed, the standard convention is to name a test class after the class it is testing, with the suffix `Test`.

*   Class to be tested: `UserService.java`
*   Test class: `UserServiceTest.java`

This convention is so common that IDEs and build tools rely on it to automatically identify which classes are tests.

### **4️⃣ `pom.xml` Dependencies for Testing**

Your `pom.xml` file declares all the libraries your project needs. For testing, you need to add JUnit 5, Mockito, and AssertJ. The most important part is the `<scope>test</scope>` tag.

This tag tells Maven that this dependency is **only** needed for compiling and running the code in `src/test/java`. It will not be included in the final production artifact.

Here is a typical `dependencies` block in a `pom.xml` for a modern testing setup:

```xml
<dependencies>
    <!-- Add your production dependencies here (e.g., Spring Boot) -->

    <!-- =============================================== -->
    <!--  TESTING DEPENDENCIES                           -->
    <!-- =============================================== -->

    <!-- JUnit 5 (Jupiter) -->
    <dependency>
        <groupId>org.junit.jupiter</groupId>
        <artifactId>junit-jupiter-engine</artifactId>
        <version>5.10.1</version> <!-- Use the latest version -->
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>org.junit.jupiter</groupId>
        <artifactId>junit-jupiter-params</artifactId>
        <version>5.10.1</version>
        <scope>test</scope>
    </dependency>

    <!-- Mockito -->
    <dependency>
        <groupId>org.mockito</groupId>
        <artifactId>mockito-core</artifactId>
        <version>5.7.0</version> <!-- Use the latest version -->
        <scope>test</scope>
    </dependency>
    <dependency>
        <!-- Needed for mocking final classes/static methods -->
        <groupId>org.mockito</groupId>
        <artifactId>mockito-inline</artifactId>
        <version>5.2.0</version>
        <scope>test</scope>
    </dependency>

    <!-- AssertJ -->
    <dependency>
        <groupId>org.assertj</groupId>
        <artifactId>assertj-core</artifactId>
        <version>3.24.2</version> <!-- Use the latest version -->
        <scope>test</scope>
    </dependency>
</dependencies>
```
*(Note: Spring Boot users often get these dependencies managed automatically via the `spring-boot-starter-test` dependency, which simplifies this setup significantly.)*

### **5️⃣ IDE Test Configurations (IntelliJ)**

Modern IDEs like IntelliJ IDEA have deep integration with Maven and JUnit.

*   **Automatic Recognition:** Because you follow the standard Maven directory structure, IntelliJ automatically recognizes `src/test/java` as a test source root.
*   **"Gutter" Icons:** You will see small "play" icons in the margin (the gutter) next to your test classes and methods. You can click these to run or debug an individual test or an entire test class.
*   **Test Runner UI:** When you run tests, IntelliJ opens a dedicated Test Runner window that shows a tree of your tests, progress bars, and a clear red/green indication of success or failure.

### **6️⃣ Using Tags/Groups for Selective Runs (`@Tag`, `@Disabled`)**

Sometimes you want to run only a subset of your tests. For example, you might have some very slow tests that you only want to run before a release, not on every code change.

#### **`@Disabled`**
This annotation is simple: it tells JUnit to skip a test method or an entire test class. You can provide an optional reason.

```java
@Test
@Disabled("This feature is currently under rework.")
void someWipTest() {
    // This test will not be executed.
}
```
**Best Practice:** Don't let `@Disabled` tests accumulate in your codebase. They are a form of technical debt. A test should either be working or be deleted.

#### **`@Tag`**
`@Tag` is a more powerful way to categorize your tests. You can apply a tag (a simple string) to test methods or classes.

```java
@Test
@Tag("fast")
void fastUnitTest() { /* ... */ }

@Test
@Tag("slow")
@Tag("database")
void slow_database_integration_test() { /* ... */ }
```

You can then configure your build tool (Maven/Gradle) or IDE to **include** or **exclude** tests based on these tags. For example, you could configure one build step to run all tests `!slow` (not slow) and a nightly build step that runs *all* tests, including the slow ones.

### **🏆 Lesson 5 Recap: Project Setup**

*   **Standard Structure:** You know the Maven layout with `src/main/java` for production code and `src/test/java` for test code.
*   **Separation of Concerns:** You understand why this separation is crucial for keeping your production artifact clean.
*   **Naming and Packages:** You know to mirror the package structure and use the `Test` suffix for test classes.
*   **`pom.xml`:** You can add the necessary dependencies for JUnit 5, Mockito, and AssertJ with the correct `test` scope.
*   **Selective Runs:** You can use `@Disabled` to temporarily skip tests and `@Tag` to categorize tests for more advanced, selective test runs.

You now have a complete picture of how to write, organize, and manage unit tests in a professional Java project.

There is just one final piece of the puzzle: **How do we measure the effectiveness of our tests?** This brings us to our final lesson.

Ready to begin **Lesson 6: Code Coverage with Jacoco**?