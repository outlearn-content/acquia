<!--
{
"name" : "drupal-8-phpstorm-strategies",
"version" : "0.0.1",
"title" : "Lesson 11.5 - PhpStorm strategies",
"description" : "PhpStorm strategies",
"freshnessDate" : 2015-12-11,
"homepage" : "https://docs.acquia.com/articles/drupal-8-phpstorm-strategies",
"canonicalSource" : "https://docs.acquia.com/articles/drupal-8-phpstorm-strategies",
"license" : "CC BY-SA"
}
-->

<!-- @section -->

# Overview

PhpStorm provides tools that help in using dependency injection patterns.

<!-- @section -->

## Expanded structure view

In Drupal 8, functionality for components is often spread across many files. A class usually extends another class which may extend a class itself. It often also uses an interface. It's a good practice to use existing code as examples, but doing so doesn't give you the full picture.

The Structure View is useful for browsing the available methods and properties of classes, but by default, you cannot see methods that are inherited from other classes. There is a button which will enable this. From there, you can see if by extending a class, you get certain methods that you want to use. You can then click on them from that view and copy and paste the implementation that you want to override.

Open the view with **CMD + 7** (Mac) or **View > Tool Windows > Structure**. If you press CMD twice, you will also see a menu on the border of the editor that lets you select all of these views.

With the view open, toggle this button:

![phpstorm-structure.jpg](https://raw.githubusercontent.com/outlearn-content/acquia/master/assets/phpstorm-structure.jpg)

The gray methods are those inherited and the black ones are those in the current class. You can show the file list with **CMD + 1**.

<!-- @section -->

## Finding a service class from a service name

You will often see services referred to by their name, but finding the service may be more challenging. We can use PhpStorm's search in files system to find this quite easily.

For example, you have `arguments: ['@current_user']`, which is defined as:

```
$current_user = \Drupal::service('current_user');
```

In these cases, we know that the service is called `current_user`, but it has no prefix, so it's unclear what `service.yml` file it is defined in.

Open the Find in Files using **CMD + Shift + F**.

![find-service-class.jpg](https://raw.githubusercontent.com/outlearn-content/acquia/master/assets/find-service-class.jpg)

By searching in only `*services.yml` files, and by using **service-name:** with the colon, we will only return one search result.

```yml
current_user:
  class: Drupal\Core\Session\AccountProxy
```

From here, we know that there is a class `AccountProxy` and that it can be referred to by `Drupal\Core\Session\AccountProxy`. We will use that pattern when we use dependency injection. We can also search for this class in PhpStorm using **Search Everywhere**.

Press **Shift** twice and then search for AccountProxy. One of the results will be listed under **Class**. Selecting this will take you to the class definition and you can see how it is defined.

All classes should have a comment at the top that also shows how it can be referenced:

```
/**
* @file
* Contains \Drupal\Core\Session\AccountProxy.
*/
```

<!-- @section -->

## Navigating to classes in PhpStorm from code use

If we know the name of a class in PhpStorm, we can easily navigate to that class by holding **CMD** and clicking on the class name. In this example, we can navigate to the AccountInterface class by clicking on it.

```
public function setAccount(AccountInterface $account) {}
```

<!-- @task, "text" : "Explore the different ways PhpStorm can help you in dealing with dependency injection and other common patterns." -->
