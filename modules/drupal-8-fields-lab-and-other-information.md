<!--
{
"name" : "drupal-8-fields-lab-and-other-information",
"version" : "0.0.1",
"title" : "Lesson 5.3 - Labs and other information",
"description" : "TBD",
"freshnessDate" : 2015-12-11,
"homepage" : "https://docs.acquia.com/articles/drupal-8-fields-lab-and-other-information",
"canonicalSource" : "https://docs.acquia.com/articles/drupal-8-fields-lab-and-other-information",
"license" : "CC BY-SA"
}
-->

<!-- @section -->

# Overview

<!-- @section -->

## New field types

Drupal 8 ships with several new modules to extend the default Fields API possibilities.

*   **Datetime** - Doesn't include the recurring date functionality from the Date field module — you’ll need to find a contrib module for that.
*   **Email** - Ships with both address validation and an email field formatter (transforms the plain text email address into a clickable mailto).
*   **Entity reference** - Links to users, terms, and content, with an auto-complete field.
*   **Link** - Includes basic validation (on http:// only) and several common options (such as nofollow and open in new window), and different link formatting possibilities.
*   **Telephone** - A basic field which doesn't do any sort of validation, such as the popular [Phone](https://www.drupal.org/project/phone) module. However, it does come with a Telephone link field formatter (HTML 5 `tel` element) which will allow mobile devices to determine this is a telephone link, and then offer to call this number or access the phone dialer. There's also a straight [backport to Drupal 7](https://drupal.org/project/telephone).

HTML5 fields also support the [schema.org](http://schema.org/) markup. There is no user interface in core, but you can use the [RDF UI](https://www.drupal.org/project/rdfui) module to map your fields to schema.org.

<!-- @section -->

## Step by step instructions

1.  Using the Drupal 8 user interface, create a new content type called **External**.
2.  Enable the Link module.
3.  Add the fields **link** and **_image field_** to the new content type.
4.  Export the entire configuration for the site as an archive or export each file separately using the **Single Export** option.
5.  Create the empty module, called **External Link Content Type**.
6.  Create a subdirectory called `config`, and navigate to that directory.
7.  Create a directory called `install`.
8.  Copy the `node.type.external.yml` file from the configuration export to `config/install/`.
9.  Create a new empty file named `external.info.yml`.
10.  Remove the existing **External** content type that you created in step 1.
11.  Enable the newly created External Link Content Type module.
12.  Confirm that the new content is being loaded.
13.  Uninstall the External Link Content Type module.
14.  Add the field configuration files.
15.  Re-enable the module.
16.  Test to confirm that both the new content type and fields have been correctly added.

<!-- @section -->

## Additional questions and exercises

*   Export configurations for a new field using the [Configuration development](https://www.drupal.org/project/config_devel) module.
*   Explain why the module requires a `field.storage.node.field_url.yml` file, when the body and image fields do not.
*   Using the Examples modules as a reference, modify the module code to prevent deletion of the External content type while the module is installed.
