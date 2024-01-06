# Writing test for Java project using JUnit5

## These are the steps we need to follow to write JUnit tests.

- Right-click on the project, go to Build Path, and Add Libraries.
- The syntax for creating tests in Java:

  - We have some class called MyClass.
  - To set up tests we would create a class called MyClassTest {}

  ```java
  class MyClassTest {
    // Here we would need a method for each test. A method that returns a void
    // We need @Test annotation
    @Test
    public void methodName_TestState_ExpectedOutcome() {
      //1. Arrange (set up) our data.
      //2. Act - call the method that we are testing.
      //3. Assert - make sure the output of the method is what we expected.
    }
  }
  ```

  - Example

  ```java
  public class Utils {
  public static int add(int a, int b) {
  	return a + b;
  }
  }
  ```

  We will have the test in a separate file like this:

  ```java
  import static org.junit.jupiter.api.Assertions.assertEquals;

  import org.junit.jupiter.api.Test;

  public class UtilsTest {

    @Test
    public void add_TwoPositiveIntegers_ReturnsCorrectSum() {
      // Arrange
      int expectedResult = 4;
      // Act
      int actual = Utils.add(2, 2);
      // Assert
      assertEquals(expectedResult, actual);
      assertEquals(8, Utils.add(3, 5));
    }

    @Test
    public void add_PositiveAndNegativeInteger_ReturnsCorrectSum() {
      int expected = 5;
      int actual = Utils.add(8, -3);
      assertEquals(expected, actual);
    }
  }

  ```
