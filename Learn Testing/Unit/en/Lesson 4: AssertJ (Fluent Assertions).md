Excellent. We have the test runner (JUnit 5) and the mocking framework (Mockito). The final piece of our core testing trio is an assertion library that makes our tests incredibly readable and powerful: **AssertJ**.

### **Phase 1, Lesson 4: AssertJ (Fluent Assertions)**

While JUnit 5 provides a solid set of assertion methods (`assertEquals`, `assertTrue`, etc.), they can sometimes feel a bit clunky. The order of `(expected, actual)` is easy to mix up, and chaining multiple checks can be awkward.

AssertJ is a third-party library that solves this with a **fluent API**. This means you can chain assertion methods together to form readable, sentence-like statements.

**JUnit 5 Assertion:**
`assertEquals("Frodo", user.getName());`
`assertTrue(user.getAge() > 30);`
`assertNotNull(user.getRing());`

**AssertJ Fluent Assertion:**
`assertThat(user.getName()).isEqualTo("Frodo");`
`assertThat(user.getAge()).isGreaterThan(30);`
`assertThat(user.getRing()).isNotNull();`

The AssertJ version is easier to read, and your IDE's autocompletion will show you all the possible assertions you can make on a given object type, which is a huge productivity boost.

### **1️⃣ Basic Usage: `assertThat(value).isEqualTo(expected)`**

The entry point for every AssertJ assertion is the static method `assertThat()`. You pass the *actual* value (the result from your code) into this method. It returns a special assertion object that has methods tailored to the type of the value you passed in.

```java
import static org.assertj.core.api.Assertions.assertThat; // The only import you need!

class AssertJBasicsTest {

    @Test
    void a_simple_assertion() {
        String name = "Frodo";
        int age = 50;

        // Start with assertThat() and chain your assertion
        assertThat(name).isEqualTo("Frodo");
        assertThat(age).isEqualTo(50);

        // It also clearly shows the difference between actual and expected
        assertThat(name).isNotEqualTo("Sauron");
    }
}
```

### **2️⃣ Common Methods: The Bread and Butter of AssertJ**

Let's explore the most common assertions you'll use every day.

#### **String Assertions**
```java
String fellowship = "The Lord of the Rings";
assertThat(fellowship).isNotNull();
assertThat(fellowship).startsWith("The");
assertThat(fellowship).endsWith("Rings");
assertThat(fellowship).contains(" of the ");
assertThat(fellowship).isEqualToIgnoringCase("the lord of the rings");

String emptyString = "";
assertThat(emptyString).isEmpty();
assertThat("  ").isBlank(); // Checks for null, empty, or whitespace-only
```

#### **Collection Assertions (Lists, Sets)**
```java
List<String> hobbits = List.of("Frodo", "Sam", "Pippin", "Merry");

assertThat(hobbits).isNotNull();
assertThat(hobbits).hasSize(4);
assertThat(hobbits).contains("Sam", "Frodo"); // In any order, can have others
assertThat(hobbits).containsExactly("Frodo", "Sam", "Pippin", "Merry"); // Exact elements, in exact order
assertThat(hobbits).containsExactlyInAnyOrder("Sam", "Merry", "Frodo", "Pippin"); // Exact elements, any order
assertThat(hobbits).doesNotContain("Sauron");
```

### **3️⃣ Object Assertions: `extracting()` and `usingRecursiveComparison()`**

This is where AssertJ truly shines, especially when dealing with collections of complex objects.

#### **`extracting()`: Asserting on Properties of Objects in a Collection**

Imagine you have a `List<User>` and you want to verify the list of usernames.

```java
@Test
void testing_a_list_of_users() {
    List<User> users = List.of(
        new User("alex", "alex@example.com"),
        new User("bob", "bob@example.com")
    );

    // The old way would be a loop. The AssertJ way is clean and powerful.
    assertThat(users)
        .extracting(User::getUsername) // Extracts the 'username' property from each user
        .containsExactly("alex", "bob"); // Asserts on the resulting list of strings

    // You can even extract multiple properties into a tuple
    assertThat(users)
        .extracting(User::getUsername, User::getEmail)
        .containsExactly(
            tuple("alex", "alex@example.com"),
            tuple("bob", "bob@example.com")
        );
}
```

#### **`usingRecursiveComparison()`: Comparing Complex Objects Field-by-Field**

What if you want to assert that two `User` objects are equal, but the `User` class doesn't have an `equals()` method? Or you want to ignore a field like a randomly generated `id`?

`usingRecursiveComparison()` compares objects by reflectively checking all of their fields.

```java
@Test
void comparing_two_user_objects() {
    User user1 = new User(1L, "alex", "alex@example.com");
    User user2 = new User(1L, "alex", "alex@example.com");
    User user3 = new User(2L, "alex", "alex@example.com"); // Different ID

    // This will pass because all fields are identical
    assertThat(user1).usingRecursiveComparison().isEqualTo(user2);

    // This will fail because the IDs are different. But what if we don't care about the ID?
    // We can tell the comparison to ignore specific fields.
    assertThat(user1)
        .usingRecursiveComparison()
        .ignoringFields("id") // Very useful for auto-generated fields
        .isEqualTo(user3);
}
```

### **4️⃣ Exception Assertions: `assertThatThrownBy()` and `catchThrowable()`**

AssertJ provides a fluent way to test exceptions that is often more readable than JUnit's `assertThrows`.

#### **`assertThatThrownBy()`**
This is the most direct way. You provide a lambda for the code that should throw an exception.

```java
@Test
void testing_for_an_exception() {
    User user = new User(null, null); // Invalid user

    assertThatThrownBy(() -> {
        // Code that is expected to throw
        validator.validateUser(user);
    })
    .isInstanceOf(IllegalArgumentException.class) // You can chain assertions about the exception
    .hasMessageContaining("Username must not be null");
}
```

#### **`catchThrowable()`**
This is useful if you want to capture the exception object for more complex, multi-line assertions.

```java
@Test
void capturing_an_exception() {
    // Act: Execute the code and capture the thrown exception
    Throwable thrown = catchThrowable(() -> validator.validateUser(null));

    // Assert: Run assertions on the captured throwable
    assertThat(thrown)
        .isInstanceOf(IllegalArgumentException.class)
        .hasMessage("User cannot be null");
}
```

### **5️⃣ Edge Cases: Asserting on `Optional`, Maps, and Complex Objects**

AssertJ has rich support for virtually any type you can think of.

*   **`Optional` Values:**
    ```java
    Optional<String> maybeName = Optional.of("Gandalf");
    assertThat(maybeName).isPresent();
    assertThat(maybeName).hasValue("Gandalf");

    Optional<String> emptyOptional = Optional.empty();
    assertThat(emptyOptional).isEmpty();
    ```
*   **Maps:**
    ```java
    Map<String, String> translations = Map.of("one", "uno", "two", "dos");
    assertThat(translations).hasSize(2);
    assertThat(translations).containsKey("one");
    assertThat(translations).containsValue("dos");
    assertThat(translations).containsEntry("one", "uno");
    ```

### **🏆 Lesson 4 Recap: AssertJ**

*   **Fluent API:** You understand how AssertJ's chainable methods improve test readability and maintainability.
*   **Core Assertions:** You can write basic assertions for Strings, Collections, and other common types using `assertThat(...)`.
*   **Advanced Object Assertions:** You can perform powerful assertions on collections of objects using `extracting()` and compare complex objects field-by-field with `usingRecursiveComparison()`.
*   **Exception Testing:** You can write clean and descriptive exception tests with `assertThatThrownBy()`.
*   **Rich Type Support:** You know that AssertJ has dedicated assertions for almost any type, including `Optional` and `Map`.

You are now fluent in the "Big Three" of Java unit testing: **JUnit 5**, **Mockito**, and **AssertJ**. With these tools, you can write clean, professional, and maintainable tests for almost any situation.

The next step is to put this all together in a properly structured project.

Ready to begin **Lesson 5: Test Project Structure & Setup**?