<!--
{
"name" : "drupal-8-exporting-configurations",
"version" : "0.0.1",
"title" : "Lesson 5.2 - Exporting configurations",
"description" : "Exporting configurations",
"freshnessDate" : 2015-12-11,
"homepage" : "https://docs.acquia.com/articles/drupal-8-exporting-configurations",
"canonicalSource" : "https://docs.acquia.com/articles/drupal-8-exporting-configurations",
"license" : "CC BY-SA"
}
-->

<!-- @section -->

# Overview

<!-- @section -->

## Exporting the Configuration

There are three methods that you can use to expose the configuration files:

*   **Full export** - Export the entire configuration for the site as an archive.

    Navigate to `/admin/config/development/configuration/full/export` and click **Export**. A file named `config.tar.gz` will download to your computer. Expand the archive and move the configuration files to a separate temporary directory.

    ![full-export.png](https://raw.githubusercontent.com/outlearn-content/acquia/master/assets/full-export.png)

*   **Partial export** - Export each file separately using the Single Export option.

    Export each file separately using the Single Export option. Navigate to `/admin/config/development/configuration/single/export` and using the list, find each file using the **Configuration type** and **Configuration name** selection lists. Copy the contents of each file to a new file in a separate temporary directory. The name of each file should match the filename listed below the output textarea, shown in the following picture.

    ![single-export.png](https://raw.githubusercontent.com/outlearn-content/acquia/master/assets/single-export.png)

    If you have trouble finding all the configuration files, examine the second group of words in each configuration line. To find `core.entity_form_display.node.external.default`, choose **Entity form display** as the **Configuration Type**. The select lists in the single export are vague. For example, when selecting the **Field Configuration** type, the **Body** field is listed at least 3 times. You must manually view each one until you find one that contains mentions of the **External** content type.

*   **Drush** - Export the configuration with Drush.

    In the earlier part of the lesson, we exported the configuration files to the file system. This created a structure like `sites/default/files/config_[your config path]/staging/`. We are going to export the configuration to the file system and move our desired files to our module.

  ```
  drush config-export
  ```

<!-- @task, "text" : "Export the configuration needed for your content type as a preparation to have it included by your own module." -->

Regardless of which route one takes, the next step is the same: Remove the first line containing the `UUID`.

<!-- @section -->

## Creating the empty module

To create an empty module for your use, complete the following steps:

1.  Create a `fields_example` directory in the Drupal 8 modules directory.
2.  Create a subdirectory called `config`.
3.  Create another directory inside `config` called `install`.
4.  Copy the `node.type.external.yml` file from the configuration export to `config/install/`.
5.  Create a new empty file named `external.info.yml`.

The directory structure should appear similar to the following:

```
fields_example/
|-- config/
| |-- install/
| |-- node.type.external.yml
|-- external.info.yml
```

It's time to review the progress.

1.  Remove the existing External content type.

    If you used the console to move the files in the file system, the content type is already removed.

2.  Navigate to `/admin/structure/types`, and then **delete** the content type.
3.  Navigate to **extend**, and then enable the **External Link Content Type** module.
4.  Go to `/node/add/external`, and then and view the **node add** form.

    ![one-field-node.png](https://raw.githubusercontent.com/outlearn-content/acquia/master/assets/one-field-node.png)

We now have a working module which provides the External content type! Now that we've verified the module works, navigate back to **extend**, and then uninstall the External Link Content Type module in preparation for the next step.

<!-- @task, "text" : "Create an empty placeholder module as described above that is going to provide your content type." -->

<!-- @section -->

## Adding the field configuration files

Move the remaining configuration files we exported earlier in the lesson to the `fields_example/config/install/` directory. Here's the list of required files:

*   `core.entity_form_display.node.external.default.yml`
*   `core.entity_view_display.node.external.default.yml`
*   `core.entity_view_display.node.external.teaser.yml`
*   `field.field.node.external.body.yml`
*   `field.field.node.external.field_image.yml`
*   `field.field.node.external.field_url.yml`
*   `field.storage.node.field_url.yml`

Then navigate to **extend** and enable the **External Link Content Type** module. Now navigate to `/node/add/external` and view the **node add** form. The **body**, **URL**, and **screenshot** fields are present.

![enabled-external.png](https://raw.githubusercontent.com/outlearn-content/acquia/master/assets/enabled-external.png)

<!-- @task, "text" : "Extend the module as described so it provides all the field-level configuration for your content type as well." -->
