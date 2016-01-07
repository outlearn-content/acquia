<!--
{
"name" : "drupal-8-blocks-configuration-and-forms",
"version" : "0.0.1",
"title" : "Lesson 2.1 - Blocks and Configuration",
"description" : "Blocks and Configuration",
"freshnessDate" : 2015-12-11,
"homepage" : "https://docs.acquia.com/articles/drupal-8-blocks-configuration-and-forms",
"canonicalSource" : "https://docs.acquia.com/articles/drupal-8-blocks-configuration-and-forms",
"license" : "CC BY-SA"
}
-->

## Creating a simple block

In the Example project you will find the `block_example` module which contains three code examples for different types of blocks in the `src/Plugin/Block` directory. We will be examining the simplest use case which creates an empty block with nothing in it. The new block is named `ExampleEmptyBlock`, and it will reside in a class file called `ExampleEmptyBlock.php`. The following portion of code is all that is necessary to create an empty block in which you may place anything you wish:

<!-- @section -->

## Download this file.

```php
<?php
/**
 * @file
 * Contains \Drupal\block_example\Plugin\Block\ExampleEmptyBlock.
 */

namespace Drupal\block_example\Plugin\Block;
use Drupal\Core\Block\BlockBase;

/**
 * Provides a 'Example: empty block' block.
 *
 * @Block(
 *   id="example_empty",
 *   admin_label = @Translation("Example: empty block")
 * )
 */
class ExampleEmptyBlock extends BlockBase {

  /**
   * {@inheritdoc}
   */
  public function build() {
    return [
      '#type' => 'markup',
      '#markup' => '',
    ];
  }

}
```

[view raw](https://gist.github.com/acquialibrary/196dca7dd1edbbf32bbe/raw/0e457a5e87a54e7899640b424886070e8a8e26d5/ExampleEmptyBlock.php) [ExampleEmptyBlock.php](https://gist.github.com/acquialibrary/196dca7dd1edbbf32bbe#file-exampleemptyblock-php) hosted with ❤ by [GitHub](https://github.com)

#### Classes and annotations

All class files must begin with defining the namespace for the class. After creating the [namespace](https://docs.acquia.com/articles/php.net/manual/en/language.namespaces.php) for the class, we extend the Drupal core `BlockBase` class to create the `ExampleEmptyBlock` class. With that, we can use Drupal PHP Annotations, introduced with Drupal 8.

Generic PHP Annotations are self contained comment text which allows the auto-discovery of metadata and registration information regarding a class file. The example above has annotations specifically for **Example: empty block**, located in 2nd set of comment text. By utilizing annotations we notify Drupal that we want to register a new block type (`@Block`) with the id of `example_empty` and the **admin_label** of `Example: empty block`, which is passed through the translation system prior to being used.

The `{@inheritdoc}` annotation can be used in a class when you are overriding a class function. This annotation tells the system to use the parent class's annotation.

> **Note**:
Drupal will not discover the blocks unless they are properly annotated.

This extends the `BlockBase` class into our `ExampleEmptyBlock`, inside which we implement one method: `build()`. This is one of the most common ad important methods, as it returns a renderable array which the block will print out.

Clear the caches, navigate to the layout configuration page **`/admin/structure/block`**, and although in Drupal 7 you would be able to see your new block under the disabled section, in Drupal 8 you have to click **Place Block** to see your new block as a choice:

<!-- @task, "text" : "Create and place your first block as described above." -->
