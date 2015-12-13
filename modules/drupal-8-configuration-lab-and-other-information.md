<!--
{
"name" : "drupal-8-configuration-lab-and-other-information",
"version" : "0.0.1",
"title" : "Lesson 3.3 - Labs and other information",
"description" : "TBD",
"freshnessDate" : 2015-12-11,
"homepage" : "https://docs.acquia.com/articles/drupal-8-configuration-lab-and-other-information",
"canonicalSource" : "https://docs.acquia.com/articles/drupal-8-configuration-lab-and-other-information",
"license" : "CC BY-SA"
}
-->

<!--@section-->

# Overview

<!-- @section -->

## Conclusion and summary

In this lesson we’ve begun to explore configurations and the configuration form which can be used to store values in the system. You are encouraged to further explore the `ConfigFormBase` and what options are available for extending it. Additionally, you should try different combinations for importing/exporting configuration between sites using the user interface.

Overall, you should now see how powerful configurations are in Drupal 8 and the amount of improvement to the deployment process that has occurred since Drupal 7.

We've learned about module-level implementation of CM.

*   How to use boiler plate code to reduce the amount of code writing necessary.
*   Configuration management provides a central place for modules to store configuration data rather then implementing a hodgepodge collection of functions (Drupal 7).
*   YAML files are used to implement a common standard for recording configuration data into code.
*   Configuration forms are a specific use form helpful for storing configuration data to the database and file system.
*   A config object is an instantiation of a configuration.

<!-- @section -->

## Step by step instructions:

1.  Switch to the root of your `Examples` directory.
2.  Create a new empty directory called `configform_example`.
3.  Switch to the `configform_example` directory
4.  Create the `configform_example.info.yml` file based on what you learned in Lesson 1.
5.  Cut and paste the sample code into `configform_example.info.yml` file
6.  Create the `configform_example.routing.yml` file.
7.  Cut and paste the sample code into `configform_example.routing.yml` file
8.  Create a new empty directory called `src`.
9.  Switch to the `src` directory
10.  Create a new empty directory called `Form`.
11.  Switch to the `Form` directory
12.  Create the `ConfigFormExampleConfigForm.php` file.
13.  Cut and paste the sample code into the `ConfigFormExampleConfigForm.php` file
14.  Switch back to the `configform_example` directory and create the configform_example.links.menu.yml file.
15.  Cut and paste the sample code into the `configform_example.links.menu.yml` file.
16.  Replace the `FormBase` method with `ConfigFormBase` in the `ConfigFormExampleConfigForm.php` file (start of new material).
17.  Create an empty `config/install` directory.
18.  Create the `configform_example.settings.yml` file in the `config/install` directory.
19.  Modify the `buildForm()` method.
20.  Modify the `getFormId()` method.
21.  Alter the submit handler.
22.  Enable the module.
23.  Rebuild the caches using `drush cr` or `drush8 cr`
24.  To test navigate to `http://mysite.com/examples/configform_example/simple`

You should see a Drupal page with the expected form on it that is configurable.

<!-- @section -->

## Additional questions

To further your understanding, you can explore these additional questions:

*   How did configuration work in Drupal 7?
*   Give three reasons why aggregating configuration functionality into one system is helpful.
*   Modify your sample code to save two values to the configuration system.
*   Read more about multilingual support with the use of schemas.

<!-- @section -->

## Useful links

*   [Drupal 8 Deep Dive: Plug-in System](https://www.acquia.com/resources/acquia-tv/conference/drupal-8-deep-dive-plug-system-august-21-2014) - Acquia webinar
*   [Unravelling the Drupal 8 Plugin System](https://drupalize.me/blog/201409/unravelling-drupal-8-plugin-system) - Drupalize.me tutorial
