<!--
{
"name" : "drupal-8-configuration-and-config-object",
"version" : "0.0.1",
"title" : "Lesson 3.2 - Configuration and the Config object",
"description" : "Configuration and the Config object",
"freshnessDate" : 2015-12-11,
"homepage" : "https://docs.acquia.com/articles/drupal-8-configuration-and-config-object",
"canonicalSource" : "https://docs.acquia.com/articles/drupal-8-configuration-and-config-object",
"license" : "CC BY-SA"
}
-->

<!-- @section -->

# Overview

Drupal 8 provides a [Config object](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Config%21Config.php/class/Config/8) that we can use to interact with the configuration. Some classes already have it available through [dependency injection](http://symfony.com/doc/current/components/dependency_injection/types.html). This will be discussed in later lessons; we'll treat the concept very briefly here.

> **Types of Injection: from the Symfony documentation** _"There are several ways that the dependencies can be injected. Each injection point has advantages and disadvantages to consider, as well as different ways of working with them when using the service container."_

`ConfigFormBase` is one such class that uses dependency injection to provide a config object.

Looking at the the `configform_example` module code, you can see that it is using the `config()` method of the parent class to retrieve a Config object, populated with our `configform_example.settings` simple configuration. Then, for the `#default_value` of the email form element, we use the `get()` method of the Config object to retrieve the value of the email address.

### Altering the config object

In this method we first retrieve the Config object for our configuration (like we did before). Then, we use its `set()` method to change the value of the `email_address` to the value the user submitted. Then we use the `save()` method to save the configuration. Lastly, we extend the parent submit handler because it contains some functionality to send a Drupal message to the screen.

```
public function submitForm(array &$form, FormStateInterface $form_state) {
$config = $this->config('configform_example.settings');
$config->set('email_address', $form_state->getValue('email'));
$config->save();
return parent::submitForm($form, $form_state);
```

### Testing the object

To test the module, you will need to enable the `configform_example` module. You can clear the cache and try it out.

When you're ready to test, submit a new email address, which gets stored in the configuration. The module's `configform_example.settings.yml` file won’t change, but you can export the `configform_example.settings` configuration, and then import it into another website.

Create the `ConfigFormExampleConfigForm.php` class file in the `src/Form` folder of our module:

[Download this file](https://gist.github.com/acquialibrary/c7e11dcaae9278ea9c0a/archive/14be57d56955556a0e38420a3dbacb377870deac.zip).

```php
 <?php
 /**
 * @file
 * Contains \Drupal\configform_example\Form\ConfigFormExampleConfigForm.
 */
 namespace Drupal\configform_example\Form;
 use Drupal\Core\Form\ConfigFormBase;
 use Drupal\Core\Form\FormStateInterface;
 class ConfigFormExampleConfigForm extends ConfigFormBase {
 /**
 * {@inheritdoc}.
 */
 public function getFormId() {
 return 'configform_example_form';
 }
 /**
 * {@inheritdoc}.
 */
 public function buildForm(array $form, FormStateInterface $form_state) {
 $form = parent::buildForm($form, $form_state);
 $config = $this->config('configform_example.settings');
 $form['email'] = [
 '#type' => 'email',
 '#title' => $this->t('Your .com email address.'),
 '#default_value' => $config->get('email_address'),
 ];
 return $form;
 }
 /**
 * {@inheritdoc}
 */
 public function validateForm(array &$form, FormStateInterface $form_state) {
 if (strpos($form_state->getValue('email'), '.com') === FALSE ) {
 $form_state->setErrorByName('email', $this->t('This is not a .com email address.'));
 }
 }
 /**
 * {@inheritdoc}
 */
 public function submitForm(array &$form, FormStateInterface $form_state) {
 $config = $this->config('configform_example.settings');
 $config->set('email_address', $form_state->getValue('email'));
 $config->save();
 return parent::submitForm($form, $form_state);
 }
 /**
 * {@inheritdoc}
 */
 protected function getEditableConfigNames() {
 return ['configform_example.settings'];
 }

 }
```

[view raw](https://gist.github.com/acquialibrary/c7e11dcaae9278ea9c0a/raw/14be57d56955556a0e38420a3dbacb377870deac/ConfigFormExampleConfigForm.php) [ConfigFormExampleConfigForm.php](https://gist.github.com/acquialibrary/c7e11dcaae9278ea9c0a#file-configformexampleconfigform-php) hosted with ❤ by [GitHub](https://github.com)

## Using pre-written code

As described earlier, one useful approach is to use previously written code as a starting point for new work. In this instance, some code written in a previous lesson should be modified to account for changes in names to avoid conflicts with other files and modules. It is possible to use the page_example module created in lesson 1 to create the page the form will live in for lesson 3.

Below you will find the code after modification.

**Examples project: configform_example.links.menu.yml**

```
configform_example.form:
title: Config Form Example
route_name: configform_example_form
parent: system.admin_reports
```

### Yaml configurations for configform_example module

Place this info YAML file in the root of the configform_example module in the `configform_example.info.yml` file:

```
name: Config Form example
type: module
description: 'An example module showing how to define a configuration form to be displayed at a given URL.'
package: Example modules
core: 8.x
```

Place this routing YAML file `configform_example.routing.yml` at the same level:

```
configform_example_form:
path: 'examples/configform_example/form'
defaults:
_form: '\Drupal\configform_example\Form\ConfigFormExampleConfigForm'
_title: 'Config Form'
requirements:
_permission: 'access simple page'
```

## Configuration Inspector

From drupal.org, "Configuration inspector for Drupal 8 uses the core built-in configuration system and schema system to let you inspect configuration values and the use of schemas on top of them. This makes it possible to have a developer focused overview of all your configuration values and do various testing and verification tasks on your configuration schemas."

After it is installed, the Configuration Inspector project provides a "raw data" link for your custom configuration settings, but the list, tree, and form links are unavailable (but those links are provided for all core and many other contrib settings). To facilitate this, you can add a schema definition for your settings, such as the following `schema.yml` file in `./config/schema/configform_example.schema.yml`:

```
configform_example.settings:
type: mapping
label: 'Configform Example settings'
mapping:
email_address:
type: string
label: 'This is the example email address.
```

After reinstalling the configform_example module, you will have access to the additional functionality provided by the Configuration Inspector.
