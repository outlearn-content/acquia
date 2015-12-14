<!--
{
"name" : "drupal-8-dependency-injection-and-plugins",
"version" : "0.0.1",
"title" : "Lesson 11.4 - Dependency injection and plugins",
"description" : "TBD",
"freshnessDate" : 2015-12-11,
"homepage" : "https://docs.acquia.com/articles/drupal-8-dependency-injection-and-plugins",
"canonicalSource" : "https://docs.acquia.com/articles/drupal-8-dependency-injection-and-plugins",
"license" : "CC BY-SA"
}
-->

<!-- @section -->

# Overview

<!-- @section -->

## Plugins

Plugins are the most complex component to add dependency injection to. Many plugins don't require dependency injection, making it sometimes challenging to find examples to copy. Most plugins extend other classes and may use interfaces. Blocks, for example, extend `BlockBase` which implements `BlockPluginInterface`.

Dependency injection should be added at the individual plugin level and not at the base class level. This means that we can add dependency injection to any plugin and that when we create new types of plugins we assume that they won't always need to use dependency injection.

### Implementing ContainerFactoryPluginInterface

The key to making plugins use dependency injection is to implement the `ContainerFactoryPluginInterface`. When plugins are created, the code first checks if the plugin implements this interface. If it does, it uses the `create()` and `__construct()` pattern, if not, it uses the just the `__construct()` pattern.

[Download this file](https://gist.github.com/acquialibrary/a7025196e4721dfc1359/archive/e16b42fc37bc65ea330a2fd156edbb3783507863.zip).

```php
 <?php
 /**
 * @file
 * Contains \Drupal\Core\Plugin\Factory\ContainerFactory.
 */

 namespace Drupal\Core\Plugin\Factory;

 use Drupal\Component\Plugin\Factory\DefaultFactory;

 /**
 * Plugin factory which passes a container to a create method.
 */
 class ContainerFactory extends DefaultFactory {

 /**
 * {@inheritdoc}
 */
 public function createInstance($plugin_id, array $configuration = array()) {
 $plugin_definition = $this->discovery->getDefinition($plugin_id);
 $plugin_class = static::getPluginClass($plugin_id, $plugin_definition, $this->interface);

 // If the plugin provides a factory method, pass the container to it.
 if (is_subclass_of($plugin_class, 'Drupal\Core\Plugin\ContainerFactoryPluginInterface')) {
 return $plugin_class::create(\Drupal::getContainer(), $configuration, $plugin_id, $plugin_definition);
 }

// Otherwise, create the plugin directly.
 return new $plugin_class($configuration, $plugin_id, $plugin_definition);
 }

 }
```
[view raw](https://gist.github.com/acquialibrary/a7025196e4721dfc1359/raw/e16b42fc37bc65ea330a2fd156edbb3783507863/ContainerFactory) [ContainerFactory](https://gist.github.com/acquialibrary/a7025196e4721dfc1359#file-containerfactory) hosted with ❤ by [GitHub](https://github.com)

### Creating a Block

We will now create a block that functions similarly to our controller. We add dependency injection by implementing the `ContainerFactoryPluginInterface`. It uses the `create()` and `__construct()` pattern we used previously. Because plugins take additional arguments, we also pass these through the two methods. `__construct()` also calls the parent construct function so that all of the functionality that provides is maintained.

[Download this file](https://gist.github.com/acquialibrary/c6a176c864c645ab707c/archive/2b3031046317f9b27e30f7dfb9010a2833ad0754.zip).

```php
 <?php

 /**
 * @file
 * Contains \Drupal\di_example\Plugin\Block\DIExample.
 */

 namespace Drupal\di_example\Plugin\Block;

 use Drupal\Core\Block\BlockBase;
 use Drupal\Core\Plugin\ContainerFactoryPluginInterface;
 use Symfony\Component\DependencyInjection\ContainerInterface;
 use Drupal\di_example\DITalk;

/**
 * Provides a block to that shows a conversation about mood.
 *
 * @Block(
 * id="di_example_conversation_mood",
 * admin_label = @Translation("DI Example: Conversation about mood")
 * )
 */
 class DIExample extends BlockBase implements ContainerFactoryPluginInterface {
 /**
 * @var $dITalk \Drupal\di_example\DITalk
 */
 protected $dITalk;

 /**
 * @param array $configuration
 * @param string $plugin_id
 * @param mixed $plugin_definition
 * @param \Drupal\di_example\DITalk $DITalk
 */
 public function __construct(array $configuration, $plugin_id, $plugin_definition, DITalk $DITalk) {
 // Call parent construct method.
 parent::__construct($configuration, $plugin_id, $plugin_definition);

 // Store our dependency.
 $this->dITalk = $DITalk;
 }

 /**
 * @param \Symfony\Component\DependencyInjection\ContainerInterface $container
 * @param array $configuration
 * @param string $plugin_id
 * @param mixed $plugin_definition
 * @return static
 */
 public static function create(ContainerInterface $container, array $configuration, $plugin_id, $plugin_definition) {

 return new static(
 $configuration,
 $plugin_id,
 $plugin_definition,
 $container->get('di_example.talk')
 );
 }

 /**
 * {@inheritdoc}
 */
 public function build() {
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

[view raw](https://gist.github.com/acquialibrary/c6a176c864c645ab707c/raw/2b3031046317f9b27e30f7dfb9010a2833ad0754/DIExample.php) [DIExample.php](https://gist.github.com/acquialibrary/c6a176c864c645ab707c#file-diexample-php) hosted with ❤ by [GitHub](https://github.com)

<!-- @section -->

## When you cannot use dependency injection

You cannot use dependency injection in a static context. This means that either the method is called outside of a class or is called on a static method of a class. A static method is one where we call a method of a class without using an instance of that class. Because we do not have an instance, we never called the `__construct()` method and we cannot use `$this` since it doesn't refer to anything.

Static contexts are most often inside `.module` files. For example, we could use the service as shown above in a module file like this ( `di_example.module`:

```php
<?php

/**
* Implements hook_entity_load().
*
* @param $entities
* @param $entity_type
*/
function di_example_entity_load($entities, $entity_type) {
/**
* Because we are not in a class, we cannot use dependency injection.
*
* @var $mood_ring_service \Drupal\di_example\DIMoodRing
*/
$mood_ring_service = \Drupal::service('di_example.mood_ring');

$mood = $mood_ring_service->getMood();
}
```
