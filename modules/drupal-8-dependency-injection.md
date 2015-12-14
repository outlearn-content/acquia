<!--
{
"name" : "drupal-8-dependency-injection",
"version" : "0.0.1",
"title" : "Lesson 8.3 - Dependency injection",
"description" : "TBD",
"freshnessDate" : 2015-12-11,
"homepage" : "https://docs.acquia.com/articles/drupal-8-dependency-injection",
"canonicalSource" : "https://docs.acquia.com/articles/drupal-8-dependency-injection",
"license" : "CC BY-SA"
}
-->

<!-- @section -->

# Overview

Drupal 8 uses Symfony dependency injection containers to manage service instantiation and injection of those services into classes that need them. This decouples functionality and improves testability. For these two reasons alone it's important to learn what dependency injection means, why and how we use it.

Now that you have created a service and used it, you may want to better understand dependency injection and service definitions.

<!-- @section -->

## Service Definitions

[Services](http://symfony.com/doc/current/glossary.html#term-service) are defined in Symfony documentation, in the [Service Container](http://symfony.com/doc/current/book/service_container.html) section:

> Put simply, a Service is any PHP object that performs some sort of "global" task. It's a purposefully-generic name used in computer science to describe an object that's created for a specific purpose (e.g. delivering emails).

Services are objects that perform a task that you would like to reuse globally without having to rewrite the same code multiple times. This is useful for setting a database connection or creating a mailer for sending emails to another end point. Services split functionality into reusable components. A service is a PHP class that performs some global operations and that is registered with the service container in order to be accessed.

This is then related to the service container which exists as another object - one which manages a collection of services. You instantiate services using `[module_name].services.yml`, which then saves this information in the container object. Access the service by instantiating it and operating on it using `get` or `set`.

Typically, you want to use dependency injection to pass your service container into your methods called by your objects.

### An Example Common Service: Configuration and the Config object

Drupal 8 provides a [Config](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Config%21Config.php/class/Config/8) object that we can use to interact with the configuration.

Some classes already have it available through [dependency injection](http://symfony.com/doc/current/components/dependency_injection/types.html) such as described on Drupal.org in the discussion about [services and dependency injection in Drupal 8](https://www.drupal.org/node/2133171).

> Drupal 8 introduces the concept of services to decouple reusable functionality and makes these services pluggable and replaceable by registering them with a service container. As a developer, it is best practice to access any of the services provided by Drupal using the service container to ensure the decoupled nature of these systems is respected.

Symfony makes a similar statement:

> Making a class's dependencies explicit and requiring that they be injected into it is a good way of making a class more reusable, testable and decoupled from others.

In Lesson 3, we discussed configuration and introduced the `ConfigFormBase` class for the services_example module. It is one class that uses dependency injection to provide a config object. The `config()` method of the parent class is used to retrieve a config object populated with the `demo.settings` simple configuration, so you might say that the method is injecting the variables (settings).

<!-- @section -->

## Dependency Injection

What is dependency injection? Dependency Injection is defined by Wikipedia (DI) as:

> Dependency injection is a [software design pattern](http://en.wikipedia.org/wiki/Software_design_pattern) in which one or more [dependencies](http://en.wikipedia.org/wiki/Coupling_%28computer_programming%29) (or services) are injected, or passed by [reference](http://en.wikipedia.org/wiki/Reference_%28computer_science%29), into a dependent [object](http://en.wikipedia.org/wiki/Object_%28computer_science%29) (or client) and are made part of the client's state. The pattern separates the creation of a client's dependencies from its own behavior, which allows program designs to be [loosely coupled](http://en.wikipedia.org/wiki/Loose_coupling) and to follow the [dependency inversion](http://en.wikipedia.org/wiki/Dependency_inversion_principle) and `<a href="http://en.wikipedia.org/wiki/Single_responsibility_principle">single responsibility principles</a>`.

The Drupal.org documentation on dependency injection states that:

> "Dependency injection is the preferred method for accessing and using services in Drupal 8 and should be used whenever possible."

### Why use dependency injection?

In some cases, using dependency injection is needed to override core services. If a developer needs to implement logic that supersedes a core service, like verifying page access, dependency injection can be used. For an example of this use case, see [Access checking on routes](https://www.drupal.org/node/2122195). The most common need for dependency injection is to allow for Unit Testing using tools such as [PHPUnit](https://phpunit.de/) which is "a programmer-oriented testing framework for PHP. It is an instance of the [xUnit architecture](http://en.wikipedia.org/wiki/XUnit) for [unit testing](http://en.wikipedia.org/wiki/Unit_testing) frameworks."

### What not to do - loading services statically

Many of the current tutorials on Drupal 8 demonstrate loading services statically through the `\Drupal` class. Developers who are used to Drupal 7 may find it faster to write that way, but using dependency injection is the technically preferred approach. If you do not understand service containers and dependency injection you may find yourself statically requesting services and coupling your code like it was procedural. This is against best practices and will hinder you in developing clean modules and code. As an example:

```
$serviceExampleService = \Drupal::service('service_example.example_service');;
$value = $serviceExampleService->getServiceExampleValue();

// or
$value = \Drupal\service_example\ServiceExampleService::getServiceExampleValue();
```

Services, Dependency Injection, and service containers are essentially the future of Drupal. These new concepts can be quite challenging to tackle but are critical towards improving your understanding of how core Drupal is evolving into something new.

### Service or Dependency Injection Container

The next concept is the [service container](http://symfony.com/doc/current/book/service_container.html). We use service containers in our Drupal applications to instantiate, organize, and retrieve objects in a standardized and centralized way.

With controllers, getting access to the container is very easy. We can extend the [`ControllerBase`](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Controller%21ControllerBase.php/class/ControllerBase/8) class which gives us that in addition to some other helpers. Alternatively, our Controller can implement the [`ContainerInjectionInterface`](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21DependencyInjection%21ContainerInjectionInterface.php/interface/ContainerInjectionInterface/8) that also gives us access to the container.

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
* {@inheritdoc}
*/
public static function create(ContainerInterface $container) {
return new static(
$container->get('service_example.example_service')
);
}
}
```

### Load two or more services in construct and create

The `create()` method on the controller gets the container passed in, and then it can inject anything in the container into the constructor. So if you know what service(s) you want your controller to have access to, you can do the following:

```
class QueryExampleController extends ControllerBase {
protected $entity_query;
protected $entity_manager;

public function __construct(QueryFactory $entity_query, EntityManagerInterface $entity_manager) {
$this->entity_query = $entity_query;
$this->entity_manager = $entity_manager;
}
public static function create(ContainerInterface $container) {
return new static(
$container->get('entity.query'),
$container->get('entity.manager')
);
}
```

Later in your code, you can access the entity manager. For your use case, you can use it to load the nodes:

```
$nids = $this->advancedQuery();
/** @var $node_storage \Drupal\node\NodeStorage */
$node_storage = $this->entity_manager->getStorage('node');
$nodes = $node_storage->loadMultiple($nids);
```

This approach is similar to how core loads the services it needs.
