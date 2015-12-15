<!--
{
"name" : "examples-module-symfony-controllers-and-menu",
"version" : "0.0.1",
"title" : "Lesson 1.1 - Examples module, Symfony, and Controllers",
"description" : "Examples module, Symfony, and Controllers",
"freshnessDate" : 2015-12-11,
"homepage" : "https://docs.acquia.com/articles/examples-module-symfony-controllers-and-menu",
"canonicalSource" : "https://docs.acquia.com/articles/examples-module-symfony-controllers-and-menu",
"license" : "CC BY-SA"
}
-->

<!-- @section -->

# Overview

_Why is Drupal 8 module development so different?_

Drupal 8 implements changes that are necessary to modernize itself so that it may more effectively leverage common standards and remain competitive with other modern frameworks.

<!-- @section -->

## In this lesson...

*   Learn about the [Examples contributed project](https://docs.acquia.com/articles/examples-module-symfony-controllers-and-menu#examples), the [Symfony PHP framework](https://docs.acquia.com/articles/examples-module-symfony-controllers-and-menu#symfony), [Controllers](https://docs.acquia.com/articles/examples-module-symfony-controllers-and-menu#what-controllers), and [Routers](https://docs.acquia.com/articles/examples-module-symfony-controllers-and-menu#routers).
*   Build the module `examples/page_example`.
*   Explore how to build the `*.info.yml` file for the `page_example` module, and the `*.routing.yml`, the `Controller.php`, and `*.links.menu.yml` files.

<!-- @section -->

## Drupal 8 and the Examples module

To explore Drupal 8, we use the [Examples](https://www.drupal.org/project/examples) project, both for sample code snippets in this class and to prepare you for future exploration of the various module development capabilities in Drupal. Be sure to follow the setup instructions for this lesson and clone the Git repo for the Drupal 8 version of the module to a location that isn't in the Drupal path — this avoids any chance of the code conflicting with the lab work that you'll be engaging in. Work and examples in this class primarily draw upon the Examples project's module `page_example`.

<!-- @section -->

## Symfony

### What is Symfony?

[Symfony](http://symfony.com/) is a PHP framework that aids in building PHP-based applications by leveraging and modeling for re-use coding best practices, and allows Drupal to reuse well-built freely available code for its routing and controllers. These pre-existing pieces of code were not specifically developed for Drupal and they do implement common public standards.

One example is that Drupal 8 routing and controllers are built from Symfony [routing](http://symfony.com/doc/current/book/routing.html) and [controllers](http://symfony.com/doc/current/book/controller.html). Also, in place of Drupal 7's procedural programming approach Drupal 8 implements an object oriented architecture ([OOP](http://en.wikipedia.org/wiki/Object-oriented_programming)) which allows for greater flexibility. Drupal 8 achieves a significant portion of this flexibility by including the [Symfony Framework](http://symfony.com/) as part of core.

> "Symfony is a collection of over twenty independent libraries that can be used inside any PHP project." The Book, symfony.com, 2014.

The twenty-plus available [components](http://symfony.com/doc/current/components/index.html) listed on the Symfony website are as follows:

| Term | Definition |
|--------|-------------|
| **[HttpFoundation](http://symfony.com/doc/current/components/http_foundation/introduction.html)** | Contains the Request and Response classes, as well as other classes for handling sessions and file uploads |
| **[Routing](http://symfony.com/doc/current/components/routing/introduction.html)** | Powerful and fast routing system that allows you to map a specific URI (for example, `/contact`) to some information about how that request should be handled (for example, execute the `contactAction()` method) |
| **[Form](https://github.com/symfony/Form)** | A full-featured and flexible framework for creating forms and handling form submissions |
| **[Validator](https://github.com/symfony/Validator)** | A system for creating rules about data and then validating whether or not user-submitted data follows those rules |
| **[ClassLoader](http://symfony.com/doc/current/components/class_loader/introduction.html)** | An autoloading library that allows PHP classes to be used without needing to manually require the files containing those classes |
| **[Templating](http://symfony.com/doc/current/components/templating/introduction.html)** | A toolkit for rendering templates, handling template inheritance (i.e. a template is decorated with a layout) and performing other common template tasks |
| **[Security](https://github.com/symfony/Security)** | A powerful library for handling all types of security inside an application |
| **[Translation](https://github.com/symfony/Translation)** | A framework for translating strings in your application |

You can read some helpful information about [PSR-4 autoloading in Drupal 8](https://www.drupal.org/node/2156625), which helps to further explain the concept. For more about the basics of Symfony, see the [Symfony documentation](http://symfony.com/doc/current/index.html).

> **Note**: Drupal 8 does not use the Model-View-Controller software architectural pattern. Drupal 8 only implements Controllers from that programming model.

### Why use Symfony?

One of the most helpful tools introduced with Symfony is the ability to automatically load code for use based upon its location in the code base. Autoloading is the capability of loading pieces of code from a mass storage automatically when needed. As a result, a Drupal developer can include reusable code with a simple [`use`](https://docs.acquia.com/articles/examples-module-symfony-controllers-and-menu#use) line rather than creating a full include file. Autoloading is added with the Symfony ClassLoader component.

Drupal has always been a PHP application, and Symfony is a PHP framework that makes several helpful PHP libraries available. Building on the Symfony framework in Drupal core has both benefits and risks.

This framework change may increase the number of experienced creators, especially PHP developers, who can more easily transition to Drupal and might therefore be motivated to learn and leverage the project. It may also create a barrier to entry/transition for a portion of the existing Drupal community of developers who have deep Drupal knowledge but minimal experience with modern PHP practices. Experienced Drupal developers know that _the drop is always moving_.

Ideally, by creating helpful learning tools and sharing educational information with one another we will be able to address the risk to the community and lower the barrier to entry for pre-existing Drupal develpers.

### What is a Controller?

Controllers can be a challenging concept for someone used to procedural programming like Drupal 7 and earlier primarily used (though some parts were written in OOP ways).

> A controller is a PHP function you create that takes information from the HTTP request and constructs and returns an HTTP response (as a Symfony Responseobject). The response could be an HTML page, an XML document, a serialized JSON array, an image, a redirect, a 404 error or anything else you can dream up. The controller contains whatever arbitrary logic your application needs to render the content of a page. -The Book, symfony.com, 2014.

A controller is the equivalent of a _page callback_ function used in Drupal 7's `hook_menu()` function. In Drupal 8, you create a Controller class with a method to display page data, the same way where in Drupal 7 you created a function referenced by "page callback" in `hook_menu()` to display page data. Controllers are introduced with Symfony.

In the Symfony documentation on controllers there are two very helpful quotes:

*   "A controller is a PHP function you create that takes information from the HTTP request and constructs and returns an HTTP response..."
*   "Every request handled by a Symfony2 project goes through the same simple lifecycle. The framework takes care of the repetitive tasks and ultimately executes a controller, which houses your custom application code."

Drupal is using Symfony in the same way that a custom application written on top of Symfony would, it uses the framework to handle repetitive tasks like using a controller to provide a response.

### What is routing and a router?

> The Symfony router lets you define creative URLs that you map to different areas of your application." and "A route is a map from a URL path to a controller. For example, suppose you want to match any URL like `/blog/my-post` or `/blog/all-about-symfony` and send it to a controller that can look up and render that blog entry. - The Book, symfony.com, 2014.

<!-- @section -->

## How do I create my first Drupal 8 module?

With that basic information as a starting point, now we can create a simple module based on Examples. Most of the module structure now is based on [YAML](http://yaml.org/).

YAML is a human friendly data serialization standard which can be implemented by any programming languages. A helpful YAML tutorial can be found [here](http://ess.khhq.net/wiki/YAML_Tutorial), and you can read more about the details of it [here](http://www.yaml.org/).

1.  Create the [directories](https://docs.acquia.com/articles/examples-module-symfony-controllers-and-menu#directories).
2.  Create the [`page_example.info.yml`](https://docs.acquia.com/articles/examples-module-symfony-controllers-and-menu#info) file.
3.  Create the [`page_example.routing.yml`](https://docs.acquia.com/articles/examples-module-symfony-controllers-and-menu#routing) file.
4.  Create the [`PageExampleController.php`](https://docs.acquia.com/articles/examples-module-symfony-controllers-and-menu#controller) file.
5.  Create the [`page_example.links.menu.yml`](https://docs.acquia.com/articles/examples-module-symfony-controllers-and-menu#links) file.
6.  [Rebuild the caches](https://docs.acquia.com/articles/examples-module-symfony-controllers-and-menu#cc).
7.  Test.

### Setting up directories

First, we must create the file and directory structure that allows the Drupal 8 application to become aware of your module. In Drupal 7 the minimum requirement was to include the `.info` and `.module` files. Drupal 8 requires even less - just the YAML version of the `.info` file, called `.info.yml` which contains a slightly different data structure recognizable by Drupal 8.

A major change is that the custom and contributed module directories are placed in the Drupal root (`modules/[directory]`). All of the core code has been moved into a separate (`core/`) directory of its own. In the (`modules/`) directory, you are encouraged to separate modules between custom and contributed like in Drupal 7.

In your Drupal 8 [docroot](https://docs.acquia.com/articles/docroot-definition), complete the following steps:

1.  Navigate to the `/modules` directory for the creation of our sample module.
2.  Create a directory called `examples`.
3.  Change to the new directory using the following command:

    `cd examples`

4.  Create a second directory called `page_example`. This will mirror the Examples project and its module called `page_example`.
5.  Change to this directory:

    `cd page_example`

For this lesson we will not be installing the actual Examples module. Instead, we will be using code snippets from it to create a sample "Hello world" style module. The intention here is to introduce you to the module in a step by step way that allows you to see the code in the context of a larger module.

### Creating the info.yml file

Now that you're in the correct directory, it's time to create the module's `.info` file.

1.  Create a new file named `page_example.info.yml`.
2.  Add the following code to the file:

    ```
    name: 'Page example'
    type: module
    description: 'An example module showing how to define a page to be displayed at a given URL.'
    package: 'Example modules'
    core: 8.x
    ```

The `type` is a new attribute required in the file, and can be set to `module` or `theme` as theme projects also contain `*.info.yml` files. If you have experience with Drupal 7 development you will recognize several things such as: `name`, `description`, `core` and `dependencies` in the `*.info.yml` file.


Note

White spaces in YAML files have value/meaning; therefore, proper indentation must be used to organize data in array-like structures or else unexpected actions can occur.


You can check out this [Drupal 8 documentation page](https://drupal.org/node/2000204) on drupal.org for additional `key|value` pairs that can be used in a module's `*.info.yml` file and the related change records.

Now, navigate to the **Extend** page in your Drupal website, find the Page Example module, and enable it. Creating a `.module` file is not required prior to enabling the new module using the Drupal UI (extend) or `drush pm-enable`. We expect the final `*.module` file to be smaller as much of the functional code is distributed to service classes, controllers and plugins created with other files in an OOP architecture.

### Creating routing.yml

Now you're going to create the menu structure for your new module.
