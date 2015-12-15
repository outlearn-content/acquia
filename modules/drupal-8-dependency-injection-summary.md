<!--
{
"name" : "drupal-8-dependency-injection-summary",
"version" : "0.0.1",
"title" : "Lesson 8.4 - Conclusion and other information",
"description" : "Conclusion and other information",
"freshnessDate" : 2015-12-11,
"homepage" : "https://docs.acquia.com/articles/drupal-8-dependency-injection-summary",
"canonicalSource" : "https://docs.acquia.com/articles/drupal-8-dependency-injection-summary",
"license" : "CC BY-SA"
}
-->

<!-- @section -->

# Overview

<!-- @section -->

## Conclusion and summary

The power of the service container allows for writing decoupled functionality in code and passing it where it’s needed. You can also declare dependencies when you register services. This means that when Drupal instantiates a service object, it will do so for all its dependencies as well, and pass them to its constructor. You can read more about how to do that in the [documentation for Services and dependency injection](https://www.drupal.org/node/2133171) in Drupal 8.

We reviewed services, what they are and how they work — they're great way of maintaining reusable and decoupled pieces of functionality accessible from anywhere. We also introduced the concept of dependency injection — the equivalent of passing parameters to procedural functions, but done using constructor methods (or setters), under the hood, by Symfony and the service container and the dependency injection container.

<!-- @section -->

## Additional exercises

1.  Add an additional function that could act as a service.
2.  Demonstrate both the static and dependency injection versions of the function.
3.  Identify in core one to three static uses of functions that you believe should instead be dependency injection.
4.  Describe two different use cases for when you'd want to use dependency injection.
5.  Describe one use case for when you would want to use a static function instead of dependency injection.
