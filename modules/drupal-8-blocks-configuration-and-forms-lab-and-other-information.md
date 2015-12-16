<!--
{
"name" : "drupal-8-blocks-configuration-and-forms-lab-and-other-information",
"version" : "0.0.1",
"title" : "Lesson 2.3 - Labs and other information",
"description" : "Labs and other information",
"freshnessDate" : 2015-12-11,
"homepage" : "https://docs.acquia.com/articles/drupal-8-blocks-configuration-and-forms-lab-and-other-information",
"canonicalSource" : "https://docs.acquia.com/articles/drupal-8-blocks-configuration-and-forms-lab-and-other-information",
"license" : "CC BY-SA"
}
-->

<!-- @section -->

# Overview

<!-- @section -->

## Conclusion and Summary

In this lesson we’ve continued to explore Drupal 8 module development with two new topics: blocks and forms. You know how to create a new block type so that new blocks may be generated via the UI. You've learned how to create a simple implementation of the FormBase class to print out to the screen the value submitted by the user. The concept of inheritance in Drupal has been covered:

*   In Drupal 8 blocks are now re-useable plugins.
*   Class files begin with a definition of the namespace.
*   The file then includes the other classes from either Core or other sources.
*   The comments contain annotations which allow the auto-discovery of metadata about the class and this is not a normally expected behavior for a comment but is how the standard works.

<!-- @section -->

## Complete lab instructions

1.  Switch to the root directory of your Examples module from Lesson 5.1.
2.  Create a new module directory called `block_example` within the project directory `examples` for your current attempt at creating a block.
3.  Switch to the new, empty `block_example` directory.
4.  Create a new `block_example.info.yml` file.
5.  Cut and paste the ample code for `*.info.yml` files from Lesson 5.1 into the `block_example.info.yml` file.
6.  Modify the code from Lesson 1 as appropriate for a new module called `block_example` which resides within the examples project.
7.  Create the path and directory `src/Plugin/Block`.
8.  Switch to the directory `src/Plugin/Block`.
9.  Create a new `ExampleEmptyBlock.php` file.
10.  Cut and paste the sample code from Lesson 2 into the `ExampleEmptyBlock.php` file.
11.  Enable the module.
12.  Clear the caches using `drush cr` or `drush7 cr`.
13.  To test, navigate to `http://mysite.com/admin/structure/block` and you should see the new block type and have the ability to add it.
14.  Add the block to a region and examine a node where you expect to be able to see the block.
15.  You may see nothing when you enable the empty block in a region. There is presently a core bug here: [`ExampleEmptyBlock` is broken](https://www.drupal.org/node/2369443).
16.  Create the `ExampleConfigurableTextBlock.php` file.
17.  Cut and paste the sample code into `ExampleConfigurableTextBlock.php` file.
18.  Move the file into your `block_example` Block directory.
19.  Clear the caches using `drush cr` or `drush8 cr`.
20.  To test, navigate to `http://mysite.com/admin/structure/block` and you should see the new block type and have the ability to add it.
21.  Test the configuration form.
22.  Switch to the `page_example` directory.
23.  Create the path and directory `src/Form`.
24.  Switch to the new, empty `src/Form/` directory.
25.  Create `PageExampleForm.php` file.
26.  Cut and paste the sample code into `PageExampleForm.php` file.
27.  Edit `page_example.routing.yml` in the `page_example` module.
28.  Clear the caches using `drush cr`.
29.  To test, navigate to `http://mysite.com/examples/page_example/form`.

<!-- @section -->

## Additional activities

These additional exercises can expand your knowledge and understanding of the concepts in these lessons.

*   Manually recreate the pieces of the module used to add a descriptive page for the `block_example` such as the Controller, YAML files, and Plugin to create what the true `block_example` module possesses:

_Examples module_ - List of files for `block_example`

```
drwxr-xr-x 5 username staff 170 Aug 28 12:46 src
-rw-r--r-- 1 username staff 193 Aug 28 12:46 block_example.routing.yml
-rw-r--r-- 1 username staff 1977 Aug 28 12:46 block_example.module
-rw-r--r-- 1 username staff 90 Aug 28 12:46 block_example.links.menu.yml
-rw-r--r-- 1 username staff 171 Aug 28 12:46 block_example.info.yml
```

_Examples module_ - List of files for `src/Plugin/Block`

```
-rw-r--r-- 1 username staff 990 Aug 28 12:46 ExampleUppercaseBlock.php
-rw-r--r-- 1 username staff 615 Aug 28 12:46 ExampleEmptyBlock.php
-rw-r--r-- 1 username staff 1894 Aug 28 12:46 ExampleConfigurableTextBlock.php
```

We don't use the `ExampleUppercaseBlock.php` file but we do create both the `ExampleEmptyBlock` and `ExampleConfigurableTextBlock`.

*   Create a block which displays a rotating banner of images.
*   Create a form block or page which can send an e-mail to an address provided in the form.
*   Create your own Entity that utilizes Block Plugins.
*   Research why some developers push back against the use of annotations.

<!-- @section -->

## Other useful links

*   [Form API Reference](https://api.drupal.org/api/drupal/developer%21topics%21forms_api_reference.html/8)
*   [Drupal 8 Deep Dive: Plug-in System](https://www.acquia.com/resources/acquia-tv/conference/drupal-8-deep-dive-plug-system-august-21-2014)
*   [Drupal 8: PHP Annotations](https://api.drupal.org/api/drupal/core%21vendor%21doctrine%21annotations%21lib%21Doctrine%21Common%21Annotations%21Annotation.php/class/Annotation/8)
*   [Unravelling the Drupal 8 Plugin System](https://drupalize.me/blog/201409/unravelling-drupal-8-plugin-system)
*   [An Overview of the Drupal 8 Plugin System (DrupalCon Amsterdam 2014)](https://amsterdam2014.drupal.org/session/overview-drupal-8-plugin-system)
*   [Preparing for Drupal 8 Development - Blocks, Forms, and Pages](http://www.mediacurrent.com/blog/preparing-drupal-8-development-blocks-forms-and-pages)
*   [Dynamically define blocks in Drupal 8 with derivatives](http://blog.dev030.com/posts/dynamically-define-blocks-drupal-8-derivatives)
*   [A Look Inside Drupal 8's Block Plugin API](http://drupalize.me/blog/201404/look-inside-drupal-8s-block-plugin-api)
*   [The Block System is Finally Useful in Drupal 8](http://drupalize.me/blog/201403/block-system-finally-useful-drupal-8)
*   [SCOTCH initiative homepage](https://groups.drupal.org/scotch)
*   [Blocks & Layouts (SCOTCH) Post-Feature Freeze Overview](https://groups.drupal.org/node/287563)
*   [Block API in Drupal 8](https://drupal.org/node/2168137)

The next tutorial examines configuration forms and it explores setting values submitted by a user using the Drupal 8 configuration system. Additionally, we will look at the service container and dependency injection and how those work in Drupal 8.
