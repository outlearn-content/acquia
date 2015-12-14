<!--
{
"name" : "drupal-8-creating-elements-theming-elements-and-managing-frontend-libraries",
"version" : "0.0.1",
"title" : "Lesson 9.1 - Creating Elements, Theming Elements, and Managing Front-end Libraries",
"description" : "TBD",
"freshnessDate" : 2015-12-11,
"homepage" : "https://docs.acquia.com/articles/drupal-8-creating-elements-theming-elements-and-managing-frontend-libraries",
"canonicalSource" : "https://docs.acquia.com/articles/drupal-8-creating-elements-theming-elements-and-managing-frontend-libraries",
"license" : "CC BY-SA"
}
-->

<!-- @section -->

# Overview

<!-- @section -->

## In this lesson...

*   Assemble the module `examples/theme_example`.
*   Define an element.
*   Process an element through the render array flow.
*   Learn about Twig templates.
*   Learn about adding JavaScript and css to elements.
*   Learn about other process patterns used in Drupal 8.

This lesson demonstrates how to create a component of a webpage that's called an _element_, which contains markup and front-end assets that include JavaScript and CSS. We will show how to define this element in the back-end PHP system, and then follow this element as it is processed and rendered to the webpage with its JavaScript and CSS assets.

<!-- @section -->

## Definitions

### Elements

In Drupal, pieces of the webpage are organized as _elements_. These elements can often contain other elements. As such, we bundle all of these pieces is a large nested structure, modify the pieces, and then produce the final rendered HTML page or other output. Examples of elements are links, buttons, fields, images, nodes, blocks, pages. Everything in Drupal is some type of element.

### Render arrays

The PHP concept used to store information about elements is called a _render array_, which contains all of the information about a renderable element.

These render arrays contain information about both the element (such as its type) and variables that define the element's pieces. For example, a link has a type link and uses a title and URL to generate an HTML `<a>` element. Links can also take a lot of other optional variables to generate that markup such as classes and an id.

We have seen in previous lessons that controllers and other pieces of code produce render arrays as their output. These render arrays are often in their most basic form. The render array system is designed to take these very basic representations of elements and then process them through an extendable system until they are HTML markup.

During this processing, default values are added to the render array, other logic is performed by the piece of code that defined the element type and by modules and themes, and finally this render array is delivered to a template where the markup is generated.

We will be building a simple element and seeing how this render array process flow can be used to shape the element. All render arrays will contain one of the following:

*   `#markup`
*   `#type`
*   `#theme`

For most of this lesson, we will focus on `#markup` and `#type`.

### Twig templates

A _template_ is file that converts the render array into markup. Typically this template will contain the markup with replacement patterns.

```
<aside>
{{ element.aside }}
</aside>
```

[Twig](http://twig.sensiolabs.org/) is a PHP-based templating engine that is used in Drupal 8\. In previous versions of Drupal, we used PHP itself in template files to take render array components and create HTML markup. Previous versions of Drupal also occasionally used php functions to output the markup directly. In Drupal 8, the only recommended way to output markup is with a template.

The change to Twig makes it easier to create these templates and also allows us to more safely include basic logic in our template files.

In previous versions of Drupal, it was always advised not to write logic in template files for security and performance reasons. Twig is more limited and provides a structure that helps alleviate most of those concerns. The syntax is also very similar to other front-end systems, making it easy to pick up by front-end developers.

### JavaScript / Stylesheets / Front-end libraries

In Drupal 8, we bundle all JavaScript and css associated with an element into a front-end library and attach (using #attached) this library to the element's render array. This was the recommended way to handle assets in Drupal 7, but it wasn't always used. Drupal 7 also supported adding JavaScript and CSS on the fly.

Aside from a few small changes, the JavaScript and CSS files themselves are mostly the same as they were in previous versions of Drupal.

<!-- @section -->

## Define our element

For this lesson, we are going to create an element called **My Element** and it will contain a link, a description, and a random number. This element will also have associated CSS and JavaScript that we will load with the element.

```
<div class="myElement">
<div class="randomNumber">Random Number: 42</div>
<p>Here is a description</p>
<a href="http://www.drupal.org">Drupal.org</a>
</div>
```

To generate this element, we will be providing a description and a label for the link.

To display this element, we will create a page that includes our element.

<!-- @section -->

## Setting up our module

We are going to quickly flesh out our module and provide an example page that hosts the new element.

#### theme_example.info.yml

```
name: Theme Example
type: module
description: Example showing how to add a custom element and add JavaScript and CSS as a library
core: 8.x
package: Examples
```

#### theme_example.routing.yml

```
theme_example.simple:
path: 'examples/theme-example/simple'
defaults:
_controller: '\Drupal\theme_example\Controller\ThemeExampleController::simple'
requirements:
_access: 'TRUE'
```

#### src/Controller/ThemeExampleController.php

[Download this file](https://gist.github.com/acquialibrary/a0d02ba0529c5fddd9e7/archive/ce2e1d1ea45fe92780c65b56812e471936dd6146.zip).

```php
 <?php

 /**
 * @file
 * Contains \Drupal\theme_example\Controller\ThemeExampleController.
 */

 namespace Drupal\theme_example\Controller;

 use Drupal\Core\Controller\ControllerBase;

 /**
 * Controller routines for theme example routes.
 */
 class ThemeExampleController extends ControllerBase {

 public function simple() {
 return [
 'example one' => [
 '#markup' => '<div>Markup Example</div>',
 ],
 'example two' => [
 '#type' => 'my_element',
 '#label' => $this->t('Example Label'),
 '#description' => $this->t('This is the description text.'),
 ],
 ];
 }
 }
```

[view raw](https://gist.github.com/acquialibrary/a0d02ba0529c5fddd9e7/raw/ce2e1d1ea45fe92780c65b56812e471936dd6146/ThemeExampleController.php) [ThemeExampleController.php](https://gist.github.com/acquialibrary/a0d02ba0529c5fddd9e7#file-themeexamplecontroller-php) hosted with ❤ by [GitHub](https://github.com)

<!-- @section -->

## Creating the render arrays

In the controller (`ThemeExampleController.php`), we created three elements/render arrays. The first is a container that contains **example one** and **example two** which are both elements/render arrays. **example one** is the most basic type of render array. This simplest form of a render array contains one key `#markup` and an associated value which is the markup to print.

```
$output = [
'#markup' => '<div>Markup Example</div>',
];
```

 More complex render arrays like example two contain a set of variables that are prefixed with # and children elements.

 Our second element is the basic render array that will eventually become our element markup. It is defined by `#type my_element` and contains the **label** and **description** variables. It contains no child elements at this point. At this point, the render array does not contain the link or random number. Those will be added later.

 ```
 $output = [
'#type' => 'my_element',
'#label' => $this->t('Example Label'),
'#description' => $this->t('This is the description text.'),
];
```

<!-- @section -->

## Defining an element with a render element plugin

Now that we have seen the way the element might be included in a controller or other code, we need to define what our element is and how it is processed. We'll use the `#type` pattern which uses a render element plugin. As with other plugins, we create a new class in `src/Element/` that extends a base class `RenderElement`. This pattern allows us to store all of the information and logic related to our element in one file.

### Annotation

The annotations for this element will define the naming of it and allow the system to find it. We add annotation with `@RenderElement` and set it to our `#type`.

### getInfo()

The [`getInfo()`](https://api.drupal.org/api/drupal/8/search/getInfo) function defines how our element is structured. We provide the starting variables and defaults. We'll also add a `#theme` variable with the same name as the `#type`. This allows the system to pick up the element type. We will define this #theme in `hook_theme()` in our module later. This may seem redundant since we didn't have to do this for other plugins, but the system assumes that very basic elements will be defined only in `hook_theme()` and therefore we still have to use that system for all elements.

Most of this type of configuration has moved to YML files for similar systems, but the theming system in Drupal 8 is still a bit of a mix.

### #pre_render function

Our plugin contains a [`#pre_render`](https://api.drupal.org/api/drupal/8/search/pre_render) function to process the basic render array and create the necessary pieces for the template. In Drupal 7, preprocess functions handled this. Preprocess functions still exist and they make sense for modules and themes that don't define the element themselves. In our `pre_render` function we use the variables to create a render array with several children and a variable `#random_number`.

#### src/Element/MyElement.php

[Download this file](https://gist.github.com/acquialibrary/a4035855cbcf36ed0703/archive/998301b282eebf4e5dfa6e3a65a0c1cdbd9ed247.zip).

```php
 <?php
 /**
 * @file
 * Contains \Drupal\theme_example\Element\MyElement.
 */

 namespace Drupal\theme_example\Element;

 use Drupal\Core\Render\Element\RenderElement;
 use Drupal\Core\Url;

 /**
 * Provides an example element.
 *
 * @RenderElement("my_element")
 */
 class MyElement extends RenderElement {
 /**
 * {@inheritdoc}
 */
 public function getInfo() {
 $class = get_class($this);
 return [
 '#theme' => 'my_element',
 '#label' => 'Default Label',
 '#description' => 'Default Description',
 '#pre_render' => [
 [$class, 'preRenderMyElement'],
 ],
 ];
 }

 /**
 * Prepare the render array for the template.
 */
 public static function preRenderMyElement($element) {
 // Create a link render array using our #label.
 $element['link'] = [
 '#type' => 'link',
 '#title' => $element['#label'],
 '#url' => Url::fromUri('http://www.drupal.org'),
 ];

 // Create a description render array using #description.
 $element['description'] = [
 '#markup' => $element['#description']
 ];
 $element['pre_render_addition'] = [
 '#markup' => 'Additional text.'
 ];

 // Create a variable.
 $element['#random_number'] = rand(0,100);
 return $element;
 }
 }
```

[view raw](https://gist.github.com/acquialibrary/a4035855cbcf36ed0703/raw/998301b282eebf4e5dfa6e3a65a0c1cdbd9ed247/MyElement.php) [MyElement.php](https://gist.github.com/acquialibrary/a4035855cbcf36ed0703#file-myelement-php) hosted with ❤ by [GitHub](https://github.com)

### hook_theme()

In [`hook_theme`](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Render%21theme.api.php/function/hook_theme/8), we redefine our element by specifying that it should use the render element pattern and naming the element `element`. This provides a Twig variable element. The most widely used is the render element pattern and the Twig template. In previous versions of Drupal, most element information was defined in `hook_theme()`, but in Drupal 8, that logic is now in the plugin.

#### theme_example.module

```php
<?php

/**
* Implements hook_theme().
*/
function theme_example_theme() {
$items = [
'my_element' => [
'render element' => 'element',
],
];
return $items;
}
```
