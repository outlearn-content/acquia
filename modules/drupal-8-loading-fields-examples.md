<!--
{
"name" : "drupal-8-loading-fields-examples",
"version" : "0.0.1",
"title" : "Lesson 7.2 - Loading fields examples",
"description" : "TBD",
"freshnessDate" : 2015-12-11,
"homepage" : "https://docs.acquia.com/articles/drupal-8-loading-fields-examples",
"canonicalSource" : "https://docs.acquia.com/articles/drupal-8-loading-fields-examples",
"license" : "CC BY-SA"
}
-->

<!-- @section -->

# Overview

## Example module

We will create an example module that loads a node and prints some information about it. First, create at least one node with the body field filled in. Then, we will set up the basics of our module. This module creates a single route and a controller that loads a single node.

Create the file `field_example.info.yml`:

```
name: Field Example
type: module
description: Example showing how to use fields
core: 8.x
package: Examples
```

Create the `field_example.routing.yml` file:

```
field_example.simple:
path: 'examples/field-example/simple'
defaults:
_controller: '\Drupal\field_example\Controller\FieldExampleDefaultController::simple'
requirements:
_access: 'TRUE'
```

Create the controller file - `src/Controller/FieldExampleDefaultController.php`:

[Download this file](https://gist.github.com/acquialibrary/3939e98772eaaf681504/archive/7a05a4de926630f8d8be962739bd36ec76ace459.zip).

```php
 <?php
 /**
 * @file
 * Contains \Drupal\field_example\Controller\FieldExampleDefaultController.
 */
 namespace Drupal\field_example\Controller;

 use Drupal\Core\Controller\ControllerBase;
 use Drupal\Core\Entity\EntityManagerInterface;
 use Drupal\Core\Entity\Query\QueryFactory;
 use Symfony\Component\DependencyInjection\ContainerInterface;

 /**
 * Controller routines for field example routes.
 */
 class FieldExampleDefaultController extends ControllerBase {

 /**
 * @var \Drupal\Core\Entity\EntityManagerInterface
 */
 protected $entityManager;

 /**
 * @var \Drupal\Core\Entity\Query\QueryFactory
 */
 protected $queryFactory;
 /**
 * @param \Drupal\Core\Entity\EntityManagerInterface $entityManager
 * @param \Drupal\Core\Entity\Query\QueryFactory $queryFactory
 */
 public function __construct(EntityManagerInterface $entityManager, QueryFactory $queryFactory) {
 $this->entityManager = $entityManager;
 $this->queryFactory = $queryFactory;

 }

 /**
 * @param \Symfony\Component\DependencyInjection\ContainerInterface $container
 * @return static
 */
 public static function create(ContainerInterface $container) {
 return new static(
 $container->get('entity.manager'),
 $container->get('entity.query')
 );
 }

 public function simple() {
 /**
 * We need to get one node with a body field.
 */

 /**
 * @var $query \Drupal\Core\Entity\Query\QueryInterface
 */
 $query = $this->queryFactory->get('node');

 // We just want some nodes.
 $nids = $query->execute();

 // If there are no nodes, we exit.
 if (count($nids) < 1) {
 return ['#markup' => $this->t('No nodes with a body field.')];
 }

 /**
 * @var $entity_storage \Drupal\Core\Entity\ContentEntityStorageBase
 */
 $entity_storage = $this->entityManager->getStorage('node');

 /**
 * @var $entity \Drupal\Core\Entity\Entity
 */
 $entity = $entity_storage->load(array_values($nids)[0]);

 // We will get the values from here.

 return [
 '#markup' => 'placeholder',
 ];
 }
 }
```
[view raw](https://gist.github.com/acquialibrary/3939e98772eaaf681504/raw/7a05a4de926630f8d8be962739bd36ec76ace459/FieldExampleDefaultController.php) [FieldExampleDefaultController.php](https://gist.github.com/acquialibrary/3939e98772eaaf681504#file-fieldexampledefaultcontroller-php) hosted with ❤ by [GitHub](https://github.com)

After you have the node, let's get some values for the body and change them using the previously described patterns.

### Controller fields

This is an example for the file `src/Controller/FieldExampleDefaultController.php`.

[Download this file](https://gist.github.com/acquialibrary/ded1cd04c05befcdfb8f/archive/a3c6e5a56612f51538db82fc3950b10fd93e8e44.zip).

```
 /**
 * @var $field \Drupal\Core\Field\FieldItemList
 */
 $body_field = $entity->get('body');

 // If we want a value off the first item, we can use a magic method __get()
 // Which is sometimes easier to use.
 $simple_value = $body_field->value;

 // We get a array of items for this field.
 // Unlike Drupal 7, there are no language keys.
 $field_value = $body_field->getValue();

 // We will set the summary to the value.
 // We don't need to update the entity because the field setValue does that already.
 $body_field->summary = $body_field->value;

 // or
 $field_value[0]['summary'] = $field_value[0]['value'];
 $body_field->setValue($field_value);

 /**
 * @var $definition \Drupal\Core\Field\BaseFieldDefinition
 */
 $definition = $body_field->getFieldDefinition();

 $field_type = $definition->get('field_type');

 return [
 'field_type' => [
 '#markup' => 'Field Type: ' . $field_type,
 '#theme_wrappers' => [
 'container' => [],
 ],
 ],
 'field_value' => [
 '#markup' => 'Field Value: ' . $body_field->value,
 '#theme_wrappers' => [
 'container' => [],
 ],
 ],
 ];
```

[view raw](https://gist.github.com/acquialibrary/ded1cd04c05befcdfb8f/raw/a3c6e5a56612f51538db82fc3950b10fd93e8e44/FieldExampleDefaultController.php) [FieldExampleDefaultController.php](https://gist.github.com/acquialibrary/ded1cd04c05befcdfb8f#file-fieldexampledefaultcontroller-php) hosted with ❤ by [GitHub](https://github.com)

### The final controller

[Download this file](https://gist.github.com/acquialibrary/d9686081ab44c87ee772/archive/7e5bf9a18ccdc6162ae2104c33a52ccb21e7139a.zip).

```php
 <?php

 /**
 * @file
 * Contains \Drupal\field_example\Controller\FieldExampleDefaultController.
 */

 namespace Drupal\field_example\Controller;

 use Drupal\Core\Controller\ControllerBase;
 use Drupal\Core\Entity\EntityManagerInterface;
 use Drupal\Core\Entity\Query\QueryFactory;
 use Symfony\Component\DependencyInjection\ContainerInterface;

 /**
 * Controller routines for field example routes.
 */
 class FieldExampleDefaultController extends ControllerBase {

 /**
 * @var \Drupal\Core\Entity\EntityManagerInterface
 */
 protected $entityManager;

 /**
 * @var \Drupal\Core\Entity\Query\QueryFactory
 */
 protected $queryFactory;

 /**
 * @param \Drupal\Core\Entity\EntityManagerInterface $entityManager
 * @param \Drupal\Core\Entity\Query\QueryFactory $queryFactory
 */
 public function __construct(EntityManagerInterface $entityManager, QueryFactory $queryFactory) {
 $this->entityManager = $entityManager;
 $this->queryFactory = $queryFactory;
 }

 /**
 * @param \Symfony\Component\DependencyInjection\ContainerInterface $container
 * @return static
 */
 public static function create(ContainerInterface $container) {
 return new static(
 $container->get('entity.manager'),
 $container->get('entity.query')
 );
 }

 public function simple() {
 /**
 * We need to get one node with a body field.
 */

 /**
 * @var $query \Drupal\Core\Entity\Query\QueryInterface
 */
 $query = $this->queryFactory->get('node');

 // We just want some nodes.
 $nids = $query->execute();

 // If there are no nodes, we exit.
 if (count($nids) < 1) {
 return ['#markup' => $this->t('No nodes with a body field.')];
 }

 /**
 * @var $entity_storage \Drupal\Core\Entity\ContentEntityStorageBase
 */
 $entity_storage = $this->entityManager->getStorage('node');

 /**
 * @var $entity \Drupal\Core\Entity\Entity
 */
 $entity = $entity_storage->load(array_values($nids)[0]);

 /**
 * @var $field \Drupal\Core\Field\FieldItemList
 */
 $body_field = $entity->get('body');

 // If we want a value off the first item, we can use a magic method __get()
 // Which is sometimes easier to use.
 $simple_value = $body_field->value;

 // We get a array of items for this field.
 // Unlike Drupal 7, there are no language keys.
 $field_value = $body_field->getValue();

 // We will set the summary to the value.
 // We don't need to update the entity because the field setValue does that already.
 $body_field->summary = $body_field->value;

 // or
 $field_value[0]['summary'] = $field_value[0]['value'];
 $body_field->setValue($field_value);

 /**
 * @var $definition \Drupal\Core\Field\BaseFieldDefinition
 */
 $definition = $body_field->getFieldDefinition();

 $field_type = $definition->get('field_type');

 return [
 'field_type' => [
 '#markup' => 'Field Type: ' . $field_type,
 '#theme_wrappers' => [
 'container' => [],
 ],
 ],
 'field_value' => [
 '#markup' => 'Field Value: ' . $body_field->value,
 '#theme_wrappers' => [
 'container' => [],
 ],
 ],
 ];
 }
 }
```
[view raw](https://gist.github.com/acquialibrary/d9686081ab44c87ee772/raw/7e5bf9a18ccdc6162ae2104c33a52ccb21e7139a/FieldExampleController.php) [FieldExampleController.php](https://gist.github.com/acquialibrary/d9686081ab44c87ee772#file-fieldexamplecontroller-php) hosted with ❤ by [GitHub](https://github.com)

<!-- @section -->

## Review

We learned the long and short form way to access fields and edit them. The major change from Drupal 7 is that we do this through the entity system rather than directly to fields. Additionally, there is a consistent way of access fields using `FieldItemList`.

In some situations, you will need access the the Field Storage system which is also available inside the field object.
