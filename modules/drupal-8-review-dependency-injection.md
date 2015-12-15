<!--
{
"name" : "drupal-8-review-dependency-injection",
"version" : "0.0.1",
"title" : "Lesson 11.1 - Review of Dependency Injection",
"description" : "Review of Dependency Injection",
"freshnessDate" : 2015-12-11,
"homepage" : "https://docs.acquia.com/articles/drupal-8-review-dependency-injection",
"canonicalSource" : "https://docs.acquia.com/articles/drupal-8-review-dependency-injection",
"license" : "CC BY-SA"
}
-->

<!-- @section -->

# Overview

In earlier lessons, we introduced a few patterns including services, controllers, and plugins. In these patterns, we demonstrated how to use dependency injection. In this review, we will provide simple examples and discuss how to use [PhpStorm](http://www.jetbrains.com) to help use these patterns.

<!-- @section -->

## Setting up the basics of our example module

We'll create the example module `di_example` that contains two services, one controller, and one block plugin. We will first create the basics that don't involve dependency injection: the `.info.yml` and `.routing.yml` files, and a service to use. The service does not depend on any other services.

#### di_example.info.yml

```
name: Dependency Injection Example
type: module
description: Examples on how to use dependency injection
core: 8.x
package: Examples
```

#### di_example.routing.yml

In this file, we'll have a controller to create one page that provides a printed message we are calling a `conversation`. This conversation will be about the mood of a user.

```
di_example.conversation_mood:
path: '/examples/conversation/mood'
defaults:
_controller: '\Drupal\di_example\Controller\DIController::conversationAboutMood'
requirements:
_access: 'TRUE'
```

#### di_example.services.yml

This service is used to inject into our other components. It provides a mood through a random mood ring system. In it, we will provide a list of moods and a `getMood()` function that returns a random mood.

```
services:
# A service that will let us get a mood.
di_example.mood_ring:
class: Drupal\di_example\DIMoodRing
```

This file provides the code to create the mood (`src/DIMoodRing.php`):

[Download this file](https://gist.github.com/acquialibrary/88251c080bee4dc98719/archive/aae64579c52bf44101b59370714edd41e4e9c4f7.zip).

```php
 <?php
 /**
 * @file Contains \Drupal\di_example\DIMoodRing
 */

 namespace Drupal\di_example;

 /**
 * A Service for reading a mood from a mood ring.
 *
 * This service does not have any dependencies.
 */
 class DIMoodRing {
 protected $moods = [
 0 => 'Very Sad',
 1 => 'Sad',
 2 => 'So-so',
 3 => 'Happy',
 4 => 'Very Happy',
 ];

 /**
 * Returns a string that tells the user's current mood.
 *
 * @return string
 */
 public function getMood() {
 return $this->moods[rand(0,4)];
 }

 }
```
[view raw](https://gist.github.com/acquialibrary/88251c080bee4dc98719/raw/aae64579c52bf44101b59370714edd41e4e9c4f7/DIMoodRing.php) [DIMoodRing.php](https://gist.github.com/acquialibrary/88251c080bee4dc98719#file-dimoodring-php) hosted with ❤ by [GitHub](https://github.com)
