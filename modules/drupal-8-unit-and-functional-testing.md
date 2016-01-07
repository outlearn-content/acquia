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

## Initial setup

We are going to create a new module `test_example`.

#### test_example.info.yml

```yml
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

```yml
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

<!-- @task, "text" : "Prepare a module for testing as described above." -->
