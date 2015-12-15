<!--
{
"name" : "intro",
"version" : "0.0.1",
"title" : "Introduction",
"description" : "What you need to get started with Drupal 8.",
"freshnessDate" : 2015-12-11,
"homepage" : "https://docs.acquia.com/articles/building-drupal-8-modules",
"canonicalSource" : "https://docs.acquia.com/articles/building-drupal-8-modules",
"license" : "CC BY-SA"
}
-->

<!-- @section -->

# Building modules with Drupal 8


When attempting to determine your skill at programming, it can be difficult to identify which specific issue to identify with. Keep in mind that these lessons are simply an attempt to introduce you to D8 module building.[Lesson 1](https://docs.acquia.com/articles/examples-module-symfony-controllers-and-menu) attempts to teach to the middle, so module creation beginners should refer to the hints and notes for help. Advanced readers should view the hints and notes as optional and focus more on the extra credit. It is perfectly reasonable to gloss over the reading and to attempt to follow the lab goals and actions immediately and use the readings above it solely for reference.

If you're just getting started with PHP and object-oriented programming (OOP), Code Academy has a starter class at [http://www.codecademy.com/en/tracks/php](http://www.codecademy.com/en/tracks/php).

Review the following items with which you should be familiar before you proceed with these lessons:

*   Basic understanding of programming in PHP
*   Basic familiarity with OOP terminology and their meaning
*   Basic experience in using PHPStorm
*   Read and confirm your comprehension of both the [code organization](https://docs.acquia.com/articles/building-drupal-8-modules#code) and the [important terms](https://docs.acquia.com/articles/building-drupal-8-modules#terms) sections on this page

<!-- @section -->

## Code organization

You will be using path naming patterns based on the following example:

*   project/module_name/*

Future lessons will use the following paths:

*   `examples/page_example/*`
*   `examples/block_example/*`
*   `examples/form_example/*`
*   `examples/field_example/*`
*   `examples/entity_example/*`
*   `examples/theme_example/*`

Files and directories in those paths will appear similar to the following:

*   `src/*`
*   `src/Form/FormExampleForm.php`
*   `templates/*`
*   `images/*`
*   `config/*`

<!-- @section -->

## Important terms

This section describes the Drupal and general terms with which you should be familiar.

### Drupal terms

For important Drupal terms, see the [Drupal term index](https://docs.acquia.com/articles/drupal-glossary).

### General programming terms

| Term | Definition |
| -------| -------- |
| **Class** | An extensible program-code-template for creating objects, providing initial values for state (member variables) and implementations of behavior (member functions or methods) —_object-oriented programming definition_ |
| **Comment** | A programming language construct used to embed programmer-readable annotations in the source code of a computer program |
| **Function** | A sequence of program instructions that perform a specific task, packaged as a unit, which can then be used in programs wherever that particular task should be performed |
| **Method** | A function used in the context of a class/object — also known as a member function |
| **Namespace** | Provides a method to group related classes, interfaces, functions, and constants, which can solve two problems that authors of libraries and applications encounter when creating re-usable code elements such as classes or functions:
|| * Name collisions between code that you create, and internal PHP classes/functions/constants or third-party classes/functions/constants |
|| * Ability to alias (or shorten) Extra_Long_Names designed to alleviate the first problem, improving readability of source code |
| **Object** | A variable, function, or data structure that refers to a particular instance of a class where the object can be a combination of variables, functions, and data structures — comprised of variables (such as properties, attributes, or fields) and functions (methods), and allows the bundling of functions and data in one structure — create using classes/object constructors_object-oriented programming definition_ |
| **Object-oriented programming (OOP)** | Provides a model for programming based on objects, integrating code and data using the concept of an _object_ |
| **Variable** | A symbolic name associated with a value and whose associated value may be changed |

<!-- @section -->

## Code example

```php
<?php
// This is a comment

/* This is a comment that
spans multiple lines */

// This is a variable
$age = '33';

// This is a function
function displayAge() {
return $age;
}

// This is a class
class personClass {
// This is a property variable
public $age = '33';

// This is a method
public function displayAge() {
return $this->age;
}
}

// This is an instantiated class
$age = new personClass();

// This is calling the 'displayAge' method of the class
echo $age->displayAge();

?>
```

<!-- @section -->

## Summary

If you're not familiar with most of the previous terms, you should consider reviewing the [Code Academy class](http://www.codecademy.com/en/tracks/php). It can hopefully provide additional context and experience for the remainder of this lesson. With time and practice, through the iterations provided, it should be possible to improve your understand of Drupal 8 using module migration. Some concepts can take many tries (such as dependency injection and service containers) while others (such as YAML files) might make sense the first time that you encounter them.
