<!--
{
"name" : "drupal-8-advanced-render-array-patterns",
"version" : "0.0.1",
"title" : "Lesson 9.4 - Advanced render array patterns",
"description" : "Advanced render array patterns",
"freshnessDate" : 2015-12-11,
"homepage" : "https://docs.acquia.com/articles/drupal-8-advanced-render-array-patterns",
"canonicalSource" : "https://docs.acquia.com/articles/drupal-8-advanced-render-array-patterns",
"license" : "CC BY-SA"
}
-->

<!-- @section -->

# Overview

<!-- @section -->

## HTML attributes

You can set HTML attributes (such as IDs and classes) as part of the render array. These will then be passed to the Twig template which allows a themer to ad to or override the default. To do this, add the `#attributes` variable to the render array. The keys of this array are the attributes and the values are their values.

```
$element = [
'#type' => 'my_element',
'#config_one' => 'blue',
];

$element['#attributes'] = [
'id' => 'my_blue_element_1',
'class' => ['my-element','blue'],
];
```

This allows for the following type of preprocess hook — the `$variables['attributes']` was created by `template_process()` in `theme.inc` which is run on all render arrays. This creates what appears to be duplication since the original values are also in `$variables['element']['#attributes']`, but those original values are not used any more:

```
function hook_preprocess_my_element(&$variables) {
$variables['attributes']['id'] = 'my_blue_element_altered_id';
}
```

You can then use these attributes in a Twig template:

```
<div {{ attributes }}>
{{ element }}
</div>
```

After the preprocess functions act on the `attributes` variables, they are converted into an object that allows them to be easily modified and printed in Twig. This is done in `render()` in `ThemeManager.php`.

<!-- @section -->

## Wrappers (#theme_wrapper) and containers

You can wrap your element with a div and add classes to this using the preceding attribute pattern. This can be done by adding a `#theme_wrappers` to a render array. The most common type of wrapper is a container which prints the attributes.

```
$element = [
'#type' => 'my_element',
'#config_one' => 'blue',
'#theme_wrappers' => [
'container' => [
'#attributes' => [
'class' => ['my-wrapper-class']
],
],
],
];
```

With this, you can use the `container.html.twig` file which uses the pattern shown in the attributes example. It also does some special handling for forms.

```
{%
set classes = [
has_parent ? 'js-form-wrapper',
has_parent ? 'form-wrapper',
]
%}
<div{{ attributes.addClass(classes) }}>{{ children }}</div>
```

<!-- @section -->

## Exploring the element render array flow

Drupal 7 had support for a branching system of ways to render an element. Drupal 8 hopes to simplify this, but much of the old system remains. In this lesson, we followed one path. This pattern is an attempt to pick one path through this system based on the available recommendations and use in core. Drupal core is still a mix and uses all of the paths.

### Element Plugin / pre_render / Twig pattern

[![d8RecommendedRenderPath.png](https://raw.githubusercontent.com/outlearn-content/acquia/master/assets/d8RecommendedRenderPath.png)](https://raw.githubusercontent.com/outlearn-content/acquia/master/assets/d8RecommendedRenderPath.png)

The reasoning behind picking this pattern is for consistency with the rest of Drupal 8 while maintaining flexibility. To stick with the object oriented pattern that uses plugins, we will define all of our elements as plugins. We have contained all of our element information in a single plugin file which is consistent with how plugins and classes are structured in Drupal 8\. All elements allow other modules to override the elements before they get to the template using `hook_preprocess_hook`, so we've maintained the ability to use overrides.

### Differences from core

The main difference between this pattern and core is that most of core also uses `template_preprocess_hook` which seems redundant with `#pre_render`. Some of core uses both because of some quirks with the systems used. Working around those quirks may add polish, but comes at the cost of complexity and separating logic among several places.
