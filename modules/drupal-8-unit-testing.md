<!--
{
"name" : "drupal-8-unit-testing",
"version" : "0.0.1",
"title" : "Lesson 10.2 - Unit testing",
"description" : "Unit testing",
"freshnessDate" : 2015-12-11,
"homepage" : "https://docs.acquia.com/articles/drupal-8-unit-testing",
"canonicalSource" : "https://docs.acquia.com/articles/drupal-8-unit-testing",
"license" : "CC BY-SA"
}
-->

<!-- @section -->

# Overview

Drupal comes with a version of PHPUnit, but you will need to make sure it is configured properly. We are going to assume that we are using [PhpStorm](https://www.jetbrains.com/phpstorm/), but you can run these tests from the command line as well.

<!-- @section -->

## When should you create unit tests?

Services make the most sense for unit tests because they are already designed to be independent. There is a lot of debate around what to test. It may make sense to start by testing all of your service methods and then adding further tests as needed.

<!-- @section -->

## Configure PHPUnit in PhpStorm

Configure the Project:

1.  Navigate to **PhpStorm > Preferences > Languages & Frameworks > PHP > PHPUnit > Local**.
2.  Select the **PHPUnit Library > Use custom autoloader**.
3.  Navigate to `/core/vendor/autoload.php`
4.  Select **Test Runner > Default configuration file**.
5.  Navigate to `/core/phpunit.xml.dist`.
6.  Click **Save**.

This sets the project specific settings. You can see this in the top right: **For current project**. Next, complete the following steps:

1.  Run: **Run > Edit Configurations**
2.  Click the **+** icon and select **PHPUnit**.
3.  Provide a name, such as `Drupal 8: All Tests`.
4.  Select **Test Runner > Defined** in the configuration file.
5.  Save.

In the top right, ensure your run configuration is selected and press the green arrow. You can also go to **Run > Run Drupal 8: All Tests**.

If you want to set global defaults, you can go to **File > Default Settings**, but that isn't necessary for a Drupal 8 project.

When you run tests, you will see a new window in PhpStorm that lists the tests. By default, tests that pass are not shown. You can show passed tests with the icon **Hide passed** in the top left of the test window.

### Testing a specific area

PHPUnit will run many tests. To restrict the testing area to just a folder (our module) and help the process run more quickly:

1.  Run: **Run > Edit Configurations**
2.  Click the **+** icon and select PHPUnit.
3.  Provide a name like `Drupal 8: Test Example`.
4.  Select **Test Runner > Test Scope > Directory**.
5.  Browse to the `test_example` module.
6.  Click **Save**.

<!-- @section -->

## Add a unit test

To create a unit test, you must create a new file in `tests/src/Unit/` that ends in `Test.php`. As with most Drupal 8 features, we create a test by creating a class that extends the base class `UnitTestCase`. We will also use the namespace `Drupal\Tests\[module_name]\Unit` for all of our test classes.

The `tests/src/Unit/TestExampleConversionsTest.php` file:

[Download this file](https://gist.github.com/acquialibrary/b6e3497867a0e8197f85/archive/950886c91c7dd70ce81c65118aa86bf999d329d0.zip).

```php
 <?php

 /**
 * @file
 *
 * Contains \Drupal\Tests\test_example\Unit\TestExampleConversionsTest.
 */

 namespace Drupal\Tests\test_example\Unit;

 use Drupal\Tests\UnitTestCase;

 /**
 * Demonstrates how to write tests.
 *
 * @group test_example
 */
 class TestExampleConversionsTest extends UnitTestCase {

 /**
 * @var \Drupal\test_example\TestExampleConversions
 */
 public $conversionService;

 public function setUp() {
 $this->conversionService = new \Drupal\test_example\TestExampleConversions();
 }

 /**
 * A simple test that tests our celsiusToFahrenheit() function.
 */
 public function testOneConversion() {
 // Confirm that 0C = 32F.
 $this->assertEquals(32, $this->conversionService->celsiusToFahrenheit(0));
 }

 }
```

[view raw](https://gist.github.com/acquialibrary/b6e3497867a0e8197f85/raw/950886c91c7dd70ce81c65118aa86bf999d329d0/TestExampleConversionsTest.php) [TestExampleConversionsTest.php](https://gist.github.com/acquialibrary/b6e3497867a0e8197f85#file-testexampleconversionstest-php) hosted with ❤ by [GitHub](https://github.com)

*   **Test Methods**

    In this test, we have created a single test and created a setUp() function. Each test is a method that starts with test.

*   **Assertions**

    PHPUnit will run each of these functions and they should contain statements that run assertions. Assertions are the tests results and determine if the test passes or fails. There are many assertion functions such as `assertTrue` and `assertFalse`. `assertEquals` checks that the two parameters are equal. The convention is to put the expected result as the first argument and the tested value as the second. In this example, we are testing that our service converts 0 Celsius to 32 Fahrenheit.

*   **setUp()**

    The `setUp` method will run once before every test. We use this behavior to create a class variable that is an instance of our service that we are testing. More complex tests may require more complex set-ups.

<!-- @section -->

## Run the unit test

You can run the unit test in PhpStorm using the **Run** button. Running the configuration that tests only the module will be faster.

<!-- @section -->

## Add a more complex unit test

Unit tests can become fairly complex. One simple way to reduce repetitive code is to use the setUp method. Another is to use a data provider.

### Data provider

A data provider in PHPUnit is a method that provides configuration for a testing method. This provider returns an array of test cases which contain an array of test arguments. In this example, we create a data provider `providerCentimetersToInches`. This provider returns a list of parameters.

```
/**
* Provides data for the testCentimetersToInches method.
*
* @return array
*/
public function providerCentimetersToInches() {
return [
[2.54,1],
[254,100],
[0,0],
[-2.54,-1],
];
}

/**
* Tests centimetersToInches method.
*
* @dataProvider providerCentimetersToInches
*/
public function testCentimetersToInches($length, $expectedValue) {
$this->assertEquals($expectedValue, $this->conversionService->centimeterToInch($length));
```

*   **Data Provider Annotation**

    To link the provider, we use the **@dataProvider** annotation on the test method.

*   **Data Provider Arguments**

    Now that we are using a data provider, we can add arguments to our test method. We are passing in an array of two values, so our test method should have two corresponding arguments.

<!-- @section -->

## Additional reading on unit tests

PHPUnit supports [many additional features](https://phpunit.de/manual/current/en/index.html). Doubles, mocks, and stubs are used in unit tests when your services rely on other classes. In this case, you create an empty version of this class and provide replacement implementations for methods that are needed. You can also list tests as dependent on other tests. Using the `@depends` will only run that test if the depended on tests pass.
