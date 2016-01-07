# Fields in Drupal 7 vs Drupal 8 

In Drupal 7, fields were often stored as complex arrays that could take on a number of patterns. In Drupal 8, fields all use the same pattern. They are all objects in the class [`FieldItemList`](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Field%21FieldItemList.php/class/FieldItemList/8). This is the case if they are single value, multi-value, multilingual, built-in fields, or attached fields. In some ways, this makes developing for Drupal 8 easier, but also means that some things that were simple in Drupal 7 are more complex.

In Drupal 7, we often accessed fields directly, using hooks to modify fields when they were loaded, changed, or saved. In Drupal 8, there is a focus on entities instead of fields. Because of this, if we want to modify fields we need to get the fields out of the entities.
