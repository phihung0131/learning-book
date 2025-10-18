Excellent! Let's dive into the most essential tool in a Java developer's testing toolkit: **JUnit 5**.

### **Phase 1, Lesson 2: JUnit 5 (Core Knowledge)**

JUnit is the *de facto* standard testing framework for Java. JUnit 5 is the latest major version, a complete rewrite from previous versions, making it much more powerful and flexible. It provides the "test runner" and the foundational annotations and assertions we need to write and execute our tests.

Think of JUnit as the engine that discovers, runs, and reports the results of your tests.

### **1️⃣ Basic Annotations: The Building Blocks of a Test Class**

Annotations are special labels (metadata) that you add to your Java code, prefixed with an `@` symbol. JUnit 5 uses annotations to identify which methods are tests and how they should be run.

Let's create our first test class. By convention, if you have a class named `Calculator`, its test class will be named `CalculatorTest`.

```java
import org.junit.jupiter.api.*;

class CalculatorTest {

    // 1. @Test
    @Test
    void myFirstTest() {
        // This is a test method!
    }

    // 2. @BeforeEach
    @BeforeEach
    void setUp() {
        System.out.println("Executing before each test...");
    }

    // 3. @AfterEach
    @AfterEach
    void tearDown() {
        System.out.println("Executing after each test...");
    }

    // 4. @BeforeAll
    @BeforeAll
    static void setupAll() {
        System.out.println("Executing before all tests...");
    }

    // 5. @AfterAll
    @AfterAll
    static void tearDownAll() {
        System.out.println("Executing after all tests...");
    }

    @Test
    void anotherTest() {
        // This is another test method.
    }
}
```

Let's break down what each of these annotations does.

#### **`@Test`**
This is the most important annotation. It signals to JUnit that the method it is attached to is a test method.
*   **Purpose:** To define a test.
*   **Characteristics:**
    *   Methods annotated with `@Test` **must not** be `private` or `static`.
    *   They **must not** return a value (i.e., their return type should be `void`).

#### **`@BeforeEach` and `@AfterEach` (Setup and Teardown per Test)**
These annotations are used for setup and cleanup logic that needs to run **before and after every single `@Test` method** in the class.

*   **`@BeforeEach`:** The annotated method runs *before* each test. It's perfect for resetting the state of objects to ensure each test runs in isolation. For example, creating a new instance of the class you are testing.
*   **`@AfterEach`:** The annotated method runs *after* each test. It's used for cleanup, like closing files, resetting database states, or releasing resources to prevent tests from interfering with each other.

#### **`@BeforeAll` and `@AfterAll` (Class-Level Setup and Teardown)**
These annotations are used for expensive setup and cleanup logic that should only run **once for the entire test class**.

*   **`@BeforeAll`:** The annotated method runs once *before any* of the tests in the class have run.
*   **`@AfterAll`:** The annotated method runs once *after all* of the tests in the class have completed.
*   **Important Rule:** Methods annotated with `@BeforeAll` or `@AfterAll` **must be `static`**. This is because they are executed before the test class instance is created.

#### **Execution Order**

If you were to run the `CalculatorTest` class above, the output to the console would be:

```
Executing before all tests...
Executing before each test...
Executing after each test...
Executing before each test...
Executing after each test...
Executing after all tests...
```
*(Note: The order of `myFirstTest` and `anotherTest` is not guaranteed by default, but `@BeforeEach`/`@AfterEach` will wrap each one).*

This demonstrates the lifecycle perfectly:
1.  `@BeforeAll` runs once.
2.  `@BeforeEach` -> `@Test` -> `@AfterEach` runs for the first test.
3.  `@BeforeEach` -> `@Test` -> `@AfterEach` runs for the second test.
4.  `@AfterAll` runs once.

Now that we know how to structure a test, the next crucial step is to give it a meaningful name.

Ready to talk about **Test Naming Conventions**?

Of course. Let's move on to a practice that dramatically improves the readability and value of your tests.

### **2️⃣ Test Naming Conventions (shouldReturnX_whenY)**

How you name your tests is one of the most important factors in creating a maintainable and valuable test suite. A test name should clearly and concisely describe exactly what is being tested and under what conditions. When a test fails, its name should be all you need to understand the bug.

Avoid generic, meaningless names like `test1`, `testAdd`, or `testError`. These tell you nothing about the specific scenario that failed.

A highly effective and widely adopted naming convention is the **`should_when_`** or **`methodName_should_when_`** pattern.

**The Structure:** `should`**[Expected Outcome]**`When`**[State or Condition]**

Let's apply this to our `Calculator` example. Assume we have a method `add(int a, int b)`.

*   **Poor Name:** `@Test void testAdd()`
    *   *If this fails, what do we know? Only that the `add` method is broken, but not how or why.*
*   **Good Name:** `@Test void shouldReturnFive_whenTwoAndThreeAreAdded()`
    *   *If this fails, we know the exact scenario: adding 2 and 3 did not result in 5. This is instantly informative.*

Let's consider another example from our `RegistrationService`'s `isUsernameValid(String username)` method.

*   **Happy Path:**
    *   `@Test void isUsernameValid_shouldReturnTrue_whenUsernameIsWithinBounds()`
*   **Boundary Case:**
    *   `@Test void isUsernameValid_shouldReturnFalse_whenUsernameIsOneCharShorterThanMinimum()`
*   **Negative Case:**
    *   `@Test void isUsernameValid_shouldThrowIllegalArgumentException_whenUsernameIsNull()`

**Benefits of This Convention:**

1.  **Readability:** The test reads like a sentence describing a piece of functionality. It becomes living documentation.
2.  **Clarity:** When a test fails in your build report, the name alone tells you which requirement is no longer being met.
3.  **Focus:** It forces you to think about one specific behavior per test, preventing you from writing massive tests that try to do too much at once.

You can use either camelCase (`shouldReturnFiveWhen...`) or snake_case with backticks in Java (`should_return_five_when...` - less common but possible). The key is to be consistent with your team.

Now that we can structure and name a test, we need to make it actually *do* something. That's where assertions come in.

### **3️⃣ Assertions (All Types)**

An assertion is a statement that verifies the outcome of your test. It's a check that declares whether the actual result of an action matches the expected result. If an assertion fails, the test method stops immediately and is marked as failed.

JUnit 5 provides a rich set of assertion methods in the `org.junit.jupiter.api.Assertions` class. It's common practice to static import these methods for better readability.

```java
// At the top of your test class
import static org.junit.jupiter.api.Assertions.*;
```

Let's explore the most important ones.

#### **`assertEquals(expected, actual)`**
The workhorse of assertions. It verifies that two values are equal.
```java
@Test
void shouldReturnFive_whenTwoAndThreeAreAdded() {
    Calculator calculator = new Calculator();
    int result = calculator.add(2, 3);
    assertEquals(5, result, "The sum of 2 and 3 should be 5"); // Optional failure message
}
```

#### **`assertTrue(condition)` and `assertFalse(condition)`**
Verifies that a given boolean condition is true or false.
```java
@Test
void isUsernameValid_shouldReturnTrue_whenUsernameIsNormal() {
    RegistrationService service = new RegistrationService();
    boolean isValid = service.isUsernameValid("ValidUser");
    assertTrue(isValid);
}
```

#### **`assertNotNull(object)` and `assertNull(object)`**
Verifies that an object is not null or is null.
```java
@Test
void shouldReturnAUser_whenIdExists() {
    UserService service = new UserService();
    User user = service.findById(1);
    assertNotNull(user);
}
```

#### **`assertThrows(expectedException, executable)`**
This is the correct way to test for exceptions. It verifies that a specific piece of code (an "executable" lambda) throws a specific type of exception.
```java
@Test
void isUsernameValid_shouldThrowIllegalArgumentException_whenUsernameIsNull() {
    RegistrationService service = new RegistrationService();
    // The lambda () -> service.isUsernameValid(null) is the executable code
    assertThrows(IllegalArgumentException.class, () -> {
        service.isUsernameValid(null);
    });
}
```

#### **`assertAll(executables...)`**
A very powerful assertion. It groups multiple assertions together. Unlike regular assertions, `assertAll` will run *every* assertion in the group, and if any fail, it will report *all* of the failures at once. This is great for checking multiple properties of a single object.
```java
@Test
void shouldCreateUserWithCorrectProperties() {
    // ... code to create a user
    User user = new User("alex", "alex@example.com", 25);

    assertAll("User properties",
        () -> assertEquals("alex", user.getUsername()),
        () -> assertEquals("alex@example.com", user.getEmail()),
        () -> assertEquals(25, user.getAge())
    );
}
```

#### **`assertTimeout(duration, executable)`**
Used to test performance and ensure a method doesn't take too long to execute. The test will fail if the executable code takes longer than the specified `Duration`.
```java
import java.time.Duration;

@Test
void shouldPerformActionWithin100Milliseconds() {
    assertTimeout(Duration.ofMillis(100), () -> {
        // Some potentially long-running operation
        service.performHeavyCalculation();
    });
}
```

We've covered the basics, but what about testing with multiple different inputs? Doing that manually would lead to a lot of duplicate test code. JUnit 5 has an elegant solution for this.

Ready to learn about **Parameterized Tests**?

Of course. Let's tackle a powerful feature that helps you avoid one of the anti-patterns we discussed earlier: logic and duplication in tests.

### **4️⃣ Parameterized Tests: `@ParameterizedTest`, `@ValueSource`, `@CsvSource`**

Often, you want to test the same method with a wide range of different inputs to check various happy paths, boundary conditions, and negative scenarios.

**The "Bad" Way (Without Parameterized Tests):**

You could write a separate test method for each input. This leads to a lot of boilerplate and duplicated code.

```java
// ANTI-PATTERN: Repetitive code
class StringUtilsTest {
    @Test
    void isBlank_shouldReturnTrueForNullString() {
        assertTrue(StringUtils.isBlank(null));
    }

    @Test
    void isBlank_shouldReturnTrueForEmptyString() {
        assertTrue(StringUtils.isBlank(""));
    }



    @Test
    void isBlank_shouldReturnTrueForWhitespaceString() {
        assertTrue(StringUtils.isBlank("   "));
    }
}
```
This is hard to maintain. If the logic changes, you have to update multiple tests.

**The "Good" Way (With Parameterized Tests):**

A parameterized test allows you to run the same test method multiple times with different arguments.

1.  **Replace `@Test` with `@ParameterizedTest`.** This annotation tells JUnit that the method will receive arguments.
2.  **Provide a source of arguments.** You use another annotation to specify where the values come from.

Let's look at the most common argument sources.

#### **`@ValueSource`**

This is the simplest source. It lets you provide an array of literal values (shorts, bytes, ints, longs, floats, doubles, chars, booleans, strings, or classes).

Let's refactor the previous example using `@ValueSource`.

```java
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.ValueSource;
import static org.junit.jupiter.api.Assertions.assertTrue;

class StringUtilsTest {

    @ParameterizedTest
    @ValueSource(strings = {"", "  ", "\t", "\n"}) // Provides one argument per invocation
    void isBlank_shouldReturnTrueForEmptyOrWhitespaceStrings(String input) {
        assertTrue(StringUtils.isBlank(input));
    }
}
```

**How it works:**
*   JUnit will run the `isBlank_shouldReturnTrueFor...` method four times.
*   In the first run, `input` will be `""`.
*   In the second run, `input` will be `"  "`.
*   And so on.
*   Your IDE's test runner will show these as individual test runs, making it easy to see which specific input failed.

`@ValueSource` is great, but it can only provide one argument at a time. What if you need to provide multiple arguments, like an input and an *expected output*?

#### **`@CsvSource`**

`@CsvSource` allows you to specify test inputs as comma-separated values (CSV). Each string in the array represents one invocation of the test, with the values mapped to the method parameters.

Let's test our `Calculator.add` method.

```java
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.CsvSource;
import static org.junit.jupiter.api.Assertions.assertEquals;

class CalculatorTest {

    @ParameterizedTest(name = "Run {index}: {0} + {1} = {2}") // Customizing the display name
    @CsvSource({
        "0,    1,   1",
        "1,    2,   3",
        "-5,   5,   0",
        "1000, 2000, 3000"
    })
    void add_shouldReturnCorrectSum_forMultipleInputs(int first, int second, int expectedResult) {
        Calculator calculator = new Calculator();
        int actualResult = calculator.add(first, second);
        assertEquals(expectedResult, actualResult);
    }
}
```

**How it works:**
*   The test method now has three parameters: `first`, `second`, and `expectedResult`.
*   In the first run, `first` is `0`, `second` is `1`, and `expectedResult` is `1`.
*   In the second run, `first` is `1`, `second` is `2`, and `expectedResult` is `3`.
*   The `name` attribute on `@ParameterizedTest` is optional but highly recommended. It creates a descriptive name for each run in the test report, making debugging much easier.

Parameterized tests are a cornerstone of writing concise, powerful, and maintainable unit tests.

Now, let's look at how to organize complex test scenarios for a single class.

Ready to dive into **Nested Tests with `@Nested`**?

Excellent. As your test suites grow, keeping them organized becomes critical for readability and maintainability.

### **5️⃣ Nested Tests with `@Nested`**

Sometimes a single test class can become very long, especially when testing a complex class with multiple methods or states. It can be hard to tell which tests belong to which feature.

The `@Nested` annotation in JUnit 5 provides a powerful solution. It allows you to group related tests into an inner class, creating a clear, hierarchical structure in your test suite. This is a fantastic way to organize your tests by the behavior or method you are testing.

**When to Use `@Nested`:**

*   When testing a class with multiple public methods (`createUser`, `deleteUser`, `updateUser`).
*   When testing a single method under different conditions or states (e.g., testing a `Stack` class `when new`, `when empty`, `when full`).

Let's look at an example. Imagine we are testing a `UserService`.

**The "Flat" Way (Without `@Nested`):**

```java
// Harder to read and navigate
class UserServiceTest {

    @Test
    void createUser_shouldSucceed_whenUsernameIsUnique() { /* ... */ }

    @Test
    void createUser_shouldThrowException_whenUsernameAlreadyExists() { /* ... */ }

    @Test
    void deleteUser_shouldSucceed_whenUserExists() { /* ... */ }

    @Test
    void deleteUser_shouldThrowException_whenUserDoesNotExist() { /* ... */ }
}
```
In a large class, this flat list becomes confusing. It's not immediately obvious which tests relate to `createUser` and which to `deleteUser`.

**The "Organized" Way (With `@Nested`):**

We can group these tests into nested classes, each focused on a specific feature.

```java
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Nested;
import org.junit.jupiter.api.Test;

@DisplayName("A User Service")
class UserServiceTest {

    // Common setup for all nested tests can go here

    @Nested
    @DisplayName("when creating a user")
    class CreateUserTests {

        @Test
        @DisplayName("should succeed for a unique username")
        void shouldSucceed_whenUsernameIsUnique() {
            // Test logic for successful creation
        }

        @Test
        @DisplayName("should throw an exception if the username already exists")
        void shouldThrowException_whenUsernameAlreadyExists() {
            // Test logic for duplicate username
        }
    }

    @Nested
    @DisplayName("when deleting a user")
    class DeleteUserTests {

        @Test
        @DisplayName("should succeed if the user exists")
        void shouldSucceed_whenUserExists() {
            // Test logic for successful deletion
        }

        @Test
        @DisplayName("should throw an exception if the user does not exist")
        void shouldThrowException_whenUserDoesNotExist() {
            // Test logic for deleting a non-existent user
        }
    }
}
```

**Key Benefits and Rules:**

*   **Structure and Readability:** The test's intent is immediately clear from the class structure. Your IDE will typically display these tests in a tree format, which is very easy to navigate.
*   **Shared Setup:** The outer class can contain `@BeforeEach` and `@AfterEach` methods that apply to *all* nested tests, providing a shared context. Each `@Nested` class can also have its own `@BeforeEach` and `@AfterEach` methods for more specific setup.
*   **`@DisplayName`:** This annotation is often used with `@Nested` to create human-readable names for the test groups in reports, making them even more descriptive.
*   **Rule:** Nested test classes must be **non-static inner classes**. This allows them to share the instance fields of the outer test class.

Using `@Nested` is a sign of a professional, well-organized test suite.

We've covered the main structures for writing tests. Now let's focus on the trickier scenarios that often get missed.

Ready to tackle the **Edge Cases** like floating-point precision, exceptions, and nulls?

Excellent. This is where we separate the basic tests from the professional, robust ones. Anyone can test the "happy path," but great developers rigorously test the edges.

### **6️⃣ Edge Cases: Testing Floating-Point Precision, Exceptions, Timeouts, Nulls, and Immutability**

Edge cases are the tricky scenarios at the boundaries of your input or logic that are prone to causing bugs. Let's master how to handle them with JUnit 5.

#### **1. Testing Floating-Point Precision (Floats and Doubles)**

This is a classic trap. Due to the way computers store floating-point numbers, calculations are often not exact.

*   **The Problem:** `0.1 + 0.2` is not equal to `0.3` in binary floating-point arithmetic. It's something like `0.30000000000000004`.
*   **The Wrong Way:** The following test will **fail**.
    ```java
    // THIS WILL FAIL!
    @Test
    void testFloatingPointAddition_fails() {
        double result = 0.1 + 0.2;
        assertEquals(0.3, result); // Fails because result is not exactly 0.3
    }
    ```
*   **The Solution:** Use the overloaded version of `assertEquals` that accepts a `delta` (or tolerance). This asserts that the actual value is within a certain acceptable range of the expected value.
    ```java
    @Test
    void testFloatingPointAddition_succeeds() {
        double result = 0.1 + 0.2;
        double expected = 0.3;
        double delta = 0.0001; // The acceptable difference
        assertEquals(expected, result, delta);
    }
    ```
    **Rule of Thumb:** Never use `assertEquals(expected, actual)` for `double` or `float` types without a delta.

#### **2. Advanced Exception Testing**

We've already seen `assertThrows`, but what if you need to verify something *about* the exception, like its message or a custom error code?

*   **The Goal:** Ensure not only that the right type of exception is thrown, but that it contains the correct information.
*   **The Solution:** `assertThrows` conveniently returns the exception that was thrown, allowing you to perform further assertions on it.
    ```java
    @Test
    void isUsernameValid_shouldThrowException_withCorrectMessage() {
        RegistrationService service = new RegistrationService();

        // 1. Call assertThrows and store the returned exception
        IllegalArgumentException thrownException = assertThrows(
            IllegalArgumentException.class,
            () -> service.isUsernameValid(null) // 2. The code that should throw
        );

        // 3. Assert properties of the captured exception
        assertEquals("Username cannot be null", thrownException.getMessage());
    }
    ```

#### **3. Testing Timeouts**

Sometimes you need to guarantee that a method responds within a certain timeframe.

*   **`assertTimeout(duration, executable)`:** This runs the code and lets it finish. *After* it finishes, it checks if the execution time exceeded the timeout. If it did, the test fails. This is useful for testing long-running tasks that you expect to complete.
*   **`assertTimeoutPreemptively(duration, executable)`:** This is stricter. It runs the code in a different thread. If the timeout is exceeded, it stops the execution and fails the test immediately. This is useful for testing scenarios that might result in an infinite loop.

    ```java
    import java.time.Duration;

    class TimeoutTest {
        @Test
        void shouldFinishWithinOneSecond() {
            // This test will pass
            assertTimeout(Duration.ofSeconds(1), () -> {
                // Task that takes 500ms
                Thread.sleep(500);
            });
        }

        @Test
        void shouldFailPreemptively_ifTaskRunsTooLong() {
            // This test will fail after approx. 100ms
            assertTimeoutPreemptively(Duration.ofMillis(100), () -> {
                // An infinite loop or a very long task
                Thread.sleep(1000);
            });
        }
    }
    ```

#### **4. Testing Nulls**

As discussed in our "Negative Scenarios" lesson, `null` is a frequent source of `NullPointerException`s. You should always consider it as a potential input.

*   **The Goal:** Explicitly test how your method behaves when it receives `null`. Does it throw an exception (best practice for invalid arguments)? Does it return an empty result?
*   **The Solution:** Combine `assertThrows` or other assertions to verify the expected behavior.
    ```java
    @Test
    void shouldThrowNullPointerException_whenListIsNull() {
        ListProcessor processor = new ListProcessor();
        assertThrows(NullPointerException.class, () -> {
            processor.process(null); // Passing a null list
        });
    }
    ```

#### **5. Testing Immutable Objects**

An immutable object is one whose state cannot be changed after it is created (e.g., `String`, `Integer`). When you design your own immutable classes, you need to test that they are, in fact, immutable.

*   **The Goal:** Verify that methods that appear to modify the object actually return a *new instance* with the changes, leaving the original object untouched.
*   **The Solution:** Create an instance, call a "mutator" method, and then assert two things:
    1.  The new object has the correct state.
    2.  The original object's state has not changed.

    ```java
    // Assume Money is an immutable class
    @Test
    void money_add_shouldReturnNewInstance_andNotModifyOriginal() {
        Money initialAmount = new Money(10, "USD");
        Money amountToAdd = new Money(5, "USD");

        // Call the 'mutator' method
        Money finalAmount = initialAmount.add(amountToAdd);

        // Assert 1: The new object is correct
        assertEquals(15, finalAmount.getAmount());

        // Assert 2: The original object is unchanged
        assertEquals(10, initialAmount.getAmount());

        // Assert 3: The returned object is a different instance
        assertNotSame(initialAmount, finalAmount);
    }
    ```

We're making great progress. The last core piece of JUnit 5 to cover is the test lifecycle and execution order.

Ready to learn about **Test Lifecycle and Ordering**?

Got it. Let's cover the final piece of our core JUnit 5 knowledge.

### **7️⃣ Test Lifecycle, Ordering (`@TestMethodOrder`)**

By default, JUnit 5 does not guarantee the execution order of your `@Test` methods. It uses an internal algorithm that is deterministic but not obvious. This is a deliberate design choice to enforce a critical best practice: **your tests should be independent and self-contained.** One test should never depend on another test having run before it.

However, there are some rare scenarios (like certain types of integration tests) where a specific order is required. For these cases, JUnit 5 provides the `@TestMethodOrder` annotation.

You apply this annotation at the class level and provide it with a specific `MethodOrderer`.

Let's explore the most common orderers.

#### **1. Ordering by `@Order` Annotation**

This is the most explicit and flexible way to define an order. You annotate your test class with `@TestMethodOrder(MethodOrderer.OrderAnnotation.class)` and then add the `@Order` annotation to each test method with an integer value.

```java
import org.junit.jupiter.api.MethodOrderer;
import org.junit.jupiter.api.Order;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.TestMethodOrder;

@TestMethodOrder(MethodOrderer.OrderAnnotation.class)
class OrderedTest {

    @Test
    @Order(1)
    void firstTest() {
        System.out.println("Executing the first test.");
    }

    @Test
    @Order(3)
    void thirdTest() {
        System.out.println("Executing the third test.");
    }

    @Test
    @Order(2)
    void secondTest() {
        System.out.println("Executing the second test.");
    }
}
```
When you run this class, the output will be:
```
Executing the first test.
Executing the second test.
Executing the third test.
```
This approach is clear and easy to maintain.

#### **2. Ordering Alphanumerically**

This orderer sorts test methods by their names in alphanumeric order.

```java
import org.junit.jupiter.api.MethodOrderer;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.TestMethodOrder;

@TestMethodOrder(MethodOrderer.Alphanumeric.class)
class AlphanumericOrderedTest {

    @Test
    void testA_someScenario() {
        // Runs first
    }

    @Test
    void testC_anotherScenario() {
        // Runs third
    }

    @Test
    void testB_middleScenario() {
        // Runs second
    }
}
```

#### **3. Random Order**

You can also have JUnit run your tests in a random order. This can be a useful technique to find hidden, unintended dependencies between tests. If your test suite passes when run in a fixed order but fails when run randomly, it's a clear sign that one test is accidentally relying on the side effects of another.

```java
import org.junit.jupiter.api.MethodOrderer;
import org.junit.jupiter.api.TestMethodOrder;

@TestMethodOrder(MethodOrderer.Random.class)
class RandomOrderedTest {
    // ... tests will run in a new random order on every execution
}
```

**⚠️ A Word of Caution**

While these tools are available, **always prefer to write independent tests.** Needing to order your unit tests is often a "code smell," indicating that your tests are not properly isolated. Relying on order can lead to a brittle and hard-to-maintain test suite. Use `@TestMethodOrder` sparingly.

---

### **🏆 Lesson 2 Recap: JUnit 5**

You have now mastered the core of JUnit 5. Let's recap what you've learned:

*   **Basic Annotations:** You can structure a test class using `@Test`, `@BeforeEach`, `@AfterEach`, `@BeforeAll`, and `@AfterAll` to manage the test lifecycle.
*   **Naming Conventions:** You know how to write descriptive test names like `shouldReturnX_whenY` that serve as living documentation.
*   **Assertions:** You can verify outcomes using a wide range of assertions like `assertEquals`, `assertThrows`, `assertTrue`, and group them with `assertAll`.
*   **Parameterized Tests:** You can write clean, data-driven tests using `@ParameterizedTest` with `@ValueSource` and `@CsvSource` to avoid code duplication.
*   **Nested Tests:** You can organize complex test classes into a readable, hierarchical structure with `@Nested`.
*   **Edge Cases:** You are equipped to test tricky scenarios involving floating-point numbers, exception messages, timeouts, and immutability.
*   **Ordering:** You understand the importance of test independence and know how to control execution order with `@TestMethodOrder` when absolutely necessary.

You are now fully capable of writing professional, maintainable unit tests for code that has no external dependencies.

But what happens when your code needs to talk to a database, an external API, or another class? We can't use those real dependencies in a *unit test* because that would violate the principle of **isolation**.

This is the perfect time to introduce our next major topic: **Mockito**, the mocking framework that allows us to fake these dependencies.

Are you ready to begin **Lesson 3: Mockito (Mocking Framework)**?