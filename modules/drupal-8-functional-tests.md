<!--
{
"name" : "drupal-8-functional-tests",
"version" : "0.0.1",
"title" : "Lesson 10.3 - Functional tests",
"description" : "Functional tests",
"freshnessDate" : 2015-12-11,
"homepage" : "https://docs.acquia.com/articles/drupal-8-functional-tests",
"canonicalSource" : "https://docs.acquia.com/articles/drupal-8-functional-tests",
"license" : "CC BY-SA"
}
-->

<!-- @section -->

# Overview

Functional testing in Drupal 8 requires that you have a running site and install the [SimpleTest](https://www.drupal.org/project/simpletest) module. In this test, we are going to add a field to the user entity and then check that the field exists on users. We need to use functional testing because we need to bootstrap Drupal to get the user object. We may be able to create unit tests to do this, but we end up dealing with very complex systems.

<!-- @section -->

## PHPUnit and SimpleTest functional tests (BrowserTestBase / WebTestBase)

The standard and future default functional testing system uses PHPUnit and a base class `BrowserTestBase`. Much of core uses an old functional testing system called SimpleTest that uses `WebTestBase`. PHPUnit based tests can be run alongside unit tests from the IDE. SimpleTest tests are a bit more complex to run and we will cover those later.

The main limitation of `BrowserTestBase` currently is that it cannot use JavaScript and AJAX. If your tests require that, you will need to use the old `WebTestBase` until JavaScript support is added to `BrowserTestBase`.

### Add a user field

For the first test, we'll add a field to the user entity and then test that the field is available.

#### test_example.info.yml

```
name: Test Example
type: module
description: Example showing how to create tests
core: 8.x
package: Examples
dependencies:
- user
- options
```

#### config/install/field.field.user.user.test_status.yml

Use this file to define the field:

[Download this file](https://gist.github.com/acquialibrary/df98e2a88973be66fe42/archive/85fe9b4f43b32fee938a80b79de6d2969c581519.zip).

```
 langcode: en
 status: true
 dependencies:
 config:
 - field.storage.user.test_status
 id: user.user.test_status
 field_name: test_status
 entity_type: user
 bundle: user
 label: test_status
 description: ''
 required: false
 translatable: false
 default_value:
 -
 value: 0
 default_value_callback: ''
 settings:
 on_label: 'On'
 off_label: 'Off'

 field_type: boolean
```

[view raw](https://gist.github.com/acquialibrary/df98e2a88973be66fe42/raw/85fe9b4f43b32fee938a80b79de6d2969c581519/field.field.user.user.test_status.yml) [field.field.user.user.test_status.yml](https://gist.github.com/acquialibrary/df98e2a88973be66fe42#file-field-field-user-user-test_status-yml) hosted with ❤ by [GitHub](https://github.com)

config/install/field.storage.user.test_status.yml

```
langcode: en
status: true
dependencies:
module:
- user
id: user.test_status
field_name: test_status
entity_type: user
type: boolean
settings: { }
module: test_example
locked: false
cardinality: 1
translatable: true
indexes: { }
persist_with_no_fields: false
```

<!-- @task, "text" : "Add a user field as a preparation for functional testing." -->

### Add a functional test

To create a functional test, create a new file in `tests/src/Functional/` that ends in `test.php`. As with most Drupal 8 features, we create a test by creating a class that extends the base class `BrowserTestBase`. Use the namespace `Drupal\Tests\[module_name]\Functional` for all of test classes.

This pattern is VERY similar to unit tests, but uses `Functional` in the path and namespace instead of `Unit`.

#### src/Tests/TestExampleUserTest.php

[Download this file](https://gist.github.com/acquialibrary/592ef8fdc41a3ba21ee5/archive/520305274709233d8cc136cf971666500272022f.zip).

```php
 <?php
 /**
 * @file
 *
 * Contains \Drupal\Tests\test_example\Functional\TestExampleUserTest.
 */
 namespace Drupal\Tests\test_example\Functional;
 use Drupal\simpletest\BrowserTestBase;
 /**
 * Check if our user field works.
 *
 * @group test_example
 * @runTestsInSeparateProcesses
 * @preserveGlobalState disabled
 */
 class TestExampleUserTest extends BrowserTestBase {
 /**
 * @var \Drupal\user\Entity\User.
 */
 protected $user;
 /**
 * Enabled modules
 */
 public static $modules = ['test_example'];
 /**
 * {@inheritdoc}
 */
 function setUp() {
 parent::setUp();
 $this->user = $this->drupalCreateUser();
 }
 /**
 * Test that the user has a test_status field.
 */
 public function testUserHasTestStatusField() {
 $this->assertTrue(in_array('test_status', array_keys($this->user->getFieldDefinitions())));
 }

 }
```

[view raw](https://gist.github.com/acquialibrary/592ef8fdc41a3ba21ee5/raw/520305274709233d8cc136cf971666500272022f/TestExampleUserTest.php) [TestExampleUserTest.php](https://gist.github.com/acquialibrary/592ef8fdc41a3ba21ee5#file-testexampleusertest-php) hosted with ❤ by [GitHub](https://github.com)

*   **Annotations**

    All Functional tests using PhpUnit need to have two annotations, `@runTestsInSeparateProcesses` and `@preserveGlobalState` disabled.

*   **Enabled Modules**

    Since functional tests run inside the Drupal system that is not fully built, we need to make sure that any dependencies are listed. We will list our module here so that the test knows to enable it.

*   **setUp**

    In our test, it's necessary to call `parent::setUp` since the setup for these functional tests is quite complex. We can also call the special [`drupalCreateUser()`](https://api.drupal.org/api/drupal/core%21modules%21simpletest%21src%21BrowserTestBase.php/function/BrowserTestBase%3A%3AdrupalCreateUser/8) method which allows us to access the user entity. In our unit tests, we don't have access to fully formed user entities.

*   **Test Methods**

    Tests start with test as with unit tests.

*   **Assert statements**

    Assert statements are the same as PHPUnit unit tests. In this example, we are checking if a field exists on the user entity. The user entity has a `getFieldDefinitions()` method that we can use. This would be available for a unit test, but it would return null by default. We're testing that the field exists, so we don't want to mock this function, we want to know that it is actually going to return our field.

    ### Run the functional tests

    To run Functional tests, we need to tell PHPUnit where our site can be reached. This is done in PhpStorm by setting an environmental variable with the configuration setting.
    1.   Open PhpStorm.
    2.   In the **Command Line** section, edit the **Environment** variables.
    3.   Add a new item with the name `SIMPLETEST_BASE_URL` and value set to your local environment.

[![ConfigurePhpUnitBrowserTestBase.jpg](https://raw.githubusercontent.com/outlearn-content/acquia/master/assets/ConfigurePhpUnitBrowserTestBase.jpg)](https://raw.githubusercontent.com/outlearn-content/acquia/master/assets/ConfigurePhpUnitBrowserTestBase.jpg)

You can run the tests the same way you run unit tests, using the **Run** button.

<!-- @task, "text" : "Add a functional test as described above and run it." -->

<!-- @section -->

## AJAX legacy functional testing (SimpleTest)

As with PHPUnit functional tests, if you use AJAX forms, you will currently need to use the SimpleTest system until Javascript is added to BrowserTestBase. You will need to have a running site and install the [SimpleTest](http://drupal.org/project/simpletest) module.

### Differences between PHPUnit Functional Tests and SimpleTest Functional Tests

The two test types have some similarities, but enough differences that there can be confusion when you need to use both.

#### File Naming and Namespaces

To create a functional test, you must create a new file in src/Tests/ that ends in Test.php. As with most Drupal 8 features, we create a test by creating a class that extends the base class WebTestBase or KernelTestBase.

We will also use the namespace `Drupal\[module_name]\Tests` for all of our test classes. This pattern is VERY similar to PHPUnit tests, but is not exact. PHPUnit tests are located in `tests/src/Functional` and functional tests in `src/Tests`. Similarly, the namespaces are swapped around and they use different base classes.

#### Example Test File

An example SimpleTest, `src/Tests/TestExampleUserTest.php`:

[Download this file](https://gist.github.com/acquialibrary/dadee0ebc9f7aa45d59c/archive/e10124844530af6aea145c3beb6d72ea7f6e9ce5.zip).

```php

 <?php
 /**
 * @file
 *
 * Contains \Drupal\test_example\Tests\TestExampleUserTest.
 */

 namespace Drupal\test_example\Tests;

 use Drupal\simpletest\WebTestBase;

 /**
 * Check if our user field works.
 *
 * @group test_example
 */
 class TestExampleUserTest extends WebTestBase {

 /**
 * @var \Drupal\user\Entity\User.
 */
 protected $user;

 /**
 * Enabled modules
 */
 public static $modules = ['test_example'];

 /**
 * {@inheritdoc}
 */
 function setUp() {
 parent::setUp();

 $this->user = $this->drupalCreateUser();
 }

 /**
 * Test that the user has a test_status field.
 */
 public function testUserHasTestStatusField() {
 $this->assertTrue(in_array('test_status', array_keys($this->user->getFieldDefinitions())));
 }

 }
```

[view raw](https://gist.github.com/acquialibrary/dadee0ebc9f7aa45d59c/raw/e10124844530af6aea145c3beb6d72ea7f6e9ce5/TestExampleUserTest.php) [TestExampleUserTest.php](https://gist.github.com/acquialibrary/dadee0ebc9f7aa45d59c#file-testexampleusertest-php) hosted with ❤ by [GitHub](https://github.com)

*   **Assert statements**

    Assert statements are similar to PHPUnit tests, but the methods available are not identical. See [https://www.drupal.org/node/2469723](https://www.drupal.org/node/2469723).

*   **Running the functional tests**

    Functional tests can be run from the Drupal 8 website, or by using the command line. The command line usage looks something like this:

```
php core/scripts/run-tests.sh --verbose --url http://d8.dev/ --color test_example
```

You will want to use a --url option that matches your local setup. The --color option will color code the results which make them easier to skim. At the end of the command, we add our module name so that the tests are run only on that module.

<!-- @task, "text" : "Add a web test for something that can be only tested in a browser." -->

<!-- @section -->

## Additional goals for functional testing

There are many functional tests that exist in core Drupal. The test `core/modules/user/src/Tests/UserBlocksTest.php` contains the pattern we used in this lesson as well as one to check the output of forms.
