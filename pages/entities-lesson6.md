# Lesson 6: Querying Entities in Drupal 8

## In this lesson...

*   Assemble the module `examples/query_example`.
*   Learn about entity queries.
*   Learn about entity loading.
*   Explore and build the `PageExampleController` class.

Drupal core has historically provided limited approaches to manipulating and using the entity system which was initially introduced with Drupal-7 and as a result of that need, a solution arose in the contributed module space. This effort is the [Entity API](https://www.drupal.org/project/entity) module. Drupal 7 core, however, it did provide the `EntityFieldQuery` class, which can be used to search Drupal entities programmatically. The class contains several methods that aid with entity queries based on conditions such as field values or class properties. Additional documentation on the subject can be found on drupal.org at [How to use EntityFieldQuery](https://www.drupal.org/node/1343708).

Querying entities has changed since Drupal 7 because `EntityFieldQuery` has been replaced by the core service called `entity.query` which can instantiate a query object for a specified entity type. The [`entity.query`](https://api.drupal.org/api/drupal/core%21core.services.yml/service/entity.query/8) is an implementation of the [`QueryInterface`](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Entity%21Query%21QueryInterface.php/interface/QueryInterface/8), which is never directly implemented. Instead, the `QueryInterface` is implemented through `QueryFactory` classes such as `entity.query`. It is possible to use this service statically through the [`\Drupal`](https://api.drupal.org/api/drupal/core%21lib%21Drupal.php/function/Drupal%3A%3AentityQuery/8) namespace or by leveraging dependency injection.
