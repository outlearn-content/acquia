<!--
{
"name" : "drupal-8-entities-content-entities-and-configuration-entities",
"version" : "0.0.1",
"title" : "Lesson 4.1 - Entities, Content Entities, and Configuration Entities",
"description" : "Entities, Content Entities, and Configuration Entities",
"freshnessDate" : 2015-12-11,
"homepage" : "https://docs.acquia.com/articles/drupal-8-entities-content-entities-and-configuration-entities",
"canonicalSource" : "https://docs.acquia.com/articles/drupal-8-entities-content-entities-and-configuration-entities",
"license" : "CC BY-SA"
}
-->

<!-- @section -->

# Overview

Drupal 7 introduced the key concept of entities to Drupal, which had their roots in object-oriented programming. While the concept of "all things" in Drupal 7 existing as entities was the goal, only a few content entity types shipped with core: nodes, comments, terms, and users.

A contributed module was necessary to further extend the entity API of Drupal 7 core and provide a unified method for handling entities and their properties. This module also included an entity CRUD (Create, Read, Update and Delete) controller (previously missing from core), with the purpose of aiding in the creation of new entity types.

Prior to Drupal 7, only nodes were fieldable; however, with modeling core parts of the Drupal framework as entities you were then able to add fields to comments, terms, and users.

Now, with the advent of Drupal 8, Drupal has the ability to generate entities and fields through the Configuration Management Initiative (which was covered in [Lesson 5.3](https://docs.acquia.com/articles/drupal-8-configuration-forms-and-cmi)).

<!-- @section -->

## Entities and entity types

An _entity_, as defined for Drupal 7 in [An introduction to entities](https://www.drupal.org/node/1261744), is an object that has a unique and separate existence. In Drupal 8, they are additionally defined as a specifically-typed object, where each entity is an instance of an entity type, and acts as a useful abstraction. This allows developers to group fields in a repeatable fashion. A group of [content types](https://docs.acquia.com/articles/content-type-definition) are a subset of the more inclusive group of entity types available in Drupal 8, with entity types being a more generic term that is meant to encompass the variety of grouped abstractions available to developers.

Some examples of common entity types are:

*   Nodes (content types)
*   Comments
*   Taxonomy terms
*   User profiles
*   Configuration entities

### Another way of looking at entity types

Each entity type can have different versions of that type called _bundles_. The node entity type, for example, calls its bundles _content types_. Nodes are the only entity type that gives a specific name to its bundle as its a colloquial name used in the web interface. Another way of looking at it is that entities that have bundles and nodes (which are an entity) have chosen to have a synonym for the bundle called content type. By specializing our entities we get specialized display modes, forms, distinct theming, and other parts of the Drupal application and system encourage you to specialize into multiple entity bundle types (or entity types).

<!-- @section -->

## Configuration entities

Drupal 8 introduces the concept of [configuration entities](https://www.drupal.org/node/1818734) (or configurables) that leverage the entity system's CRUD and pluggable storage functionality. This differs from plain configuration in that they are typically user-created, and often need to fire and respond to hooks. Examples of potential configuration entities include:

*   Content types
*   Views
*   Taxonomy vocabularies
*   Contact forms
*   Image styles

To define a new configuration entity type, you'll extend the [`ConfigEntityBase`](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Config%21Entity%21ConfigEntityBase.php/class/ConfigEntityBase/8) class and define an interface that extends [`ConfigEntityInterface`](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Config%21Entity%21ConfigEntityInterface.php/interface/ConfigEntityInterface/8).

Configuration entities do not currently support revisions ([RevisionableInterface](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Entity%21RevisionableInterface.php/interface/RevisionableInterface/8)), the Entity Translation API ([TranslatableInterface](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21TypedData%21TranslatableInterface.php/interface/TranslatableInterface/8) — but they can be translated through the config translation API), nor are they typed data ([TypedDataInterface](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21TypedData%21TypedDataInterface.php/interface/TypedDataInterface/8)), which means they can't have fields.

<!-- @section -->

## Creating content types

The easiest way for most users to create a content type is through the user interface. The page [Using an entity reference](https://docs.acquia.com/articles/using-entity-reference) describes the process of how to create a content type, starting at _step 5_.

Drupal.org also contains an [example](https://www.drupal.org/node/2192175) of how to create a content entity.

### Creating a content type with Console

An alternate approach to creating an entity file is using the [Console](https://www.drupal.org/project/console) project. Console can:

*   Generate `module` and `info` files.
*   Generate a PSR-4 compliant directory structure for a module.
*   Register routes on `YML` files and map to controller and form PHP Classes.
*   Create classes adding namespaces, uses and also the `extend` and `implements` keywords when required.
*   Support adding services using Dependency Injection on class generation.

If you use the Console project, the instructions involves installing a PHP Archive or `.phar` file. The `phar` extension provides a mechanism for putting entire PHP applications into a single file for improved distribution and installation. Using the instructions, you may encounter permissions issues. For instance, when running `drupal generate::module`, the process recently failed and resulted in a warning of `Notice: The "phar.readonly" setting needs to be off to create Phars`. If you encounter this or any other issue during the installation process, we recommend using the Console project issue queue.
