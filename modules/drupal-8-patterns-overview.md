<!--
{
"name" : "drupal-8-patterns-overview",
"version" : "0.0.1",
"title" : "Lesson 9.5 - Patterns overview",
"description" : "Patterns overview",
"freshnessDate" : 2015-12-11,
"homepage" : "https://docs.acquia.com/articles/drupal-8-patterns-overview",
"canonicalSource" : "https://docs.acquia.com/articles/drupal-8-patterns-overview",
"license" : "CC BY-SA"
}
-->

<!-- @section -->

# Overview of the available patterns

[![D8RenderArrayEntireMap.png](https://raw.githubusercontent.com/outlearn-content/acquia/master/assets/D8RenderArrayEntireMap.png)](https://raw.githubusercontent.com/outlearn-content/acquia/master/assets/D8RenderArrayEntireMap.png)

*   **First of many branches**

    The render array first checks if there is `#markup`, if so, we just render the markup.

*   **Determining how to deal with complex elements**

    If the render array is more complex, it goes to the theme to find out how to deal with the render array. The system checks for `#type` and `#theme`. All element types must be defined in a `hook_theme()` function in a module.

*   **`#type` and `#theme` difference**

    `#type` is the object oriented pattern for defining elements while `#theme` is a non-object oriented approach. Unfortunately, these two paths are tangled. The `#type` pattern starts by defining settings for the element. If we use `#theme`, `hook_theme()` will be used to define defaults later and these will be merged.

*   **Render element and variables difference**

    A render array must have either a setting for render element or for variables. The main difference is the way the template variables will look. For render element, all variables are under a single element, and for variables they are all at the top level.

```
{# Render Element #}
{{ element }}
{{ element.field_one }}
{{ element.field_two }}

{# Variables #}
{{ field_one }}
{{ field_two }}
```

While in theory, this makes sense, you might have a render array that starts as a group of other elements or one that starts as a group of variables. In reality, most elements are a mix and the way people have worked around these differences is very inconsistent.

In the pattern we have used in this lesson, you end up with:

```
{# Render Element #}
{{ element }}
{{ element.field_one }}
{{ element.field_two }}
{{ element['#variable'] }}
```

It's not ideal, but it seems simple enough. You can print the entire element, a field, or a variable. This also keeps the pattern of variables all starting with `#`.

*   **Process functions**

    After the defaults are gathered, we may have defined different process functions like `#pre_render` (part of `#type`) or `template_preprocess_hook` (part of `#theme`). Some elements use both in which case `#pre_render happens` before `#template_preprocess_hook`. Aside from timing, there isn't much difference in the functions.

*   **Template or render function**

    Drupal 8 uses Twig — most of the time. In rare cases, a function does the job of the template and returns markup. It's good to be aware that this is out there, but it should probably be avoided going forward.

<!-- @task, "text" : "Make sure you have understood the render array patterns." -->
