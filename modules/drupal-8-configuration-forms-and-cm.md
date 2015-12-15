<!--
{
"name" : "drupal-8-configuration-forms-and-cm",
"version" : "0.0.1",
"title" : "Lesson 3.1 - Configuration forms and management",
"description" : "Configuration forms and management",
"freshnessDate" : 2015-12-11,
"homepage" : "https://docs.acquia.com/articles/drupal-8-configuration-forms-and-cm",
"canonicalSource" : "https://docs.acquia.com/articles/drupal-8-configuration-forms-and-cm",
"license" : "CC BY-SA"
}
-->

<!-- @section -->

# Overview

This lesson introduces both a special type of form called a _configuration form_ and Configuration Management (CM), which describes the effort made to gather the multiple sources of configuration information and functions in Drupal 7 into a single system in Drupal 8.

We also take this opportunity to demonstrate how to use boiler plate code. We will guide you through modifying a copy of the `page_example` module which is part of the larger [Examples](http://drupal.org/project/examples) project. We will modify the page_example module so that it uses a configuration form to store a single default value in the configuration system.

<!-- @section -->

## In this lesson...

*   Learn about CM (Configuration Management).
*   Assemble the module `examples/configform_example`.
*   Learn about configuration forms.
*   Explore building a configuration form.
*   Explore creating `*.settings.yml` files as an introduction to using CM to store configurations for a module.

<!-- @section -->

## Configuration Management

[Drupal 8 Configuration Management](https://groups.drupal.org/build-systems-change-management/cmi), or CM, is powered by a [configuration API](https://www.drupal.org/developing/api/8/configuration) and provides a central place for modules to store configuration data. This can be simple static data like the site name, or configuration for more complex objects like field definitions or image styles. It is meant to replace contributed solutions like Drupal 7 [CTools](http://drupal.org/project/ctools) exportables and enhance other contributed Drupal 7 modules, such as the [Features](http://drupal.org/project/features) module.

The Configuration core module in Drupal 8 provides a user interface for importing and exporting configuration changes between a Drupal installation in different environments (such as development, staging, and production), so that you can make and verify your changes separately from your live environment. This replaces the use of the Features and [Strongarm](https://www.drupal.org/project/strongarm) modules.

With the Configuration inspector module, you'll have the possibility to review the configuration using the Drupal administration pages rather than by having to grep through YAML files in the file system.

Module developers can commit YAML files in a `{module}/config/install` directory, defining the structure of their configuration settings, and deploy them across multiple environments thanks to version control. There is also a context system for loading configurations, which makes customization for multilingual sites (and similar use-cases) much easier. Such customization is possible through the use of schema files.

### Drupal 7 functions replaced by CM

CM replaces several functions used by earlier versions of Drupal. Many of these functions are used in different contexts. The key idea is that these functions are consolidated into the CM system to allow for consistent handling of configuration data.

*   [`variable_set()`](https://api.drupal.org/api/drupal/includes%21bootstrap.inc/function/variable_set/7)
*   [`variable_get()`](https://api.drupal.org/api/drupal/includes%21bootstrap.inc/function/variable_get/7)
*   [`variable_del()`](https://api.drupal.org/api/drupal/includes%21bootstrap.inc/function/variable_del/7)
*   [`hook_update_N()`](https://api.drupal.org/api/drupal/modules%21system%21system.api.php/function/hook_update_N/7)
*   [`drush features-update`](https://www.drupal.org/node/960926)
*   [`ctools_export_object()</a>`](http://drupalcontrib.org/api/drupal/contributions%21ctools%21includes%21export.inc/function/ctools_export_object/7)
*   [`ctools_export_load_object()`](http://drupalcontrib.org/api/drupal/contributions%21ctools%21includes%21export.inc/function/ctools_export_load_object/7)
*   [`$conf[...];`](https://api.drupal.org/api/drupal/developer%21globals.php/global/conf/7)

<!-- @section -->

## Configuration forms

When we defined [PageExampleForm](https://docs.acquia.com/articles/creating-forms-drupal-8#form-create) we extended the `FormBase` class; a simple implementation of the `FormInterface` which provides an interface for a form.

Drupal 8 provides an alternate core class, `ConfigFormBase`, which provides additional functionality at the cost of increased complexity in the method. The primary benefit of this class is improved interaction with the configuration system.

### Creating a module to use ConfigFormBase

At this point we create a new module named `configform_example` by copying our already created `page_example`. Now that we have a new place to house this configuration experiment we will transform the `PageExampleForm` class into one which is capable of storing a user provided email address. To achieve this you must replace the `FormBase` class with `ConfigFormBase` in the `ConfigFormExampleConfigForm.php` file (found in configform_example/src/Form/):

```
use Drupal\Core\Form\ConfigFormBase;

class ConfigFormExampleConfigForm extends ConfigFormBase {
```

This new class requires an additional function `getEditableConfigNames`. We should create that now.

```
/**
* {@inheritdoc}.
*/
public function getEditableConfigNames() {
return ['configform_example.settings'];
}
```

### Configuration using YAML

Using the configuration API in Drupal 8 allows you to store configurations such as variables or settings provided by a module in a file. This file is called a YAML file. When a module is enabled the information in the YAML file is immediately imported into the database. The purpose of the import is to improve performance as early versions of Drupal 8 tried a more dynamic approach which resulted in abysmal load times. Despite the fact that the configuration is loaded from a file it is also possible to change the information once it's in the DB using the Drupal user interface. Once changed in the DB it is then easily exportable to YAML files for redeployment to the code base as needed.

When providing configuration for a module it is possible to define the default value in a special YAML file located in the `config/install` directory in the module's root directory. The proper naming convention for this file is to prefix it with the name of the module. In working with our example we use `configform_example.settings.yml` . You can use the following sample code so to set a default email address of `examples@examples.com`. Feel free to change it to a address of your choosing:

```
email_address: form@examples.com
```

### Nesting fields

If necessary, you can also create more complex settings with nested properties:

```
address:
line_1: 123 fake st
city: Boston
state: MA
zip: 55555
```

Note the associative array like nested structure which is similar for all YAML files. For example the `configform_example.info.yml` file. Under the key `address`, we have a `key|value` pair `line_1|123 fake st`. In most use cases, access to the nested values is achieved by use a of a dot(.) - and in this example a developer would use: `address.line_1`.

> **Note**:
This YAML file gets imported only when the module is installed. This means that if a module is already installed you must reinstall it by uninstalling and installing the module.

### Form methods

This is how the `buildForm()` method should now appear:

```
public function buildForm(array $form, FormStateInterface $form_state) {
$form = parent::buildForm($form, $form_state);
$config = $this->config('configform_example.settings');
$form['email'] = [
'#type' => 'email',
'#title' => $this->t('Your .com email address.'),
'#default_value' => $config->get('email_address')
];
return $form;
}
```

First, as opposed to `FormBase`, the `ConfigFormBase` class implements this method to add elements to the form array (a submit button). Because of this, we can use what the parent did before adding our own elements.
