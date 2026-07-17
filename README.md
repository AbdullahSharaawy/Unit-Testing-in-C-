# Unit Testing in C# with xUnit & Fluent Assertions

## Key Concepts

* **Unit Testing Frameworks:** Tools that integrate with IDEs (like Visual Studio) to easily run, manage, and track test results via a Test Explorer.
* **xUnit:** A popular and robust testing framework for .NET applications.
* **Project Structure & Naming Conventions:** Keeping production code and test code separated but clearly linked by standard naming conventions.

## Explanation

To begin unit testing, you need to structure your solution correctly. A testing framework like xUnit makes your life easier by automatically discovering your tests and allowing you to run them quickly through the Visual Studio Test Explorer.

**Standard Setup Steps:**

1. Create your main project (e.g., `NetworkUtility` console app).
2. Add a new xUnit Test Project to your solution.
3. **Naming Convention:** Name the test project `[MainProjectName].Tests` (e.g., `NetworkUtility.Tests`).
4. Add a project reference from your test project pointing to your main project.
5. Create a test class named after the class you are testing, appended with "Tests" (e.g., `NetworkServiceTests`).

> **Callout:** A fast test suite is crucial. In a production environment, you might have thousands of tests, and they should ideally run in just a few seconds.

---

# The Arrange, Act, Assert (AAA) Pattern

## Key Concepts

* **Arrange:** Set up the variables, dependencies, and conditions needed for the test.
* **Act:** Execute the specific function or method being tested.
* **Assert:** Verify that the function returns the expected result.

## Explanation

When writing unit tests, don't get bogged down in the internal workings of the function. Instead, treat it like a black box: you are primarily testing **what the function returns** based on the inputs provided.

The AAA pattern is an industry-standard way to structure your test code:

* **Arrange:** Think of this as getting your "glass of water" ready. You are gathering the inputs and instantiating the classes you need.
* **Act:** This is the action of "drinking the water." You are actively calling the method under test.
* **Assert:** This is checking the outcome to ensure it matches your expectations.

---

# Testing Strings with `[Fact]` and Fluent Assertions

## Key Concepts

* **`[Fact]` Attribute:** Tells the test runner that this method is a unit test that takes no parameters.
* **Test Method Naming Convention:** `ClassName_MethodName_ExpectedResult` (e.g., `NetworkService_SendPing_ReturnString`).
* **Fluent Assertions:** A third-party library that provides a more readable, natural language syntax for assertions and better error messages than standard xUnit asserts.

## Explanation

Let's test a simple method `SendPing()` that returns the string `"Success: Ping Sent"`.

Instead of standard assertions, we use **Fluent Assertions**. It allows you to chain assertions using the `.Should()` method, making your tests read like regular English.

> **Callout:** Testing is a balancing act. If you make assertions too rigid, tests become annoying to maintain. If you make them too loose, bugs will slip through. Aim for a middle ground.

```csharp
using Xunit;
using FluentAssertions;
using NetworkUtility.Ping; // Your main project reference

public class NetworkServiceTests
{
    [Fact]
    public void NetworkService_SendPing_ReturnString()
    {
        // Arrange: Bring in the service
        var pingService = new NetworkService();

        // Act: Execute the function
        var result = pingService.SendPing();

        // Assert: Evaluate the output using Fluent Assertions
        result.Should().NotBeNullOrWhiteSpace();
        result.Should().Be("Success: Ping Sent");
        result.Should().Contain("Success", Exactly.Once());
    }
}

```

---

# Testing Parameters with `[Theory]` and `[InlineData]`

## Key Concepts

* **`[Theory]` Attribute:** Denotes a parameterized test that is true for a particular set of data.
* **`[InlineData]` Attribute:** Provides the specific variable values to pass into the `[Theory]` test.
* **Integer Assertions:** Validating mathematical logic and boundary conditions.

## Explanation

Sometimes a function takes arguments, and you want to test multiple scenarios without rewriting the entire test method. This is where `[Theory]` shines compared to `[Fact]`.

| Concept | Purpose | Input Parameters |
| --- | --- | --- |
| **`[Fact]`** | Tests a single, specific scenario that doesn't change. | None |
| **`[Theory]`** | Tests the same logic across multiple different data sets. | Provided via `[InlineData]` |

Let's test a method called `PingTimeout(int a, int b)` that adds two integers. We will pass in parameters `a` and `b`, as well as the `expected` result.

```csharp
using Xunit;
using FluentAssertions;

public class NetworkServiceTests
{
    [Theory]
    [InlineData(1, 1, 2)]
    [InlineData(2, 2, 4)]
    public void NetworkService_PingTimeout_ReturnInt(int a, int b, int expected)
    {
        // Arrange
        var pingService = new NetworkService();

        // Act
        var result = pingService.PingTimeout(a, b);

        // Assert
        result.Should().Be(expected);
        result.Should().BeGreaterThanOrEqualTo(2);
        result.Should().NotBeInRange(-1000, 0); // Ensures the result is strictly positive
    }
}

```
