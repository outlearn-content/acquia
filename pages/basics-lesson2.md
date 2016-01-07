# Lesson 2: The Next Most Common Components: Blocks and Forms

We have addressed the initial pieces necessary to build a module so we will move onto the next most common components in Drupal 8: _blocks_ and _forms_.

The [Block API](https://www.drupal.org/developing/api/8/block_api) in Drupal 8 has been changed such that blocks are now both [plugins](https://www.drupal.org/developing/api/8/plugins) and entities. Blocks in Drupal 8 consist of two separate API structures: the Block Plugin API, which is a stand-alone reusable API, and the Block Entity API which is a Drupal 8 specific use case of block placement and visibility control. This means that Blocks are now fieldable entities and work with the new Drupal 8 Plugin system ([change record here](https://www.drupal.org/node/1880620)). That means you can create new bundles, add new fields, and view them in different ways.

This change allows developers to use the same block in multiple regions which had been a limitation in versions of Drupal prior to Drupal 8\. In essence, it allows you to create multiple instances of the same block. You are also able to do this when you add a block to a region through the UI and not conflict with another use of the block in another region.

For this lesson we will be using the **block_example** module from the [Examples](http://drupal.org/project/examples) project.

## In this lesson...

*   [Assemble the module - examples/block_example](https://docs.acquia.com/articles/drupal-8-blocks-configuration-and-forms#create).
*   [Learn about the introduction of annotations in Drupal 8](https://docs.acquia.com/articles/drupal-8-blocks-configuration-and-forms#classes).
*   Explore how to add blocks using the Drupal GUI.
*   [Learn about the Form API (or FormsAPI)](https://docs.acquia.com/articles/creating-forms-drupal-8).
*   Explore how to build using the FormsAPI.
*   Explore how to build a block and a page with a form.

We will use the code organization from the [first page](https://docs.acquia.com/articles/building-drupal-8-modules#code) of this chapter.
