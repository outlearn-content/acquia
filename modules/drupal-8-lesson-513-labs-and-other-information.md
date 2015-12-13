<!--
{
"name" : "drupal-8-lesson-513-labs-and-other-information",
"version" : "0.0.1",
"title" : "Lesson 1.3 - Labs and other information",
"description" : "TBD",
"freshnessDate" : 2015-12-11,
"homepage" : "https://docs.acquia.com/articles/drupal-8-lesson-513-labs-and-other-information",
"canonicalSource" : "https://docs.acquia.com/articles/drupal-8-lesson-513-labs-and-other-information",
"license" : "CC BY-SA"
}
-->

<!-- @section -->

# Overview

<!-- @section -->

## Conclusion and Summary

This lesson explored module development in Drupal 8, learning how to work with the new APIs and porting contributed modules.

For starters, we looked at some basics: how you start a Drupal 8 module (including files and directory structure), all compared with Drupal 7\. We’ve also seen how to define routes and a Controller class with a method to be called by this route. And finally, we’ve seen how to create a menu link that uses the route we defined.

In the next lesson, we will continue exploring the examples module and continue to dig deeper into how Drupal 8 works.

You should now understand:

*   Drupal 8 implements object oriented programming for the benefits it provides.
*   Symfony is a framework that helps Drupal address repeatable needs.
*   You should understand what a YAML file is and how to implement one.
*   The `modulename.info` file is replaced by the `modulename.info.yml` file.
*   The `hook_menu()` system has had its functionality split between the `modulename.routing.yml` and the `modulename.links.menu.yml` files.
*   Menu links are also implemented using a YAML configuration file.
*   Classes can be declared in the `Controller.php` file.

<!-- @section -->

## Exploring further

If you want to continue exploring this lesson, try:

1.  Modify the page example so that is includes both a use of `hook_help` and `modulename.permissions.yml` (temporary link to example until API page exists).
2.  Figure out how to place your menu items under a new menu using the Providing module-defined menu links documentation.
3.  Complete the Symfony walkthrough.

You should be able to answer these questions:

*   What is different about Drupal 8 module development?
*   Why is Symfony now a part of Drupal?
*   What replaces a `page callback` function used in Drupal 7's `hook_menu()` function?
*   What is autoloading?

<!-- @section -->

## Complete lab instructions

1.  Move to the `root/modules` directory of your Drupal 8 installation.
2.  Create a new, empty module directory called `examples` for creating from scratch pieces of the Examples module.
3.  Switch directories to the new, empty Examples module.
4.  Create a new directory called `page_example` for this lesson's exploration of how to create a page with a module in Drupal 8.
5.  Switch directories to be in the new, empty `page_example` directory.
6.  Create the `page_example.info.yml` file.
7.  Cut and paste the sample code into the `page_example.info.yml` file.
8.  Create the `page_example.routing.yml` file.
9.  Cut and paste the sample code into the `page_example.routing.yml` file.
10.  Create the `PageExampleController.php` file.
11.  Cut and paste the sample code into the `PageExampleController.php` file.
12.  Create the `page_example.links.menu.yml` file.
13.  Cut and paste the sample code into the`page_example.links.menu.yml` file.
14.  Move the entire module into place in your Drupal 8 installation (`modules/custom`) directory.
15.  Enable the module.
16.  Rebuild the caches (also known historically and in some places in core as clearing the caches) using `drush cr` if you have setup your alias for drush or `drush8 cr` if you haven't.
17.  To test, navigate to `http://mysite.com/examples/page_example/simple`, and and you should then see a Drupal page with the expected text printed on it.
