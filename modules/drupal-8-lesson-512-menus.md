<!--
{
"name" : "drupal-8-lesson-512-menus",
"version" : "0.0.1",
"title" : "Lesson 1.2 - Menus",
"description" : "Menus",
"freshnessDate" : 2015-12-11,
"homepage" : "https://docs.acquia.com/articles/drupal-8-lesson-512-menus",
"canonicalSource" : "https://docs.acquia.com/articles/drupal-8-lesson-512-menus",
"license" : "CC BY-SA"
}
-->

<!-- @section -->

# Overview

<!-- @section -->

## What happened to hook_menu() and its callbacks?

In Drupal 8, the `hook_menu()` system has been split into separate YAML files:

| YAML file | Description |
|------------|-------------|
| `modulename.routing.yml` | Contains the association between URL paths and callbacks, similar to how array keys and "page callback" worked |
| `modulename.links.menu.yml` | Contains the structure of actual menu items that appear in blocks and navigation |
| `modulename.links.action.yml` | Contains the equivalent of Drupal 7's `MENU_LOCAL_ACTION` constant |
| `modulename.links.task.yml` | Contains the structure of Drupal 7's `MENU_DEFAULT_LOCAL_TASK` constant |

In Drupal 8, we use the [Symfony2 components](https://drupal.org/node/1635626) to handle the routing of paths to Controller methods that display page data.

1.  In the same directory (`page_example`), create the file `page_example.routing.yml`.
2.  Add the following code to the file:

```
    page_example_description:
    path: '/examples/page_example'
    defaults:
    _controller: '\Drupal\page_example\Controller\PageExampleController::description'
    requirements:
    _access: 'TRUE'

    page_example_simple:
    path: '/examples/page_example/simple'
    defaults:
    _controller: '\Drupal\page_example\Controller\PageExampleController::simple'
    requirements:
    _permission: 'access simple page'
```

Anatomy of the `page_example.routing.yml` file definitions of the route named `page_example_description` :

*   The `page_example_description:` marks the beginning of a new route for the Page example module.
*   The `path` specifies the path we want this route to register, and is the same as the array key in the standard Drupal 7 `$items[]` array in `hook_menu()`. This needs a leading slash as stated in the Drupal documentation for the [structure of routes](https://www.drupal.org/node/2092643).
*   The attribute `_controller` defines the route's path to a method on the `PageExampleController` class. It's interesting to note that [`_content` changed to `_controller`](https://www.drupal.org/node/2376791), bringing Drupal 8 more fully into sync with Symfony.
*   The `requirements` specify the permission the accessing user needs to have to be able to view the page.

If you want to leverage additional routing options, see the [Structure of routes](https://drupal.org/node/2092643) page on drupal.org.

### Building the Controller.php

The Page example controller called `PageExampleController` will have a method named `description()` which is called when a user requests this page.

> **Note**:
The placement of the Controllers and other classes, into the `src/` directory is part of the adoption of the PSR-4 standard. This applies to modules. For other portions of Drupal we utilize a longer file path which leverages the PSR-0 standard. If you see code placed in a directory called `lib/`, that’s PSR-0 and is an artifact of the pre-beta evolution of Drupal 8 and should be moved to the `src/` path instead for contributed and custom modules.

1.  Inside the module directory, create a path called `src/Controller`. This will house the controller classes.
2.  Create the controller file `PageExampleController.php`.
3.  We declare our class named `PageExampleController` inside the `PageExampleController.php` file similar to, though simpler than, the full `page_example module`:

```php
    <?php

    /**
    * @file
    * Contains \Drupal\page_example\Controller\PageExampleController.
    */

    namespace Drupal\page_example\Controller;

    use Drupal\Core\Url;

    /**
    * Controller routines for page example routes.
    */
    class PageExampleController {
    /**
    * Constructs a page with descriptive content.
    *
    * Our router maps this method to the path 'examples/page_example'.
    */
    public function description() {
    $simple_url = Url::fromRoute('page_example_simple');
    $simple_link = \Drupal::l(t('simple page'), $simple_url);

    $arguments_url = Url::fromRoute('page_example_description', [], ['absolute' => TRUE]);
    $arguments_link = \Drupal::l(t('arguments page'), $arguments_url);

    $build = [
    '#markup' => t(
    '<p>The Page example module provides two pages, "simple" and "arguments".</p>'
    . '<p>The !simple_link just returns a renderable array for display.</p>'
    . '<p>The !arguments_link takes two arguments and displays them, as in @arguments_url</p>',
    [
    '!simple_link' => $simple_link,
    '!arguments_link' => $arguments_link,
    '@arguments_url' => $arguments_url->toString()
    ]
    ),
    ];

    return $build;
    }

    /**
    * Constructs a simple page.
    *
    * The router _controller callback, maps the path 'examples/page_example/simple'
    * to this method.
    *
    * _controller callbacks return a renderable array for the content area of the
    * page. The theme system will later render and surround the content with the
    * appropriate blocks, navigation, and styling.
    */
    public function simple() {
    return [
    '#markup' => '<p>' . t('Simple page: The quick brown fox jumps over the lazy dog.') . '</p>',
    ];
    }

    }
```

Inside the `PageExampleController` class, we have two functions:

*   `description()` method - Returns a renderable array similar to what you would expect to see in a Drupal 7 module and which leverages the `URL::fromRoute` method.
*   `simple()` function - Contains the markup to be returned as HTML.

For all practical purposes this is the least we need to write in the module in order to get text to display at the target URL. This equates to the age old "Hello world" scenario where the goal is to get the response to running the program of "Hello World!". In this code the first critical line we wrote (line 8) is used to define the class namespace which allows for the classes reuse elsewhere in code. After the namespace has been declared, it's then possible to declare the class on line 15.


> **Namespaces in PHP**
> "Although any valid PHP code can be contained in a namespace, only the following types of code are affected by namespaces: classes (including abstracts and traits), interfaces, functions and constants. Namespaces are declared using the namespace keyword. A file containing a namespace must declare the namespace at the top of the file before any other code - with one exception: the declare keyword." PHP Net, [Defining Namespaces](http://php.net/manual/en/language.namespaces.definition.php)


Following the namespace declaration we then implement the use operator to allow us to reuse a method created as part of the URL class which is part of Drupal 8 core.

> **Using the use operator**
> "The ability to refer to an external fully qualified name with an alias, or importing, is an important feature of namespaces. This is similar to the ability of unix-based filesystems to create symbolic links to a file or to a directory... In PHP, aliasing is accomplished with the use operator." PHP Net, [Using namespaces: Aliasing/Importing](http://php.net/manual/en/language.namespaces.importing.php)

### How do I know the path for the namespace and use operator?

Essentially, the path to the location of your class file is used to define the namespace so that it can later be used an an alias using the use operator and then have any methods in your class file referenced similar to the following:

`namespace Drupal\page_example\Controller;`

Is then used in another class by saying:

`use Drupal\page_example\Controller`

And then in that new class you can use the simple() method in a newly created function:

`PageExampleController::simple`

<!-- @section -->

## What about menu links?

In Drupal 7, when we implement `hook_menu()`, we can also add the registered paths to menus in order to have menu links showing up on the site. This is again no longer handled with this hook but we use a yml file to declare the menu links as configuration.

### Building the links

Now we'll create a menu link that shows up under the **Reports** menu of the Drupal administration UI. First, we need to create a file called `page_example.links.menu.yml` in the root of our module. Inside this YAML file we will define menu links and their position in existing menus on the site.

1.  In the module directory, create the file `page_example.links.menu.yml`
2.  Add the following code to the file:

```
  page_example.description:
    title: 'Page Example'
    route_name: page_example_description
    parent: system.admin_reports
  page_example.simple:
    title: 'Simple - no arguments'
    route_name: page_example_simple
    parent: system.admin_reports
```

Again we have a YAML structure based on indentation in which we first define the machine name of the menu link (demo) for the module demo (like we did with the routing). Next, we have the link title and description followed by the parent of this link (where it should be placed) and what route it should use.

The value of parent is the parent menu link (appended by its module) and to find it you need to do a bit of digging in `*.links.menu.yml` files. The Structure link is defined in the core System module so by looking into the `system.links.menu.yml` file I could determine the name of this link.

The `route_name` is the machine name of the route we want to use for this link. We defined ours earlier. And with this in place, you can clear the cache and navigate to `http://example.com/admin/reports` where you should now see a brand new menu link with the right title and description and that links to the `examples/page_example/` path. Not bad.

### Clear the caches

To rebuild a Drupal 8 site and clear all its caches from the command line use the latest available version of Drush on the platform: `drush cr`
