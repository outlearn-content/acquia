<!--
{
"name" : "creating-forms-drupal-8",
"version" : "0.0.1",
"title" : "Lesson 2.2 - Forms",
"description" : "Forms",
"freshnessDate" : 2015-12-11,
"homepage" : "https://docs.acquia.com/articles/creating-forms-drupal-8",
"canonicalSource" : "https://docs.acquia.com/articles/creating-forms-drupal-8",
"license" : "CC BY-SA"
}
-->

<!-- @section -->

# Overview

<!-- @section -->  

## Drupal 8 forms

Now that we can create a new block type for repeatable use from the block layout page, it's time to learn how to leverage the API for adding a configuration form. This new config form will allow you to use the UI to set a text string, which will be shown as the block's content.

The extra configuration form elements are handled in this way:

*   using a `blockForm()` method, where you can add elements to the received $form array.
*   using a `blockSubmit()` method, where the received data from the form is saved.
*   using a `defaultConfiguration()` method, which defines the default configuration values you will see the first time you see the form.

### Create a block for the form

First, we'll create a single block to contain the form:

1.  Create the file `block_example/src/Plugin/Block/ExampleConfigurableTextBlock.php`.
2.  Paste the following code into the new file:

    [Download this file](https://gist.github.com/acquialibrary/a81b0d11478b78f177ac/archive/0383d8f543b0d3a666b9f4a9581d99d2e2191f1d.zip).

```php
 <?php
 /**
 * @file
 * Contains \Drupal\block_example\Plugin\Block\ExampleConfigurableTextBlock.
 */
 namespace Drupal\block_example\Plugin\Block;
 use Drupal\Core\Block\Annotation\Block;
 use Drupal\Core\Block\BlockBase;
 use Drupal\Core\Annotation\Translation;
 use Drupal\Core\Form\FormStateInterface;
 /**
 * Provides a 'Example: configurable text string' block.
 *
 * Drupal\block\BlockBase gives us a very useful set of basic functionality for
 * this configurable block. We can just fill in a few of the blanks with
 * defaultConfiguration(), blockForm(), blockSubmit(), and build().
 *
 * @Block(
 * id="example_configurable_text",
 * admin_label = @Translation("Title of first block (example_configurable_text)"),
 * category = @Translation("Example")
 * )

*/
  class ExampleConfigurableTextBlock extends BlockBase {
 /**

 * {@inheritdoc}
 */
 public function defaultConfiguration() {
 return [
 'block_example_string' => $this->t('A default value. This block was created at %time', ['%time' => date('c')]),
 ];
 }
 /**
 * {@inheritdoc}
 */
 public function blockForm($form, FormStateInterface $form_state) {
 $form['block_example_string_text'] = [
 '#type' => 'textfield',
 '#title' => $this->t('Block contents'),
 '#size' => 60,
 '#description' => $this->t('This text will appear in the example block.'),
 '#default_value' => $this->configuration['block_example_string'],
 ];
 return $form;
 }
 /**
 * {@inheritdoc}
 */
 public function blockSubmit($form, FormStateInterface $form_state) {
 $this->configuration['block_example_string']
 = $form_state->getValue('block_example_string_text');
 }
 /**
 * {@inheritdoc}
 */
 public function build() {
 return [
 '#type' => 'markup',
 '#markup' => $this->configuration['block_example_string'],
 ];
 }
 }
```

[view raw](https://gist.github.com/acquialibrary/a81b0d11478b78f177ac/raw/0383d8f543b0d3a666b9f4a9581d99d2e2191f1d/ExampleConfigurableTextBlock.php) [ExampleConfigurableTextBlock.php](https://gist.github.com/acquialibrary/a81b0d11478b78f177ac#file-exampleconfigurabletextblock-php) hosted with ❤ by [GitHub](https://github.com)

3.   Rebuild the Drupal cache to display the block, using the following command:

  `drush cr`

Understanding the new features and functions

*   **Avoiding global functions**

    In Drupal 8, each piece of code is self contained whenever possible. This means that most functions are contained in classes instead of at the global scope, so use of functions like [`l()`](https://api.drupal.org/api/drupal/includes%21common.inc/function/l/7) or [`t()`](https://api.drupal.org/api/drupal/includes%21bootstrap.inc/function/t/7), which are common in Drupal 7, are no longer recommended. Functionality is provided to classes through a system called [dependency injection](https://www.drupal.org/node/2133171), which will be discussed in later lessons.

*   **Extending base classes**

    Most of the components like blocks and controllers will use a pattern where we extend a base class. The base class does most of the work in pulling in common functionality like `t()`. Because of this, whenever possible, it is helpful to extend base classes.

    In our example, we are creating a block which extends the existing `BlockBase` class which created a class variable `$this->t()` that does what `t()` did in Drupal 7\. This gives us access to the `t()` function as `$this->t().`

*   **Traits**

    A trait is essentially an include which we commonly used in Drupal 7\. It provides additional methods that can be included in any class. By including the `StringTranslationTrait` in `BlockBase`, we can then use any methods in there as if they were part of the `BlockBase` class.

    `$this->t()` is defined in `StringTranslationTrait`. `BlockBase` extends a number of classes which extends `ContextAwarePluginBase`. This `ContextAwarePluginBase` class uses the `StringTranslationTrait` which adds `$this->t()`. This is done by adding the line use `StringTranslationTrait;`.

    In our example above, when we call $this->t(), PHP searches for the function in the following order:

    *   _Does our class have a `t()` method?_ No it does not.
    *   _Does the class we're extending (`BlockBase`) have a `t()` method?_ Not natively.
    *   _Does the class `BlockBase` is extending (`ContextAwarePluginBase`) have a `t()` method?_ Not natively.
    *   _Does the class we're extending include a trait that provides the `t()` method to `ContextAwarePluginBase`?_ Yes, the `StringTranslationTrait` is included in `ContextAwarePluginBase`.

    This inheritance tree allows us to use `$this->t(`) as normal to automatically get the string translation functionality.

### Create a form

Now to create a simple form. Unfortunately, the Examples module doesn't presently contain a suitable example of a form so we are leveraging the `page_example` module of the Examples project and adding to it. This example contains a simple form definition.

It creates an email field, which is a new form element in Drupal 8\. The goal is for a user to complete it. By default, Drupal 8 validates whether the value entered is an email address. The code also uses a validation function to ensure the email address entered is a .com and if not it sets a form error on the field. The submit handler prints a message to the screen.

[Download this file](https://gist.github.com/acquialibrary/d930049031773997317c/archive/3eb25764b6a53d3e0abb70760d534b73367b5ac9.zip).

```php
 <?php
 /**
 * @file
 * Contains \Drupal\page_example\Form\PageExampleForm.
 */
 namespace Drupal\page_example\Form;
 use Drupal\Core\Form\FormBase;
 use Drupal\Core\Form\FormStateInterface;
 class PageExampleForm extends FormBase {
 /**
 * {@inheritdoc}.
 */
 public function getFormId() {
 return 'page_example_form';
 }
 /**
 * {@inheritdoc}.
 */
 public function buildForm(array $form, FormStateInterface $form_state) {
 $form['email'] = [
 '#type' => 'email',
 '#title' => $this->t('Your .com email address.')
 ];
 $form['show'] = [
 '#type' => 'submit',
 '#value' => $this->t('Submit'),
 ];
 return $form;
 }
 /**
 * {@inheritdoc}
 */
 public function validateForm(array &$form, FormStateInterface $form_state) {
 if (strpos($form_state->getValue('email'), '.com') === FALSE) {
 $form_state->setErrorByName('email', $this->t('This is not a .com email address.'));
 }
 }
 /**
 * {@inheritdoc}
 */
 public function submitForm(array &$form, FormStateInterface $form_state) {
 drupal_set_message($this->t('Your email address is @email', ['@email' => $form_state->getValue('email')]));
 }
 }
```

[view raw](https://gist.github.com/acquialibrary/d930049031773997317c/raw/3eb25764b6a53d3e0abb70760d534b73367b5ac9/PageExampleForm.php) [PageExampleForm.php](https://gist.github.com/acquialibrary/d930049031773997317c#file-pageexampleform-php) hosted with ❤ by [GitHub](https://github.com)

In the code example above everything should resemble building a form in Drupal 7 though in an object oriented programming "way". This is because the Form API has remained essentially unchanged with the exception being the addition of new form elements and class encapsulation.The code from `PageExampleForm` is comprised of the following things:

*   It provides namespaces for the PageExampleForm class and extends the core FormBase class.
*   It implements 4 methods, 3 of which are similar to Drupal 7.
    *   The `getFormId()` method is new and mandatory, used simply to return the machine name of the form.
    *   The `buildForm()` method is mandatory and it returns a render array exactly like in Drupal 7 when using hook_form.
    *   The `validateForm()` method is optional and and validates similar to Drupal 7 `hook_form_validate` which api.drupal.org doesn't document.
    *   The `submitForm()` method performs the submission handling which is also not documented in Drupal 7 for `hook_form_submit`.

    ### Ensure the route

    To use this form, a route must be defined and added to the already existing `page_example.routing.yml` file:

    ```
    page_example_form:  
    path: '/examples/page_example/form'  
    defaults:  
    _form: '\Drupal\page_example\Form\PageExampleForm'  
    _title: 'Demo Form'  
    requirements:  
    _permission: 'access simple page'
    ```

    Instead of `_controller` under defaults, we use `_form` to specify that the target is a form class. The value is therefore the class name we just created.

    Rebuild the cache and navigate to `http://mysite.com/examples/page_example/form` to see and use the form.

    It should look like this:

    ![demo-form.png](https://raw.githubusercontent.com/outlearn-content/acquia/master/assets/demo-form.png)

    If you are familiar with `drupal_get_form()` and are wondering how to load a form like Drupal 7, the answer is in the global Drupal class. To retrieve a form, you can use its `formBuilder()` method and do something like this:

    ```
    $form = \Drupal::formBuilder()->getForm('Drupal\demo\Form\DemoForm');
    ```

> **IMPORTANT**
Block Plugins are what you will be using in custom and contributed modules. Block Plugins should _never_be dependent on or change Block Entities.


You can then `$form`, which will be the renderable array of the form.
