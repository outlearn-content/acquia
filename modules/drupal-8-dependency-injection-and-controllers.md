<!--
{
"name" : "drupal-8-dependency-injection-and-controllers",
"version" : "0.0.1",
"title" : "Lesson 11.3 - Dependency injection and controllers",
"description" : "Dependency injection and controllers",
"freshnessDate" : 2015-12-11,
"homepage" : "https://docs.acquia.com/articles/drupal-8-dependency-injection-and-controllers",
"canonicalSource" : "https://docs.acquia.com/articles/drupal-8-dependency-injection-and-controllers",
"license" : "CC BY-SA"
}
-->

<!-- @section -->

# Overview

<!-- @section -->

## Controllers

Controllers can easily use dependency injection because they provide the `create()` method that receives the service container. We have already created our route, so let's create the controller file `src/Controller/DIController.php`.

[Download this file](https://gist.github.com/acquialibrary/d8500203dab880f3e941/archive/9db9582e85e225549c9f7032e6b69b6ee1fcfb8f.zip).

```php
 <?php

 /**
 * @file
 * Contains \Drupal\di_example\Controller\DIController.
 */

 namespace Drupal\di_example\Controller;

 use Drupal\Core\Controller\ControllerBase;
 use Symfony\Component\DependencyInjection\ContainerInterface;
 use Drupal\di_example\DITalk;

 class DIController extends ControllerBase {

 /**
 * @var \Drupal\di_example\DITalk
 */
 protected $dITalk;

 /**
 * @param \Drupal\di_example\DITalk $DITalk
 */
 public function __construct(DITalk $DITalk) {
 $this->dITalk = $DITalk;
 }

 /**
 * When this controller is created, it will get the di_example.talk service
 * and store it.
 *
 * @param \Symfony\Component\DependencyInjection\ContainerInterface $container
 * @return static
 */
 public static function create(ContainerInterface $container) {
 return new static(
 $container->get('di_example.talk')
 );
 }


 public function conversationAboutMood() {
 // We use the injected service to get the message.
 $message = $this->dITalk->getResponseToMood();

 // We return a render array of the message.
 return [
 '#type' => 'markup',
 '#markup' => '<p>' . $this->t($message) . '</p>',
 ];
 }
 }
```

[view raw](https://gist.github.com/acquialibrary/d8500203dab880f3e941/raw/9db9582e85e225549c9f7032e6b69b6ee1fcfb8f/DIController.php) [DIController.php](https://gist.github.com/acquialibrary/d8500203dab880f3e941#file-dicontroller-php) hosted with ❤ by [GitHub](https://github.com)

### Extending ControllerBase

It usually makes sense to extend the base class when possible. Extending `ControllerBase` provides the systems that support dependency injection for controllers.

*   **`create() / __construct()`**

    `ControllerBase` gives us a `create()` method that will be passed the services container. We can use this create function to retrieve any services we need and pass them to the `__construct()` method which will store them as class properties. The `return new static()` line means that this method will return a new instance of the class that it is in.

*   **Route Method**

    Our `conversationAboutMood()` method is very simple — it uses our service to get a message and then returns a render array with this message.
