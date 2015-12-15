<!--
{
"name" : "drupal-8-loading-and-editing-fields",
"version" : "0.0.1",
"title" : "Lesson 7.1 - Loading and editing fields",
"description" : "Loading and editing fields",
"freshnessDate" : 2015-12-11,
"homepage" : "https://docs.acquia.com/articles/drupal-8-loading-and-editing-fields",
"canonicalSource" : "https://docs.acquia.com/articles/drupal-8-loading-and-editing-fields",
"license" : "CC BY-SA"
}
-->

<!-- @section -->

# Overview

In Drupal 7, fields were often stored as complex arrays that could take on a number of patterns. In Drupal 8, fields all use the same pattern. They are all objects in the class [`FieldItemList`](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Field%21FieldItemList.php/class/FieldItemList/8). This is the case if they are single value, multi-value, multilingual, built-in fields, or attached fields. In some ways, this makes developing for Drupal 8 easier, but also means that some things that were simple in Drupal 7 are more complex.

In Drupal 7, we often accessed fields directly, using hooks to modify fields when they were loaded, changed, or saved. In Drupal 8, there is a focus on entities instead of fields. Because of this, if we want to modify fields we need to get the fields out of the entities.

<!-- @section -->

## Loading fields attached to entities

To gather information about the fields that are part of an entity, use the following code:

```
/**
* @var $entity \Drupal\Core\Entity\Entity
*/
$entity = $entity_storage->load($entity_id);

/**
* @var $field \Drupal\Core\Field\FieldItemList
*/
$field = $entity->get($field_name);
```

<!-- @section -->

## Getting and setting simple fields

The `FieldItemList` class has the methods [`__get()`](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Field%21FieldItemList.php/function/FieldItemList%3A%3A__get/8) and [`__set()`](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Field%21FieldItemList.php/function/FieldItemList%3A%3A__set/8) that attempt to handle most use cases. These methods assume that if you ask for a value, you want the value from the first item.

```
// We get the string value from the field.
$value = $field->value;

// Get the summary from a body field.
$summary = $body_field->summary;

$field->value = 'changed value';
```

<!-- @section -->

## Getting and setting complex fields

This example demonstrates that some fields have more than just a value — for example, the **body** field also has a summary. If a field has multiple values, you'll need to access it using the longer [getValue()](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Field%21FieldItemList.php/function/FieldItemList%3A%3AgetValue/8) pattern.

[Download this file](https://gist.github.com/acquialibrary/6d7b7aff228ce341f55f/archive/2d6d408853c562849266b7856fd0fc424574b112.zip).

 ```
 // We get a complex field (like the body).
 $value = $field->getValue();

 /**
 * Value will look like this,
 * $value = [
 * [
 * 'value' => 'hello world',
 * 'summary' => 'the summary',
 * ],
 * [
 * 'value' => 'foo',
 * 'summary' => 'bar',
 * ],
 * [
 * 'value' => 'foo',
 * 'summary' => 'bar',
 * ],
 * ];
 */

 // We can access the value from before with,
 $string = $value[0]['value'];

 $value[0]['value'] = 'changed value'];

 // Update the value
 $field->setValue($value);
```
[view raw](https://gist.github.com/acquialibrary/6d7b7aff228ce341f55f/raw/2d6d408853c562849266b7856fd0fc424574b112/complex-field-get-set.php) [complex-field-get-set.php](https://gist.github.com/acquialibrary/6d7b7aff228ce341f55f#file-complex-field-get-set-php) hosted with ❤ by [GitHub](https://github.com)

<!-- @section -->

## Obtaining information about the field

If you want to obtain information about the field (such as the `field_type`), you will need to get the field definition. This comes from [getFieldDefinition](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Field%21FieldItemList.php/function/FieldItemList%3A%3AgetFieldDefinition/8); another object that is in the field item list.

```
/**
* @var $definition \Drupal\Core\Field\BaseFieldDefinition
*/
$definition = $field->getFieldDefinition();

$field_type = $definition->get('field_type');
```
