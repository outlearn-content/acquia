<!--
{
"name" : "drupal-8-entity-queries-and-loading-entities",
"version" : "0.0.1",
"title" : "Lesson 6.1 - Entity queries and loading entities",
"description" : "Entity queries and loading entities",
"freshnessDate" : 2015-12-11,
"homepage" : "https://docs.acquia.com/articles/drupal-8-entity-queries-and-loading-entities",
"canonicalSource" : "https://docs.acquia.com/articles/drupal-8-entity-queries-and-loading-entities",
"license" : "CC BY-SA"
}
-->

<!-- @section -->

# Overview

This lesson is an explanation of how to query entities in Drupal 8.

<!-- @section -->

## In this lesson...

*   Assemble the module `examples/query_example`.
*   Learn about entity queries.
*   Learn about entity loading.
*   Explore and build the `PageExampleController` class.

Drupal core has historically provided limited approaches to manipulating and using the entity system which was initially introduced with Drupal-7 and as a result of that need, a solution arose in the contributed module space. This effort is the [Entity API](https://www.drupal.org/project/entity) module. Drupal 7 core, however, it did provide the `EntityFieldQuery` class, which can be used to search Drupal entities programmatically. The class contains several methods that aid with entity queries based on conditions such as field values or class properties. Additional documentation on the subject can be found on drupal.org at [How to use EntityFieldQuery](https://www.drupal.org/node/1343708).

Querying entities has changed since Drupal 7 because `EntityFieldQuery` has been replaced by the core service called `entity.query` which can instantiate a query object for a specified entity type. The [`entity.query`](https://api.drupal.org/api/drupal/core%21core.services.yml/service/entity.query/8) is an implementation of the [`QueryInterface`](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Entity%21Query%21QueryInterface.php/interface/QueryInterface/8), which is never directly implemented. Instead, the `QueryInterface` is implemented through `QueryFactory` classes such as `entity.query`. It is possible to use this service statically through the [`\Drupal`](https://api.drupal.org/api/drupal/core%21lib%21Drupal.php/function/Drupal%3A%3AentityQuery/8) namespace or by leveraging dependency injection.

<!-- @section -->

## The entity query service

The entity query service allows us to get a list of nodes that match a pattern. This is similar to an SQL query, but instead of writing a single statement, we build a query object and then execute it. We can call functions on this query object to add filters or specify parameters of the query. These two techniques function with content entities such as nodes, users, comments, taxonomy terms, and configuration entities.

There are two ways to create this query object.

### Class based method (recommended)

We will talk about services more in later chapters. In this example, we will use the following method, using class variables and functions to obtain a query object. We'll use that object to get a list of node ids that match a pattern.

[Download this file](https://gist.github.com/acquialibrary/d7fa2f328579c28baaec/archive/d92d1b6b405c2cc880e2b009fb34387cc3745cad.zip).

```
 // This function and the next are part of the dependency injection pattern. We will explain those in future chapters.
 public function __construct(QueryFactory $entity_query) {
 $this->entity_query = $entity_query;
 }

 public static function create(ContainerInterface $container) {
 return new static(
 // User the $container to get a query factory object. This object let's us create query objects.
 $container->get('entity.query')
 );
 }

 // This function shows how to
 public function myCallback() {
 // Use the factory to create a query object for node entities.
 $query = $this->entity_query->get('node');

 // Add a filter (published).
 $query->condition('status', 1);

 // Run the query.
 $nids = $query->execute();
 }
```

[view raw](https://gist.github.com/acquialibrary/d7fa2f328579c28baaec/raw/d92d1b6b405c2cc880e2b009fb34387cc3745cad/query_object.php) [query_object.php](https://gist.github.com/acquialibrary/d7fa2f328579c28baaec#file-query_object-php) hosted with ❤ by [GitHub](https://github.com)

### Static method (not recommended)

Most code is no longer stored in a module's `*.module` file, so if the need arises you will not be able to use the service container. This also holds true with building tests. In these two instances, you may need to use the static call to `entity.query` (or a similar method).

To statically use the `entity.query`, add the following to your code:

```
$query = \Drupal::entityQuery('node');
```

If you want to use a different type of entity, you can replace `node`. You can specify any other entity type's machine name to obtain the query object for the specified entity type inside of the `$query` variable. The `entityQuery()` static method on the `\Drupal` namespace is a shortcut, using the entity.query service.

<!-- @section -->

## Querying entities

The following are examples of querying for node entities using a query object.

This is an example of a basic static query capable of returning a site's published nodes. The `$nids` variable will return an array of node ids. These nids will be keyed by revision ids in the event that revisioning is enabled for the entity type of node. Or, the nids will be keyed by the entity ids if revisioning is disabled.

```
/**
* @var $query \Drupal\Core\Entity\Query\QueryInterface
*/
$query = $this->entity_query->get('node')
->condition('status', 1);

$nids = $query->execute();
```

> **Note**:
In an IDE like PHPStorm, providing comments allows the system to provide autocompletion. In our example, we are using an annotation above the variable declaration that says that the variable is the class `QueryInterface` and where that class is located.

>This is also helpful to add to function declarations. While not required, this is useful while learning and for others who have to edit the code, because they can easily explore the methods available to them.

>Sometimes PHPStorm will know that a variable is of a certain type because you used a method that only returns that type. In other cases, like the one above, it could return various classes depending on the argument, so you must tell the IDE which class is correct. It's a good practice to add these annotations for all examples just to be clear and for people who aren't using an IDE.

### Query conditions

The next example uses both property and field conditions in addition to checking for a published status of `true`. In this query, we retrieve the node ids of all the published nodes that were last updated, prior to the time the query was run. The node title must contain the word the words _ipsum lorem_. The node will contain the taxonomy term called `test` as a value in the field `field_tags`.

```
$query = $this->entity_query->get('node')
->condition('status', 1)
->condition('changed', REQUEST_TIME, '<')
->condition('title', 'ipsum lorem', 'CONTAINS')
->condition('field_tags.entity.name', 'test');

$nids = $query->execute();
```

There is no distinction between `propertyCondition` and fieldCondition which had been different when using EntityFieldQuery in Drupal 7\. This is because in Drupal 8 the QueryInterface class provides the condition() method which combines both the property and field conditions and allows for condition groups. It is also possible to specify conditions based on referenced entities by adding the `entity.(column)` to the entity reference field name like we did for taxonomy and the value of `test`.

### Grouping Query Conditions

This example demonstrates how condition groups which can include both AND and OR operators allow for more powerful queries. It alters the previous query to retrieve nodes that either have:

*   the `ipsum lorem` string in their title
*   have a reference to the taxonomy term `test` in their `field_tags` field.

```
$query = $this->entity_query->get('node')
->condition('status', 1)
->condition('changed', REQUEST_TIME, '<');

$group = $query->orConditionGroup()
->condition('title', 'ipsum lorem', 'CONTAINS')
->condition('field_tags.entity.name', 'test');

$nids = $query->condition($group)->execute();
```

This OR test was created with an `orConditionGroup` object to which the query is passed as a condition. Alternatively, it is possible to group multiple conditions in an `andConditionGroup` as as a test.

These are only two of a longer [list](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Entity%21Query%21QueryInterface.php/interface/QueryInterface/8) of methods provided by the `QueryInterface` class that can extend the query. For example, it's possible to make this a count query with [`QueryInterface::count`](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Entity%21Query%21QueryInterface.php/function/QueryInterface%3A%3Acount/8) and to create sortable tables for this query with [`QueryInterface::tableSort`](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Entity%21Query%21QueryInterface.php/function/QueryInterface%3A%3AtableSort/8).

### Querying for Revisions

By default, the entity query will only return the most recent revision for each entity. You can obtain the revisions by using `allRevisions()`.

```
$query->allRevisions();
```
