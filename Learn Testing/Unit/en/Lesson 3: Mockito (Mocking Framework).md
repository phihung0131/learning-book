### **Phase 1, Lesson 3: Mockito (Mocking Framework)**

So far, we've tested simple, self-contained classes like a `Calculator`. But in a real application, objects rarely work alone. A `UserService` might depend on a `UserRepository` to talk to the database. A `PaymentService` might depend on a `StripeClient` to process payments.

If the `UserService` test actually tries to connect to a real database, it is **no longer a unit test**. It becomes a slow, unreliable, and complex **integration test**.

**The Problem:** How do we test `UserService` logic in complete **isolation** from the `UserRepository`?

**The Solution:** We use a **mock**.

### **1️⃣ Concept of Mocking, Stubbing, and Verification**

#### **What is a Mock?**

A **mock object** is a special type of test double—a "fake" object that we control completely. It looks and acts like the real object (e.g., a `UserRepository`), but instead of performing the real action (like hitting a database), it does exactly what we tell it to do for the duration of a single test.

Think of it like a crash test dummy. Car manufacturers don't put a real person in a car to test a new airbag. They use a dummy that they can control and monitor to simulate the behavior of a person in a crash. A mock is our crash test dummy for dependencies.

#### **The Three Key Activities of Mocking**

Working with mocks involves three distinct steps:

1.  **Creation:** First, you create the mock object. Mockito is the framework that allows us to create these fake objects effortlessly.
2.  **Stubbing (Behavior Setup):** This is the "telling the mock what to do" part. You define rules for the mock. For example: "**When** the `findUserById(5)` method is called on the mock `UserRepository`, **then return** a specific `User` object." This is also known as "programming the behavior" of the mock. The method call we are faking is called a "stub."
3.  **Verification:** After you call the method you are testing, you can optionally ask the mock questions to see if it was used correctly. For example: "**Verify** that the `save(user)` method on the mock `UserRepository` was called **exactly one time**."

Let's illustrate with a conceptual example:

```java
// Production Code
public class UserService {
    private final UserRepository userRepository;

    public UserService(UserRepository userRepository) { // Dependency is injected
        this.userRepository = userRepository;
    }

    public String getUserEmail(int userId) {
        User user = userRepository.findUserById(userId);
        if (user != null) {
            return user.getEmail();
        }
        return null;
    }
}
```

**Unit Test for `getUserEmail`:**

1.  **Creation:** We need a fake `UserRepository`. We use Mockito to create a mock `UserRepository` object.
2.  **Stubbing:** We need to test the "user found" scenario. So, we tell our mock: "**When `findUserById(10)` is called, then return a new `User` object with the email 'test@example.com'.**"
3.  **Action:** We call `userService.getUserEmail(10)`.
4.  **Assertion:** We use JUnit/AssertJ to assert that the returned email is `"test@example.com"`.
5.  **Verification (Optional):** We could verify that `findUserById(10)` was indeed called on our mock.

This test runs in milliseconds, never touches a database, and tests only the logic inside the `getUserEmail` method. That is the power of mocking.

Now let's look at the specific Mockito tools for creating these mocks.

### **2️⃣ Difference Between `@Mock`, `@InjectMocks`, and `@Spy`**

Mockito provides annotations to make creating and injecting mocks clean and simple. To use them, you need to tell JUnit 5 to enable Mockito's annotation processing. This is typically done by adding `@ExtendWith(MockitoExtension.class)` to your test class.

```java
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.junit.jupiter.MockitoExtension;

@ExtendWith(MockitoExtension.class) // IMPORTANT: This enables Mockito annotations
class UserServiceTest {
    // ... mocks will be defined here
}
```

#### **`@Mock`**

This is the primary annotation you will use. It creates a complete mock (a "dummy") of a class or interface.

*   **Purpose:** To create a fake object for a dependency.
*   **Behavior:** By default, all methods on a `@Mock` object do nothing. They return `null`, empty collections, or the default primitive value (0, false). You must explicitly stub any method you need to return a value.
*   **Usage:** You use `@Mock` for all the dependencies that the class you are testing relies on.

```java
@ExtendWith(MockitoExtension.class)
class UserServiceTest {

    @Mock
    private UserRepository mockUserRepository; // Creates a fake UserRepository

    // ... tests go here
}
```

#### **`@InjectMocks`**

This annotation creates an instance of the class you want to test and automatically injects any fields annotated with `@Mock` or `@Spy` into it.

*   **Purpose:** To instantiate the **Class Under Test** and inject its mocked dependencies.
*   **Behavior:** Mockito tries to inject mocks by constructor, setter, or property injection. Constructor injection is the cleanest and most common.
*   **Usage:** You use `@InjectMocks` on the class you are actually testing (e.g., `UserService`).

Let's combine them:

```java
@ExtendWith(MockitoExtension.class)
class UserServiceTest {

    @Mock
    private UserRepository mockUserRepository; // The dependency

    @InjectMocks
    private UserService userService; // The class we are testing

    @Test
    void testSomething() {
        // Here, 'userService' is a real UserService instance,
        // but its 'userRepository' field holds our mock object.
    }
}
```

#### **`@Spy`**

A `@Spy` is a "partial mock." It wraps a **real instance** of an object. This is a more advanced and less commonly used feature.

*   **Purpose:** To use a real object but override (stub) only a few of its methods.
*   **Behavior:** When you call a method on a spy, the **real method is invoked**, unless you have explicitly stubbed it.
*   **When to Use It (Use with Caution!):**
    *   Testing legacy code that is hard to refactor.
    *   When you need to call a real method on an object but fake another one on the *same object*. This is rare in well-designed code.
*   **Danger:** Spies can make tests confusing. You are mixing real and fake behavior, which can lead to tests that are hard to understand and debug. **Always prefer `@Mock` when possible.**

```java
class SpyExampleTest {
    @Spy
    private List<String> spiedList = new ArrayList<>();

    @Test
    void testSpy() {
        // Call a real method
        spiedList.add("one");
        spiedList.add("two");
        assertEquals(2, spiedList.size()); // The real add() was called

        // Stub a method
        when(spiedList.size()).thenReturn(100); // Now we are overriding size()
        assertEquals(100, spiedList.size()); // The stubbed value is returned
    }
}
```

**Summary Table:**

| Annotation | Purpose | Is it a Real Object? | Default Behavior |
| :--- | :--- | :--- | :--- |
| **`@Mock`** | Fake a dependency | **No**, it's a dummy | Methods do nothing, return `null`/`0`/`false` |
| **`@InjectMocks`**| Create the Class Under Test | **Yes**, it's a real instance | Injects `@Mock`s into this object |
| **`@Spy`** | Partially mock a real object| **Yes**, it wraps a real instance| Methods call the **real** implementation unless stubbed |

Now that we know how to create mocks, let's learn the syntax for stubbing their behavior.

Ready to learn about `when(...).thenReturn(...)`?

Perfect. Let's get to the core of controlling our mocks.

### **3️⃣ Behavior Setup: `when(...)`, `thenReturn(...)`, `thenThrow(...)`, and `thenAnswer(...)`**

Once you have a mock object, it's just a hollow shell. **Stubbing** is the process of giving that shell behavior. The standard way to do this in Mockito is by using the `when(...)` construct.

The syntax is highly readable and follows a "Given-When-Then" pattern, but applied to the mock's behavior:
`when(mock.methodCall()).thenReturn(valueToReturn);`

Let's use our `UserService` and `UserRepository` example.

```java
@ExtendWith(MockitoExtension.class)
class UserServiceTest {

    @Mock
    private UserRepository mockUserRepository; // Our fake dependency

    @InjectMocks
    private UserService userService; // The class under test

    private User sampleUser;

    @BeforeEach
    void setUp() {
        // Create a sample user for our tests to use
        sampleUser = new User(1, "testuser", "test@example.com");
    }

    // ... tests follow
}
```

#### **`when(...).thenReturn(...)`**

This is the most common stubbing method. You use it when you want a mocked method to return a specific value.

*   **Goal:** Test the scenario where a user is found in the repository.
*   **Logic:** We must tell the `mockUserRepository` to return our `sampleUser` when its `findById` method is called with the ID `1`.

```java
@Test
void getUserById_shouldReturnUser_whenUserExists() {
    // 1. Stubbing the mock's behavior
    when(mockUserRepository.findById(1)).thenReturn(sampleUser);

    // 2. Calling the method we are testing
    User foundUser = userService.getUserById(1);

    // 3. Asserting the outcome
    assertNotNull(foundUser);
    assertEquals("test@example.com", foundUser.getEmail());
}
```

What about the case where the user *doesn't* exist? The real repository would return `null`. We can stub that too.

```java
@Test
void getUserById_shouldReturnNull_whenUserDoesNotExist() {
    // 1. Stubbing: when findById is called with any other ID, it should return null.
    // By default, a mock already returns null for object types, but it's good practice
    // to be explicit for clarity.
    when(mockUserRepository.findById(99)).thenReturn(null);

    // 2. Action
    User foundUser = userService.getUserById(99);

    // 3. Assertion
    assertNull(foundUser);
}
```

#### **`when(...).thenThrow(...)`**

Sometimes you need to test how your code reacts when a dependency throws an exception. For instance, what if the database is unavailable and the repository throws a `DatabaseException`?

`thenThrow(...)` allows you to stub a method to throw an exception when it's called.

*   **Goal:** Test that our service handles database errors gracefully.

```java
// Assume our UserService has a method like this:
public void deleteUser(int userId) {
    try {
        userRepository.delete(userId);
    } catch (DatabaseException e) {
        // Maybe it logs the error and throws a custom, more specific exception
        throw new UserDeletionFailedException("Failed to delete user", e);
    }
}

@Test
void deleteUser_shouldThrowCustomException_whenDatabaseFails() {
    // 1. Stubbing: Tell the mock to throw an exception when delete is called.
    when(mockUserRepository.delete(1)).thenThrow(new DatabaseException("DB is down"));
    // You can also pass the class: .thenThrow(DatabaseException.class);

    // 2. Action & Assertion (using JUnit 5's assertThrows)
    assertThrows(UserDeletionFailedException.class, () -> {
        userService.deleteUser(1);
    });
}
```

#### **`when(...).thenAnswer(...)`**

This is a more advanced and powerful form of stubbing. Instead of returning a fixed value, `thenAnswer(...)` allows you to provide a small piece of logic (a lambda) that will be executed when the mock is called. This is useful when the return value needs to be dynamically calculated based on the input arguments.

*   **Goal:** Test a scenario where the mock's return value depends on its input.
*   **Example:** Imagine a mock that should return a `User` object whose email is based on the ID it was asked to find.

```java
@Test
void findById_shouldReturnUserWithDynamicEmail() {
    // 1. Stubbing with an Answer
    when(mockUserRepository.findById(anyInt())).thenAnswer(invocation -> {
        Integer id = invocation.getArgument(0); // Get the first argument passed to findById
        return new User(id, "user" + id, "user" + id + "@example.com");
    });

    // 2. Action
    User user5 = userService.getUserById(5);
    User user10 = userService.getUserById(10);

    // 3. Assertion
    assertEquals("user5@example.com", user5.getEmail());
    assertEquals("user10@example.com", user10.getEmail());
}
```
In the lambda, `invocation.getArgument(0)` retrieves the first argument passed to the mocked method (`findById`). `anyInt()` is an "argument matcher," a concept we will cover very soon.

Stubbing defines how the mock behaves. But how do we check if our code interacted with the mock correctly? That's where verification comes in.

Ready to learn about `verify(...)`?

Of course. We've programmed our mock's behavior. Now it's time to play detective and check if our code under test used the mock as we expected.

### **4️⃣ Verifying Interactions: `verify()`, `times()`, `never()`**

While **stubbing** is about setting up behavior before the test runs, **verification** happens *after* the test action has been executed. Its purpose is to check if certain methods on your mock objects were called with the correct arguments.

Verification is especially crucial when testing methods that don't return a value (i.e., `void` methods). For these methods, you can't assert a result; their entire purpose is to produce a side effect by interacting with a dependency.

The entry point for all verification is the `Mockito.verify()` method.

Let's imagine a `RegistrationService` that saves a new user and then sends them a welcome email.

```java
// Class Under Test
public class RegistrationService {
    private final UserRepository userRepository;
    private final EmailService emailService;

    // Constructor...

    public void registerUser(String username, String email) {
        if (username == null || email == null) {
            throw new IllegalArgumentException("Username and email must not be null");
        }
        User newUser = new User(username, email);
        userRepository.save(newUser);
        emailService.sendWelcomeEmail(newUser); // This is the interaction we want to verify
    }
}
```

Now, let's write a test for it.

```java
@ExtendWith(MockitoExtension.class)
class RegistrationServiceTest {

    @Mock
    private UserRepository mockUserRepository;

    @Mock
    private EmailService mockEmailService;

    @InjectMocks
    private RegistrationService registrationService;

    @Test
    void registerUser_shouldSaveUserAndSendWelcomeEmail() {
        // 1. Arrange (Setup)
        String username = "testuser";
        String email = "test@example.com";
        User user = new User(username, email);

        // We don't need to stub anything for this test, because the methods we
        // are calling on the mocks (save, sendWelcomeEmail) return void.

        // 2. Act (Call the method under test)
        registrationService.registerUser(username, email);

        // 3. Verify (Check the interactions with the mocks)

        // Verify that the save method was called on the repository exactly once
        // with an object that matches our user. We'll use an ArgumentMatcher for now.
        verify(mockUserRepository).save(any(User.class));

        // Verify that the sendWelcomeEmail method was also called exactly once.
        verify(mockEmailService).sendWelcomeEmail(any(User.class));
    }
}
```

#### **Default Verification: `times(1)`**

When you write `verify(mock).methodCall()`, it's shorthand for `verify(mock, times(1)).methodCall()`. It checks that the method was called **exactly one time**. This is the most common type of verification.

#### **`times(n)`**

You can verify an exact number of invocations using `times(n)`.

*   **Example:** Imagine a service that retries sending an email 3 times before giving up.
    ```java
    @Test
    void shouldRetrySendingEmailThreeTimes() {
        // ... setup to make email sending fail

        // Act
        emailGateway.sendEmailWithRetries(message);

        // Verify
        verify(mockEmailClient, times(3)).send(message);
    }
    ```

#### **`never()`**

This is an extremely powerful and useful verifier. `never()` is an alias for `times(0)`. It asserts that a specific interaction **did not happen**. This is perfect for testing conditional logic.

Let's test the negative path of our `registerUser` method.

```java
@Test
void registerUser_shouldNotSaveOrSendEmail_whenUsernameIsNull() {
    // 1. Arrange
    String username = null;
    String email = "test@example.com";

    // 2. Act & Assert (check for the expected exception)
    assertThrows(IllegalArgumentException.class, () -> {
        registrationService.registerUser(username, email);
    });

    // 3. Verify (check that NO interactions happened with the mocks)
    verify(mockUserRepository, never()).save(any(User.class));
    verify(mockEmailService, never()).sendWelcomeEmail(any(User.class));
}
```
This test beautifully confirms that if the validation fails, our service correctly stops processing and doesn't perform any unwanted side effects.

Other useful verifiers include `atLeast(n)`, `atMost(n)`, and `atLeastOnce()`.

In the examples above, we used `any(User.class)`. This is an **argument matcher**. It tells Mockito we don't care about the exact instance of the `User` object, just that *some* `User` object was passed.

But what if we need to inspect the object that was passed to the mock? What if we want to run assertions on the user object that was passed to `userRepository.save()`? We can't do that with `verify` alone.

For that, we need a special tool: the `ArgumentCaptor`.

Ready to learn how to capture and inspect arguments?

Excellent. Let's move on to a more advanced—and incredibly useful—Mockito technique.

### **5️⃣ `ArgumentCaptor` (Capturing Values Passed to Mocks)**

So far, `verify()` has helped us confirm *if* a method was called and *how many times*. We've used argument matchers like `any(User.class)` or `eq("some string")` to match the invocations.

But what if we need to verify something *about the object that was passed* to the mock?

**The Problem:**

In our `registerUser` example, the `RegistrationService` creates a `new User(...)` object inside the method. We don't have a reference to this specific object in our test, so we can't pass it to `verify(mock).save(ourUserObject)`. We used `any(User.class)`, but that's a vague check. It doesn't confirm that the `User` object passed to the `save` method had the correct username and email.

How can we capture the "invisible" object that was passed to the mock so we can run assertions on it?

**The Solution: `ArgumentCaptor`**

An `ArgumentCaptor` is a special Mockito object that acts like a net. You use it in a `verify()` call to "capture" an argument that was passed to a mocked method. Once captured, you can retrieve the value and perform detailed assertions on it.

The process involves three steps:
1.  **Create:** Create an instance of `ArgumentCaptor`.
2.  **Capture:** Use the captor in your `verify()` call.
3.  **Assert:** Get the captured value from the captor and run your assertions.

Let's refine our `registerUser` test to be more precise.

```java
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.ArgumentCaptor;
import org.mockito.Captor;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;

import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.mockito.Mockito.verify;


@ExtendWith(MockitoExtension.class)
class RegistrationServiceTest {

    @Mock
    private UserRepository mockUserRepository;

    @Mock
    private EmailService mockEmailService;

    @InjectMocks
    private RegistrationService registrationService;

    // Option 1: Create the captor using an annotation
    @Captor
    private ArgumentCaptor<User> userArgumentCaptor;

    @Test
    void registerUser_shouldSaveUserWithCorrectDetails() {
        // Arrange
        String username = "testuser";
        String email = "test@example.com";

        // Option 2: Create the captor manually if not using @Captor
        // ArgumentCaptor<User> userArgumentCaptor = ArgumentCaptor.forClass(User.class);

        // Act
        registrationService.registerUser(username, email);

        // Capture the User object that was passed to the save method
        // Instead of any(User.class), we use captor.capture()
        verify(mockUserRepository).save(userArgumentCaptor.capture());

        // Retrieve the captured value
        User capturedUser = userArgumentCaptor.getValue();

        // Assert: Now we can run detailed assertions on the captured object
        assertEquals(username, capturedUser.getUsername());
        assertEquals(email, capturedUser.getEmail());
    }
}
```

#### **How it Works:**

1.  **`@Captor` Annotation:** Just like `@Mock`, this annotation tells the `MockitoExtension` to initialize the `ArgumentCaptor` for us. It's the cleanest way to create one.
2.  **`userArgumentCaptor.capture()`:** We pass this inside the `verify()` call. It looks like an argument matcher, but it has a side effect: it secretly saves the argument that was passed to the `save` method.
3.  **`userArgumentCaptor.getValue()`:** This method retrieves the last captured value. If the mocked method was called multiple times, `getValue()` returns the argument from the last call. You can use `getAllValues()` to get a `List` of all arguments from all invocations.

`ArgumentCaptor` is an indispensable tool for verifying the side effects of your code with high precision. It ensures that your method is not just calling its dependencies, but calling them with the *correctly constructed data*.

Now that we've covered the core mechanics, let's explore some of the trickier situations you'll encounter when mocking.

Ready to tackle the **Edge Cases of Mockito**?

Of course. Mastering the basics is one thing, but knowing how to handle tricky and complex scenarios is what will make you truly proficient. Let's explore the edge cases of Mockito.

### **6️⃣ Edge Cases: Multiple Mocks, Chained Mocks, Void Methods, Exceptions, and Mocking Static/Final Classes**

#### **1. Mocking Chained Method Calls**

Sometimes you encounter code that uses a "fluent" or chained API style, like `builder.setName("X").setAge(20).build()`. What if a dependency returns an object, which in turn you need to call a method on?

*   **The Scenario:** `String street = userService.getAddress().getStreet();`
*   **The Problem:** If you mock `userService`, `userService.getAddress()` will return `null` by default, and the call to `.getStreet()` will cause a `NullPointerException` in your test.
*   **The Solution:** You need to mock both levels. You can create a mock for `Address` and have the `userService` mock return it.

    ```java
    @Test
    void testChainedCall() {
        // 1. Create mocks for both objects in the chain
        @Mock UserService mockUserService;
        @Mock Address mockAddress;

        // 2. Stub the first call to return the second mock
        when(mockUserService.getAddress()).thenReturn(mockAddress);

        // 3. Stub the second call on the returned mock
        when(mockAddress.getStreet()).thenReturn("Main St");

        // Act & Assert
        assertEquals("Main St", mockUserService.getAddress().getStreet());
    }
    ```
*   **Alternative (Recursive Mocking - Use with Caution):** Mockito supports "deep stubs" or recursive mocking, which automatically creates mocks for chained calls. This can make tests less explicit and more brittle, so it should be used sparingly.
    ```java
    // Create the mock with DEEP_STUBS
    UserService mockUserService = mock(UserService.class, RETURNS_DEEP_STUBS);

    // Now you can stub the final call directly
    when(mockUserService.getAddress().getStreet()).thenReturn("Main St");

    assertEquals("Main St", mockUserService.getAddress().getStreet());
    ```

#### **2. Handling `void` Methods**

How do you stub a method that doesn't return anything? You can't use `when(...).thenReturn(...)`. Instead, Mockito provides a different syntax family: `do...().when()`.

*   **The Problem:** You want to test what happens when a `void` method, like `userRepository.delete(user)`, throws an exception.
*   **The Solution:** Use `doThrow()`.

    ```java
    @Test
    void shouldHandleException_whenVoidMethodIsCalled() {
        // Arrange: Use the do-when syntax for void methods
        doThrow(new DatabaseException("Connection failed"))
            .when(mockUserRepository)
            .delete(any(User.class));

        // Act & Assert
        assertThrows(DatabaseException.class, () -> {
            someService.deleteUser(sampleUser);
        });
    }
    ```*   **Other `do...()` methods:**
    *   **`doNothing()`:** This is the default behavior for mocked void methods, but you can use it to override a spy's real behavior.
    *   **`doAnswer()`:** Allows you to perform a complex action (like modifying an argument) when the void method is called.
    *   **`doCallRealMethod()`:** Used with spies to force the real method to be called if you've previously stubbed it.

**Remember:** For `void` methods, the primary way to test their effect is through `verify()` and `ArgumentCaptor`.

#### **3. Mocking Static, Final, and Private Methods (Advanced)**

This is a very common interview question.

*   **The Limitation:** By default, Mockito **cannot** mock `final` classes, `static` methods, or `private` methods. It works by creating a subclass (a proxy) of the class you want to mock at runtime. This is impossible for `final` classes (which can't be subclassed) or `static` methods (which belong to the class, not an instance).
*   **The Official Solution: `mockito-inline`**
    To overcome this, the Mockito team created an alternative "mock maker." By including the `mockito-inline` dependency in your `pom.xml`, you enable Mockito to use Java instrumentation to modify classes on the fly, allowing it to mock these previously "unmockable" things.

    **Example: Mocking a `static` method**
    Imagine you have a utility class you can't change:
    ```java
    public final class IdGenerator {
        public static UUID generateId() {
            return UUID.randomUUID();
        }
    }
    ```
    Your test needs to control the ID that gets generated.
    ```java
    @Test
    void shouldCreateUserWithPredictableId() {
        UUID predictableId = UUID.fromString("123e4567-e89b-12d3-a456-426614174000");

        // Use a try-with-resources block to control the scope of the static mock
        try (MockedStatic<IdGenerator> mockedStatic = Mockito.mockStatic(IdGenerator.class)) {

            // Stub the static method call
            mockedStatic.when(IdGenerator::generateId).thenReturn(predictableId);

            // Act: Call the service that uses the static method internally
            User newUser = userService.createNewUser("test");

            // Assert
            assertEquals(predictableId, newUser.getId());
        }
        // The static mock is automatically closed and reset after the try block
    }
    ```
*   **A Word of Warning:** The need to mock a `static` or `private` method is often a sign of poor design. It's usually better to refactor the code to allow for dependency injection instead. However, `mockito-inline` is an invaluable tool when working with legacy code that you cannot change.

---

### **7️⃣ Best Practices and Common Mistakes**

1.  **Don't Mock Everything (Over-mocking):** The goal is to mock the boundaries of your system (database, APIs, etc.). Don't mock every single class your object talks to. If `ClassA` uses `ClassB`, and `ClassB` is a simple helper class within your own codebase, you should probably use the real `ClassB` in your test for `ClassA`.
2.  **Don't Mock Data Objects:** Never mock a DTO, a value object, or an entity (e.g., `User`, `Product`). Mocks are for faking **behavior**, not for holding data. It's simpler, clearer, and safer to just create a real instance: `User user = new User("name", "email");`.
3.  **Prefer `@Mock` over `@Spy`:** Spies mix real and fake behavior, which can make tests confusing and hard to debug. Always try to use a pure mock first. Spies are a tool for dealing with difficult legacy code.
4.  **Focus Verification on Essential Interactions:** Avoid the temptation to `verify()` every single method call. This leads to brittle tests (overspecification). Only verify the interactions that are critical to the behavior you are testing. For example, verifying a call to a logger is usually unnecessary.

### **🏆 Lesson 3 Recap: Mockito**

*   **Core Concepts:** You understand the difference between **Mocking** (creating fakes), **Stubbing** (programming their behavior), and **Verification** (checking interactions).
*   **Annotations:** You can effectively use `@Mock` for dependencies, `@InjectMocks` for the class under test, and know when to cautiously use `@Spy`.
*   **Stubbing:** You can control your mocks using `when(...).thenReturn(...)` for happy paths and `when(...).thenThrow(...)` for error conditions.
*   **Verification:** You can confirm side effects using `verify()`, `times()`, and `never()`.
*   **`ArgumentCaptor`:** You can capture and assert the exact arguments passed to your mocks for high-precision tests.
*   **Edge Cases:** You are now equipped to handle `void` methods, chained calls, and even mock `static` methods using `mockito-inline`.

You have now conquered the most complex part of unit testing. The combination of JUnit 5 and Mockito is incredibly powerful. However, we can make our assertions even more readable and powerful.

Ready to begin **Lesson 4: AssertJ (Fluent Assertions)**?