<!--
{
"name" : "drupal-8-loading-entities",
"version" : "0.0.1",
"title" : "Lesson 6.2 - Loading entities ",
"description" : "TBD",
"freshnessDate" : 2015-12-11,
"homepage" : "https://docs.acquia.com/articles/drupal-8-loading-entities",
"canonicalSource" : "https://docs.acquia.com/articles/drupal-8-loading-entities",
"license" : "CC BY-SA"
}
-->

<!-- @section -->

# Overview

<!-- @section -->

## Loading entities

The `execute()` method on the query object we’ve been working with returns an array of entity IDs. To use them, we now have to load those entity objects and work with them. Drupal 7 used the [`entity_load()`](https://api.drupal.org/api/function/entity_load/7) function would return an array of objects based on IDs passed to it. In Drupal 8, this helper function is maintained and used much in the same way, except only for one entity at a time. An additional function was also created to load multiple entities: [entity_load_multiple()](https://api.drupal.org/api/drupal/core%21includes%21entity.inc/function/entity_load_multiple/8).

There are three ways to load entities:

*   **Class method (recommended)**

    Dependency injection will be discussed in a future lesson. The important part of this code is the `example()` function, which uses the `entityManager` object to build a node storage object. We then use this node storage object to obtain node objects in either a list or as a single value.

    [Download this file](https://gist.github.com/acquialibrary/7bdfb45d8af6454d4128/archive/b56b15d21da7843e0b18cbdf92b3b15dae771feb.zip).

```
 protected $entityManager;

 /**
 * {@inheritdoc}
 */
 public function __construct(EntityManagerInterface $entityManager) {
 $this->entityManager = $entityManager;
 }

 /**
* {@inheritdoc}
 */
 public static function create(ContainerInterface $container) {
 return new static(
 $container->get('entity.manager')
 );
 }

 /**
 * Return a list of nodes that are published.
 */
protected function example() {
 // We get the node storage object.
 $node_storage = $this->entityManager->getStorage('node');

 // We use the load function to load a single node object.
 $nid = 1;
 $node = $node_storage->load($nid);

 // We load a revision
 $revision_id = 1;
 $node = $node_storage->loadRevision($revision_id);

 // We use the loadMultiple function to load an array of node objects keyed by node ID.
 $nids = [1,2,3,4];
 $nodes = $node_storage->loadMultiple($nids);

 // We will discuss this in future lessons, but you can get the value of simple fields using ->get('field_name')->value.
 return [
 '#markup' => $node->get('title')->value,
 ];
 }
```
[view raw](https://gist.github.com/acquialibrary/7bdfb45d8af6454d4128/raw/b56b15d21da7843e0b18cbdf92b3b15dae771feb/entitymanager_class.php) [entitymanager_class.php](https://gist.github.com/acquialibrary/7bdfb45d8af6454d4128#file-entitymanager_class-php) hosted with ❤ by [GitHub](https://github.com)

*   **Static method (not recommended)**

    You can also use the static method when the container is not available.

```
// Get a node storage object.
$node_storage = \Drupal::entityManager()->getStorage('node');

// Load a single node.
$node_storage->load($nid);
// Load multiple nodes.
$node_storage->loadMultiple($nids);
```

*   **Global method (not recommended)**

    The global method is most similar to Drupal 7\. In this example, we call global functions that load the nodes.

```
// Load a single entity.
$node = entity_load('node', $nid);

// Load multiple entities.
$nodes = entity_load_multiple('node', $nids);
```

<!-- @section -->

## Creating the Controller

We will bring together all of these pieces now. We will add the query objects and use those in our functions to obtain lists of node IDs using an example module `QueryExampleController.php`.
[Download this file](https://gist.github.com/acquialibrary/dbb72b25ba9c04eaef53/archive/613bdd63631fb18dc4066e108174cd38206e6f78.zip).

```php
<?php
/**
 * @file
 * Contains \Drupal\query_example\Controller\QueryExampleController.
 */

namespace Drupal\query_example\Controller;

use Drupal\Core\Entity\Query\QueryFactory;
use Symfony\Component\DependencyInjection\ContainerInterface;
use Drupal\Core\Controller\ControllerBase;

/**
 * Controller routines for page example routes.
 */
class QueryExampleController extends ControllerBase {

  protected $entity_query;

  public function __construct(QueryFactory $entity_query) {
    $this->entity_query = $entity_query;
  }

  public static function create(ContainerInterface $container) {
    return new static(
     $container->get('entity.query')
    );
  }

  protected function simpleQuery() {
    $query = $this->entity_query->get('node')
      ->condition('status', 1);
    $nids = $query->execute();
    return array_values($nids);
  }

  public function basicQuery() {
    return [
      '#title' => 'Published Nodes',
      'content' => [
        '#theme' => 'item_list',
        '#items' => $this->simpleQuery()
      ]
    ];
  }

  protected function intermediateQuery() {
    $query = $this->entity_query->get('node')
      ->condition('status', 1)
      ->condition('changed', REQUEST_TIME, '<')
      ->condition('title', 'ipsum lorem', 'CONTAINS')
      ->condition('field_tags.entity.name', 'test');
    $nids = $query->execute();
    return array_values($nids);
  }

  public function conditionalQuery() {
    return [
      '#title' => 'Published Nodes Called "ipsum lorem" That Have a Tag "test"',
      'content' => [
        '#theme' => 'item_list',
        '#items' => $this->intermediateQuery()
      ]
    ];
  }

  protected function advancedQuery() {
    $query = $this->entity_query->get('node')
      ->condition('status', 1)
      ->condition('changed', REQUEST_TIME, '<');
    $group = $query->orConditionGroup()
      ->condition('title', 'ipsum lorem', 'CONTAINS')
      ->condition('field_tags.entity.name', 'test');
    $nids = $query->condition($group)->execute();
    return array_values($nids);
  }

  public function conditionalGroupQuery() {
    return [
      '#title' => 'Published Nodes That Are Called "ipsum lorem" Or Have a Tag "test"',
      'content' => [
        '#theme' => 'item_list',
        '#items' => $this->advancedQuery()
      ]
    ];
  }

 }

```
[view raw](https://gist.github.com/acquialibrary/dbb72b25ba9c04eaef53/raw/613bdd63631fb18dc4066e108174cd38206e6f78/QueryExampleController.php) [QueryExampleController.php](https://gist.github.com/acquialibrary/dbb72b25ba9c04eaef53#file-queryexamplecontroller-php) hosted with ❤ by [GitHub](https://github.com)



The next step is to edit the existing routing file to call the public functions:[Download this file](https://gist.github.com/acquialibrary/da9ea858ccfcaec10c40/archive/79b8953fd5cdad77cfb675ff2d5d8c68416e0590.zip

```
query_example.simple:  
  path: 'examples/query_example/simple'
  defaults:
    _controller: '\Drupal\query_example\Controller\QueryExampleController::basicQuery'
  requirements:
    _access: 'TRUE'
query_example.intermediate:
  path: 'examples/query_example/intermediate'
  defaults:
    _controller: '\Drupal\query_example\Controller\QueryExampleController::conditionalQuery'
  requirements:
    _access: 'TRUE'
query_example.advanced:
  path: 'examples/query_example/advanced'
  defaults:
    _controller: '\Drupal\query_example\Controller\QueryExampleController::conditionalGroupQuery'
  requirements:
    _access: 'TRUE'
```

[view raw](https://gist.github.com/acquialibrary/da9ea858ccfcaec10c40/raw/79b8953fd5cdad77cfb675ff2d5d8c68416e0590/query_example.routing.yml) [query_example.routing.yml](https://gist.github.com/acquialibrary/da9ea858ccfcaec10c40#file-query_example-routing-yml) hosted with ❤ by [GitHub](https://github.com)
