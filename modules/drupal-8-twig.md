<!--
{
"name" : "drupal-8-twig",
"version" : "0.0.1",
"title" : "Lesson 9.2 - Twig",
"description" : "TBD",
"freshnessDate" : 2015-12-11,
"homepage" : "https://docs.acquia.com/articles/drupal-8-twig",
"canonicalSource" : "https://docs.acquia.com/articles/drupal-8-twig",
"license" : "CC BY-SA"
}
-->

<!-- @section -->

# Overview

<!-- @section -->

## Add a Twig template

Now that we have the basic theme elements defined, we can create a template for [Twig](http://twig.sensiolabs.org/). Twig is the [default theme system for Drupal 8](https://www.drupal.org/theme-guide/8/twig).

By default, Twig template files have the name of the component with `-` (dash) instead of `_` (underscore), and use the extension `.html.twig`. This is very similar to Drupal 7, which used `.tpl.php` for template files.

### Our Twig template

First, an example template file, `templates/my-element.html.twig`. You can print the whole render element using code like `{{element}}` or you can print individual fields or child elements with `{{element.field}}`. `#random_number` is a variable and starts with a symbol, which means we must use the alternate way of referencing properties to render it properly.

[Download this file](https://gist.github.com/acquialibrary/3122d2205cdcd8ca8512/archive/54eab43dd109059e58b93a8d090716bec4cc87db.zip).

```
 {#
 /**
 * @file
 * Default theme implementation to my_element.
 *
 * Available variables:
 * - element: Element that will be rendered.
 * - element['link'] : a link
 * - element['description'] : a description
 * - element['pre_render_addition'] : added during the #pre_render function
 * - element['random_number'] : a random number variable, won't be printed with the element.
 *
 * @ingroup themeable
 */
 #}

 <div class="myElement">
 <div class="randomNumber">Random Number: {{ element['#random_number'] }}</div>
 <p>{{ element.description }}</p>
 {{ element.link }}
 </div>

 {# Debug output #}
 <div>
 <h3>We print the element</h3>
 {{ element }}
 <h3>Link</h3>
 {{ element.link }}
 <h3>Description</h3>
 {{ element.description }}
 <h3>#pre_render addition</h3>
 {{ element.pre_render_addition }}
 <h3>Random number (not printed when we printed the whole element</h3>
 {{ element['#random_number'] }}

 </div>
```
[view raw](https://gist.github.com/acquialibrary/3122d2205cdcd8ca8512/raw/54eab43dd109059e58b93a8d090716bec4cc87db/my-element.html.twig) [my-element.html.twig](https://gist.github.com/acquialibrary/3122d2205cdcd8ca8512#file-my-element-html-twig) hosted with ❤ by [GitHub](https://github.com)

### Variables in Twig

When referring to templates, [Twig's documentation states](http://twig.sensiolabs.org/doc/templates.html):

> "A template contains variables or expressions, which get replaced with values when the template is evaluated, and tags, which control the logic of the template."

Drupal passes variables to the template in the theme layer for manipulation in the template. The variables themselves may then have attributes or elements you can access, though these are not required.

<!-- @section -->

## Debugging Twig

There are two main ways of debugging Twig:

*   Enable Twig debugging which will help determine which templates are being used.
*   Add markup to the page that will allow us to explore the variables available in our template.

### Determining template suggestions

Enabling Twig debugging will add markup to the page which tells you information about what templates are being used. The method to debug Twig is briefly outlined on Drupal.org at [Debugging Twig templates](https://www.drupal.org/node/1906392), which uses a [web browser's native debugging tools](https://docs.acquia.com/articles/browser-developer-tools) to aid in finding issues.

In brief, you enable Twig debugging in `sites/default/services.yml` file with the following:

```
parameters:
twig.config:
debug: true
```

You then use developer tools with comments enabled to allow viewing information about the template. It may be difficult to find specific documentation on the best practices for using this method.

### Debugging Twig variables

Twig debugging is much easier than Drupal 7 methods. Twig supports a way to explore all of the variables available in a template as well as individual variables.

To do this, we need to install the [Devel](http://drupal.org/project/devel) and [Kint](https://www.drupal.org/sandbox/fat763/2155159) modules. We can then dump all the variables to the screen with a command like this:

```
{{ kint() }}
```

We can explore a single variable:

```
{# Or one variable. #}
{{ kint(element) }}

{# Or one part of the render array. #}
{{ kint(element.hello) }}
```

In this example, you can also see the Twig comment format `{# comment #}`.
