<!--
{
"name" : "drupal-8-theme-lab",
"version" : "0.0.1",
"title" : "Lesson 9.6 - Labs and other information",
"description" : "Labs and other information",
"freshnessDate" : 2015-12-11,
"homepage" : "https://docs.acquia.com/articles/drupal-8-theme-lab",
"canonicalSource" : "https://docs.acquia.com/articles/drupal-8-theme-lab",
"license" : "CC BY-SA"
}
-->

<!-- @section -->

# Overview

<!-- @section -->

## Conclusion and Summary

Drupal has changed a lot over the years and in many of those cases, it has supported both old and new systems. Looking at the options at a high level is extremely confusing. There are great improvements to theming in Drupal 8, but it hasn't received the complete overhaul that much of the other systems has. Because of that, it's important to focus on the new and consistent patterns rather than all of the old alternatives.

<!-- @section -->

## Step by step instructions

1.  Create a new module `theme_example`.
2.  Copy the code in this lesson to `theme_example.info.yml`.
3.  Copy the code in this lesson to `theme_example.routing.yml`.
4.  Copy the code in this lesson to `src/Controller/ThemeExampleController.php`.
5.  Create a `src/Element/MyElement.php` plugin.
6.  Copy the code in this lesson to `theme_example.module`.
7.  Create a Twig template `templates/my-element.html.twig`
8.  Create the `theme_example.libraries.yml` and add the `sample_library`.
9.  Copy the code in this lesson to `theme_example/css/example.css`.
10.  Update our controller `src/Controller/ThemeExampleController.php`.
11.  Copy the code in this lesson to `theme_example/js/example.js`.
12.  View the example page `/examples/theme-example/simple`.
13.  Enable Twig debugging.
14.  Enable the Devel and Kint modules.
15.  Explore the generated page for debugging information.
16.  Modify the Twig template and add `kint()` debugging functions.

<!-- @section -->

## Additional exercises

*   Improve the look of the returned nodes by adding helpful HTML to the Twig file and css of your choice.
*   Define three additional variables to inject into the Twig template. Use the variables in the template file.
*   Modify a variable with at least one [filter](http://twig.sensiolabs.org/doc/filters/index.html) and one [function](http://twig.sensiolabs.org/doc/functions/index.html).
*   Use escaping to ignore the second variable that you set above.
