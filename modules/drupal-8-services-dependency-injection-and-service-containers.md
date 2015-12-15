<!--
{
"name" : "drupal-8-services-dependency-injection-and-service-containers",
"version" : "0.0.1",
"title" : "Lesson 8.1 - Services, dependency injection, and service containers",
"description" : "Services, dependency injection, and service containers",
"freshnessDate" : 2015-12-11,
"homepage" : "https://docs.acquia.com/articles/drupal-8-services-dependency-injection-and-service-containers",
"canonicalSource" : "https://docs.acquia.com/articles/drupal-8-services-dependency-injection-and-service-containers",
"license" : "CC BY-SA"
}
-->

<!-- @section -->

# Overview

<!-- @section -->

## In this lesson...

*   Create the module `examples/service_example` to use a custom service and dependency injection.
*   Learn about services and how they relate to dependency injection.
*   Explore the meaning and uses of Dependency Injection.
*   Explore the meaning and uses of Service Containers
*   Focus on what services mean in programming and why they are useful.
*   Explore creating and registering a simple service.

We are going to create a module that defines a new service `service_example.example_service` and then use that service on a page `examples/service-example/simple` that is controlled by a very simple controller `ServiceExampleController`.

<!-- @section -->

## Create the basic module

We will create the following files. The previous lessons explained how to create routers and info files.

The `service_example.info.yml` file:

```
name: 'Service example'
type: module
description: 'An example module showing how to create and use a service.'
package: 'Acquia Example modules'
core: 8.x
```

The `service_example.routing.yml` file:

```
service_example_simple:
path: 'examples/service-example/simple'
defaults:
_controller: '\Drupal\service_example\Controller\ServiceExampleController::simple_example'
requirements:
_access: 'TRUE'
```

<!-- @section -->

## Creating a Service

We are going to create a simple service that provides a function that returns a string. Further on in the lesson we will learn more about services, but for now, you can think of them as global functions or libraries<./p>

To create a service, we need to let the system know it exists using the `service_example.services.yml` file and then add a class that defines the functionality in `src/ServiceExampleService.php`.

In the `service_example.services.yml` file, the top level is a listing of services. Each service is prefixed with the module name `module_name.service_name`. The service class name is defined. The class name should be the same name as the file that contains the service class.

`service_example.services.yml`

```
services:
service_example.example_service:
class: Drupal\service_example\ServiceExampleService
```

The `ServiceExampleService.php` file looks very similar to controllers. We describe what class is defined, we define the namespace (`Drupal\module_name`), we create a public function and some internal logic. The internal logic for this service is a single variable which is set to `Student` when the service is created. When other code uses this service, they will be able to call the `getServiceExampleValue()` function which will return the string `Student`.

[Download this file](https://gist.github.com/acquialibrary/baab3196a591cadcb7cc/archive/446300388665064979cd781701983c80e51a20b5.zip).

```php
 <?php
 /**
 * @file
 * Contains \Drupal\service_example\ServiceExampleService.
 */

 namespace Drupal\service_example;

 class ServiceExampleService {

 protected $service_example_value;

 /**
 * When the service is created, set a value for the example variable.
 */
 public function __construct() {
 $this->service_example_value = 'Student';
 }

 /**
 * Return the value of the example variable.
 */
 public function getServiceExampleValue() {
 return $this->service_example_value;
 }

 }
```

[view raw](https://gist.github.com/acquialibrary/baab3196a591cadcb7cc/raw/446300388665064979cd781701983c80e51a20b5/ServiceExampleService.php) [ServiceExampleService.php](https://gist.github.com/acquialibrary/baab3196a591cadcb7cc#file-serviceexampleservice-php) hosted with ❤ by [GitHub](https://github.com)
