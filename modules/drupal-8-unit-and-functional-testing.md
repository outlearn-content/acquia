<!--
{
"name" : "drupal-8-unit-and-functional-testing",
"version" : "0.0.1",
"title" : "Lesson 10.1 - Unit and Functional Testing",
"description" : "Unit and Functional Testing",
"freshnessDate" : 2015-12-11,
"homepage" : "https://docs.acquia.com/articles/drupal-8-unit-and-functional-testing",
"canonicalSource" : "https://docs.acquia.com/articles/drupal-8-unit-and-functional-testing",
"license" : "CC BY-SA"
}
-->

<!-- @section -->

# Overview

Testing in Drupal 8 has been broken into two primary types: unit testing and functional testing. Testing is used in a variety of ways. Some tests are written before development begins and helps developers build towards desired functionality. Other tests are written to help identify regression issues. The goal of this lesson is to introduce these two types of testing, but not to explain when to write these tests.

The key difference between unit and functional testing is the scope of the tests. Unit tests aim to test a small amount of functionality. In Drupal 8, custom services are a good use case for unit tests. This is because services are designed to be decoupled from the functionality that uses the services. These services do not depend on a running Drupal website or on live configuration and data.

Functional testing to test functionality that integrates many components, such at and end-to-end use of a feature. For example, we may want to perform one change in the system and then ensure that another system reacts to this change. In order to accomplish this, we need an actual Drupal website. You can do a larger range of things with functional tests, but they are generally slower.

<!-- @section -->

## In this lesson...

*   Write unit tests.
*   Execute unit tests.
*   Write functional tests.
*   Execute functional tests.

<!-- @section -->

## Initial setup

We are going to create a new module `test_example`.

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

<!-- @section -->

## Create a Simple Service

Services are good examples of things that we can test. Let's make a simple one that coverts between some measurements.

#### test_example.services.yml

```
services:
test_example.conversions:
class: Drupal\pants\TestExampleConversions
```


And `src/TestExampleConversions.php`:

[Download this file](https://gist.github.com/acquialibrary/741fbddc865fc8ea335f/archive/d0e967898d950cdd8e5c202e6a20f3efb75a8bc6.zip).

```php
 <?php
 /**
 * @file
 * Contains \Drupal\test_example\TestExampleConversions.
 */

 namespace Drupal\test_example;

 /**
 * Provide functions for converting measurements.
 *
 * @package Drupal\test_example
 */
 class TestExampleConversions {

 /**
 * Convert Celsius to Fahrenheit
 *
 * @param $temp
 *
 * @return int
 */
 public function celsiusToFahrenheit($temp) {
 return ($temp * (9/5)) + 32;
 }

 /**
 * Convert centimeter to inches.
 *
 * @param $length
 *
 * @return int
 */
 public function centimeterToInch($length) {
 return $length / 2.54;
 }

 }
```

[view raw](https://gist.github.com/acquialibrary/741fbddc865fc8ea335f/raw/d0e967898d950cdd8e5c202e6a20f3efb75a8bc6/TestExampleConversions.php) [TestExampleConversions.php](https://gist.github.com/acquialibrary/741fbddc865fc8ea335f#file-testexampleconversions-php) hosted with ❤ by [GitHub](https://github.com)
