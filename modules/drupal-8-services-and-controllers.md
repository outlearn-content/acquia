<!--
{
"name" : "drupal-8-services-and-controllers",
"version" : "0.0.1",
"title" : "Lesson 8.2 - Services and controllers",
"description" : "TBD",
"freshnessDate" : 2015-12-11,
"homepage" : "https://docs.acquia.com/articles/drupal-8-services-and-controllers",
"canonicalSource" : "https://docs.acquia.com/articles/drupal-8-services-and-controllers",
"license" : "CC BY-SA"
}
-->

<!-- @section -->

# Overview

<!-- @section -->

## Creating a Controller

We have already created controllers. We will start with a controller that doesn't use the service and then add the service. It will return a render array when the `simple_example()` function is called. It's usually a good idea to extend base classes. In this case, we extend our controller with the `ControllerBase` class. As we will soon see, this gives us access to the services.

[Download this file](https://gist.github.com/acquialibrary/d6037e4eaaa12db3cb10/archive/69021eb586f8f1032315b6e61e8fbe28f7407cef.zip).

```php
 <?php

 /**
 * @file
 * Contains \Drupal\service_example\ServiceExampleController.
 */

 namespace Drupal\service_example\Controller;

 use Drupal\Core\Controller\ControllerBase;

 class ServiceExampleController extends ControllerBase {
 /**
 * {@inheritdoc}
 */
 public function __construct() {
 }

 /**
 * {@inheritdoc}
 */
 public static function create() {
 return new static(
 );
 }

 public function simple_example() {
 return [
 '#markup' => 'Hello World'
 ];
 }
 }
```
[view raw](https://gist.github.com/acquialibrary/d6037e4eaaa12db3cb10/raw/69021eb586f8f1032315b6e61e8fbe28f7407cef/ServiceExampleController.php) [ServiceExampleController.php](https://gist.github.com/acquialibrary/d6037e4eaaa12db3cb10#file-serviceexamplecontroller-php) hosted with ❤ by [GitHub](https://github.com)

<!-- @section -->

## Adding the Service to the Controller

To make the service available, we use a pattern called dependency injection. When our object is created, the `create()` function is called. The create() function then calls the `__construct()` function. After the object is created the `simple_example()` function is called. In Drupal 8, for most controllers, we know that the `create()` function will take one argument, something called a Container. In simple terms, the container gives you access to services. More information is further on in the lesson. Our `create()` function uses that Container to get the services and give them to the `__construct()` function which stores them as class variables. At that point they are available to the class functions.

If this sounds complex, the basic method to add a service is:

1.  Retrieve the service using the Container in the `create()` function.
2.  Pass the service to the `__construct()` function.
3.  Store the service as a class variable.

```
public static function create(ContainerInterface $container) {
return new static(
$container->get('service_example.example_service')
);
}
```

As we mentioned, the create function takes a `ContainerInterface` and which we `$container`. This has an important function `get()`, which we use to get service objects. `return new static()` essentially calls the `__construct()` function. Right now we only have one argument, but you can pass multiple services by using a comma separated list of arguments.

```
public function __construct(ServiceExampleService $serviceExampleService) {
$this->serviceExampleService = $serviceExampleService;
}
```

This function stores the passed in service as a variable. As with the `ContainerInterface` type, it's useful to state the type of argument to help editors like PhpStorm know what functions and properties are available.

```
/**
* @var \Drupal\service_example\ServiceExampleService
*/
protected $serviceExampleService;
```

Now we create a new variable. Now we're using additional class types, we add those to the top of the file with the use statements.

```
use Drupal\service_example\ServiceExampleService;
use Drupal\Core\Controller\ControllerBase;
use Symfony\Component\DependencyInjection\ContainerInterface;
```

Since we have access to the service as `$this->serviceExampleService`, we can use its function `getServiceExampleValue()` in our `simple_example()` function.

```
public function simple_example() {
return [
'#markup' => $this->serviceExampleService->getServiceExampleValue()
];
}
```

The final controller is `ServiceExampleController.php`:

[Download this file](https://gist.github.com/acquialibrary/2e60c0efafd0385acaac/archive/8e1c0b9c1607f1e8b24d743b5ff1ccce5ff823f6.zip)

```php
 <?php

 /**
 * @file
 * Contains \Drupal\service_example\ServiceExampleController.
 */

 namespace Drupal\service_example\Controller;

 use Drupal\service_example\ServiceExampleService;
 use Drupal\Core\Controller\ControllerBase;
 use Symfony\Component\DependencyInjection\ContainerInterface;

 class ServiceExampleController extends ControllerBase {

 /**
 * @var \Drupal\service_example\ServiceExampleService
 */
 protected $serviceExampleService;

 /**
 * {@inheritdoc}
 */
 public function __construct(ServiceExampleService $serviceExampleService) {
 $this->serviceExampleService = $serviceExampleService;
 }

 /**
 * {@inheritdoc}
 */
 public static function create(ContainerInterface $container) {
 return new static(
 $container->get('service_example.example_service')
 );
 }

 public function simple_example() { 
 return [
 '#markup' => $this->serviceExampleService->getServiceExampleValue()
 ];
 }

 }
```

[view raw](https://gist.github.com/acquialibrary/2e60c0efafd0385acaac/raw/8e1c0b9c1607f1e8b24d743b5ff1ccce5ff823f6/ServiceExampleController.php) [ServiceExampleController.php](https://gist.github.com/acquialibrary/2e60c0efafd0385acaac#file-serviceexamplecontroller-php) hosted with ❤ by [GitHub](https://github.com)

You can now enable your module and go to `/examples/service-example/simple` to see it all come together!
