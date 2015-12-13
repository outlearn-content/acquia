<!--
{
"name" : "drupal-8-entities-lab",
"version" : "0.0.1",
"title" : "Lesson 4.2 - Labs and other information",
"description" : "TBD",
"freshnessDate" : 2015-12-11,
"homepage" : "https://docs.acquia.com/articles/drupal-8-entities-lab",
"canonicalSource" : "https://docs.acquia.com/articles/drupal-8-entities-lab",
"license" : "CC BY-SA"
}
-->

<!-- @section -->

# Overview

<!-- @section -->

## Conclusion and summary

Regardless of which of the three approaches you choose (manual, the user interface, or a tool like the Console project), creating an entity for reuse in Drupal 8 is an easy to reproduce process. Although content types are one of the first entities you'll probably create, you'll soon find yourself wanting to create others.

<!-- @section -->

## Step by step instructions

1.  Switch to the `Examples` root directory.
2.  Create a new, empty directory called `entity_example` with a nested directory structure.
3.  Creating the `entity_example.info.yml` to create the module within your [Drupal installation](https://docs.acquia.com/articles/install-definition).
4.  Create the `*.routing.yml` file to set up the routing
5.  Create the `*.links.menu.yml` file to set up the menu.
6.  Create the `*.links.action.yml` file to add routes to the page.
7.  Create the `*.links.task.yml` file to add tabs to the entity view page and the entity settings page.
8.  Create the entity classes.
9.  Create the `*Interface.php` file in the `/src` directory.
10.  Create the `*.php` file which resides in `src/Entity/*.php` to define the entity class.
11.  Create the controller files.
12.  Create the `*Form.php` found in `src/Entity/Form` to define adding and editing entity content.
13.  Define the form for deleting entity content with the `*Form.php` found in `src/Entity/Form`.
14.  Create the `src/Entity/Controller/ContactListBuilder.php` file which defines header and row content for the Contact listing.
15.  Define the form for managing fields for the entity with the `*SettingsForm.php` found in `src/Entity/Form`.

<!-- @section -->

## Additional exercises

*   Create an module to create a new custom entity using the Console module.
*   Define a new content type, with revisions enabled, that defaults to being promoted to the front page.
*   Create an entity manually.

<!-- @section -->

## Additional information

*   Introduction to [Content Types](https://www.drupal.org/node/21947) - drupal.org
*   [Include default configuration in your Drupal 8 module](https://www.drupal.org/node/2087879) - drupal.org
*   [Create a custom content and configuration entity](https://www.drupal.org/node/2166447) - drupal.org
