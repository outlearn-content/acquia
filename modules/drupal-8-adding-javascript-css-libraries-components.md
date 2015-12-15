<!--
{
"name" : "drupal-8-adding-javascript-css-libraries-components",
"version" : "0.0.1",
"title" : "Lesson 9.3 - Adding JavaScript / CSS / Libraries to components",
"description" : "Adding JavaScript / CSS / Libraries to components",
"freshnessDate" : 2015-12-11,
"homepage" : "https://docs.acquia.com/articles/drupal-8-adding-javascript-css-libraries-components",
"canonicalSource" : "https://docs.acquia.com/articles/drupal-8-adding-javascript-css-libraries-components",
"license" : "CC BY-SA"
}
-->

<!-- @section -->

# Overview

In Drupal 7, the recommended way to add JavaScript or CSS to a component was through the use of `#attached` in a render array. Many developers also used [`drupal_add_js`](https://api.drupal.org/api/drupal/includes%21common.inc/function/drupal_add_js/7), [`drupal_add_css`](https://api.drupal.org/api/drupal/includes%21common.inc/function/drupal_add_css/7), and [`drupal_add_library`](https://api.drupal.org/api/drupal/includes%21common.inc/function/drupal_add_library/7). In Drupal 8, only [`#attached`](https://api.drupal.org/api/drupal/8/search/attached) is supported. Because of this, it is recommended that all JavaScript and CSS be defined as a library which can then be attached to a render array.

### Creating a Library

A module's `libraries.yml` file defines libraries which include the CSS and JavaScript files necessary to render your page. This file specifies the location of each of the components of the library.

Following is an example of `theme_example.libraries.yml`. In this example library, we have defined a single library, `sample-library`. It contains both JavaScript and CSS and is dependent on several core libraries. In Drupal 7, we assumed that jQuery, `jQuery.once`, and settings were available. In Drupal 8, you must define these as dependencies because they are not loaded by default.

```
sample_library:
css:
# For some reason, you need to put css under 'theme'.
theme:
css/example.css: {}
js:
# For some reason, you need to put the js directly under 'js'.
js/example.js: {}
dependencies:
# jQuery is not included by default, so we add it as a dependency
- core/jquery
# We are also going to use jQuery.once so that code doesn't trigger multiple times.
- core/jquery.once
# drupal and drupalSettings are not included by default either.
- core/drupal
```

Here two examples CSS statements that can be used with the library:

```css
myElement {
border: 3px solid purple;
border-radius: 3px;
padding: 1em;
margin: 1em;
background-color: #EEE;
}

.myElement .randomNumber {
color: purple;
font-weight: bold;
font-size: 1.2em;
}
```

### Attaching a library to a component

Now that we have a library, we can reference it in a render array.

```
$output = [
'#markup' => '<div>hello world</div>',
'#attached => [
'library' => [
'theme_example/sample_library',
],
],
];
```

We can also define a setting that will be passed to JavaScript.

```
$output = [
'#markup' => '<div>hello world</div>',
'#attached => [
// This setting will be sent to drupalSettings.sampleLibrary.mySetting.
'drupalSettings' => [
'sampleLibrary' => [
'mySetting' => 'hello world',
],
],
],
];
```

<!-- @section -->

## Update the Plugin #pre_render function

Finally, update the file `src/Element/MyElement.php`:

[Download this file](https://gist.github.com/acquialibrary/571b269d5c6d56afc115/archive/962f52db459fe1952df9411807976b7125a2cccf.zip).

```php
 /**
 * Prepare the render array for the template.
 */
 public static function preRenderMyElement($element) {
 // Create a link render array using our #label.
 $element['link'] = [
 '#type' => 'link',
 '#title' => $element['#label'],
 '#url' => Url::fromUri('http://www.drupal.org'),  ];

 // Create a description render array using #description.
 $element['description'] = [
 '#markup' => $element['#description']
 ];

 $element['pre_render_addition'] = [
 '#markup' => 'Additional text.'
 ];

 // Create a variable.
 $element['#random_number'] = rand(0,100);

 // Add the library
 $element['#attached'] = [
 'library' => [
 'theme_example/sample_library',
 ],
 'drupalSettings' => [
 'sampleLibrary' => [
 'mySetting' => 'hello world',
 ],
 ],
 ];

 return $element;
 }
```

[view raw](https://gist.github.com/acquialibrary/571b269d5c6d56afc115/raw/962f52db459fe1952df9411807976b7125a2cccf/MyElement.php) [MyElement.php](https://gist.github.com/acquialibrary/571b269d5c6d56afc115#file-myelement-php) hosted with ❤ by [GitHub](https://github.com)

## JavaScript patterns in Drupal 8

The JavaScript best practices are largely the same as in Drupal 7\. We wrap the jQuery object in a closure and attach functions to Drupal.behaviors. The major change is that the settings variable is now `drupalSettings`.

```
(function ($) {
Drupal.behaviors.themeExample = {
attach: function (context, settings) {
// jQuery once ensures that code does not run after an AJAX or other function that calls Drupal.attachBehaviors().
$('body').once('themeExample').each(function () {
// We have console.log() here to make it easy to see that this code is functioning. You should never use console.log() on production code!
if (typeof console.log === 'function') {
console.log('My Setting: ' + settings.sampleLibrary.mySetting);
}
});
if (typeof console.log === 'function') {
console.log('This will run every time Drupal.attachBehaviors is run.');
}
$('body').once('themeExampleModifyDOM').each(function () {
// Add an element to the body.
$('body').append('<div class="example">Hello World</div>');
// Tell Drupal that we modified the DOM.
Drupal.attachBehaviors();
});
}
};
})(jQuery);
```

## Testing that the library is working

You can go to our path `/examples/theme-example/simple` and then use the Chrome or Firefox debuggers to look at the console log. You will see messages produced by our JavaScript library.

### Files

These are the files you should have at the end of this exercise:

*   `theme_example.info.yml`
*   `theme_example.routing.yml`
*   `theme_example.libraries.yml`
*   `theme_example.module`
*   `cs/example.css`
*   `js/example.js`
*   `src/Element/MyElement.php`
*   `src/Controller/ThemeExampleController.php`
*   `templates/my-element.html.twig`
