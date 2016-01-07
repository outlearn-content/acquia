# Creating a New Content Type with Fields


At this time, the easiest way to create a new content type with fields is to let Drupal write the configuration files, and then select the new configuration files to use in your module. A good place to start the investigation is to review the `.yaml` files in the standard profile in Drupal core. Filling out the fields in in the YAML file itself to generate a new Drupal field for an entity by hand can be tedious. Another challenge is identifying which fields in the YAML file are needed for different field types. A good example to review is:

```
core/profiles/standard/config/install/field.storage.node.field_image.yml
```

## In this lesson...

*   Assemble the module `examples/field_example`.
*   Learn about the differences in fields for entities between Drupal versions 7 and 8.
*   Explore building a custom field with a module.
*   Explore modifying an existing field with a module.
