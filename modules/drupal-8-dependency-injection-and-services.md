<!--
{
"name" : "drupal-8-dependency-injection-and-services",
"version" : "0.0.1",
"title" : "Lesson 11.2 - Dependency injection and services",
"description" : "TBD",
"freshnessDate" : 2015-12-11,
"homepage" : "https://docs.acquia.com/articles/drupal-8-dependency-injection-and-services",
"canonicalSource" : "https://docs.acquia.com/articles/drupal-8-dependency-injection-and-services",
"license" : "CC BY-SA"
}
-->

<!-- @section -->

# Overview

<!-- @section -->

## Injection in services

When we defined our service above we mapped the service name to the service class. The service is completely independent when created this way. If we want to create a service that uses other services, we must inject its dependencies by defining these dependencies in the `services.yml` file. In these updated definitions, we have created a new `di_example.talk` service that will use both the `current_user` and `di_example.mood_ring` services.

```
services:
# A service that will let us get a mood.
di_example.mood_ring:
class: Drupal\di_example\DIMoodRing

# A service that will let us talk to users.
di_example.talk:
class: Drupal\di_example\DITalk
# We can inject the current user and mood ring services into this service.
arguments: ['@current_user', '@di_example.mood_ring']
```

Dependency injection for services starts by adding arguments to the `services.yml` file. Each service name is proceeded by `@`. Now that we have told Drupal 8 that we need these two additional services, we can store them the current service.

This service provides a single `getResponseToMood()` method which will return a string message. This message uses the two injected services. In our new service, we first build the `__construct()` method which will take our injected services and store them as class properties. This uses the public function `__construct(AccountProxy $CurrentUser, DIMoodRing $DIMoodRing) {`.

#### src/DITalk.php

[Download this file](https://gist.github.com/acquialibrary/28011356948e115a8028/archive/61c34770a76892aebe2efd8dcae6db71406f2802.zip).

```php
 <?php
 /**
 * @file Contains \Drupal\di_example\DITalk
 */

 namespace Drupal\di_example;

 use Drupal\Core\Session\AccountProxy;

 /**
 * A service that provides a system for getting response message.
 */
 class DITalk {
 protected $responsesToMood = [
 'Very Sad' => 'I hope you feel better.',
 'Sad' => 'are you ok?',
 'So-so' => 'good morning.',
 'Happy' => 'what\'s Up?',
 'Very Happy' => 'you seem happy today!',
 ];

 /**
 * @var \Drupal\di_example\DIMoodRing
 */
 protected $dIMoodRing;

 /**
 * @var \Drupal\Core\Session\AccountProxy
 */
 protected $currentUser;

 /**
 * We will inject our two services and store them for use in our service methods.
 *
 * @param \Drupal\Core\Session\AccountProxy $CurrentUser
 * @param \Drupal\di_example\DIMoodRing $DIMoodRing
 */
 public function __construct(AccountProxy $CurrentUser, DIMoodRing $DIMoodRing) {
 $this->currentUser = $CurrentUser;
 $this->dIMoodRing = $DIMoodRing;
 }

 /**
 * Returns a string that is a message to a user.
 *
 * @return string
 */
 public function getResponseToMood() {
 // We can user our services and their defined methods.
 $username = $this->currentUser->getUsername();
 $mood = $this->dIMoodRing->getMood();

 // We build a message to return.
 return $username . ', ' . $this->responsesToMood[$mood];
 }

 }

```
[view raw](https://gist.github.com/acquialibrary/28011356948e115a8028/raw/61c34770a76892aebe2efd8dcae6db71406f2802/DITalk.php) [DITalk.php](https://gist.github.com/acquialibrary/28011356948e115a8028#file-ditalk-php) hosted with ❤ by [GitHub](https://github.com)

When PhpStorm recognizes the class names, it will automatically add use statements if needed. One is needed for AccountProxy because it is using a different namespace, but one is not needed for DIMoodRing because it is using our same module namespace.

After you complete the `__construct()` function, add a comment with `/**`. PhpStorm will automatically provide documentation on the parameters and link to their full class paths. This will be helpful for autocompletion. If you define these class properties and similarly add comments, PhpStorm will provide the documentation for the property types. If for some reason PhpStorm does not do this, it's recommended to fill this out so that autocompletion is available.
