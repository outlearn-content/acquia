<!--
{
"name" : "drupal-8-phpunit-doubles-mocks-stubs",
"version" : "0.0.1",
"title" : "Lesson 10.4 - PHPUnit Doubles / Mocks / Stubs",
"description" : "PHPUnit Doubles / Mocks / Stubs",
"freshnessDate" : 2015-12-11,
"homepage" : "https://docs.acquia.com/articles/drupal-8-phpunit-doubles-mocks-stubs",
"canonicalSource" : "https://docs.acquia.com/articles/drupal-8-phpunit-doubles-mocks-stubs",
"license" : "CC BY-SA"
}
-->

<!-- @section -->

# Overview

When writing unit tests, the goal is to test the individual component, such as a service. Often, the service requires other parts of the system to function. When this is the case, it often makes sense to make _mock_ versions of these dependencies so that we focus testing just on that service.

<!-- @section -->

## Creating a mock

When you create a mock, you are creating an object that appears to the system as a certain class but has all of it's methods replaced by methods that return `NULL`. If you want to return specific values for specific methods, you can recreate dummy versions of those. For example, if we need a query object, we can use the `getMockBuilder()` method and [`getMock()`](https://api.drupal.org/api/drupal/vendor%21phpunit%21phpunit-mock-objects%21src%21Framework%21MockObject%21MockBuilder.php/function/PHPUnit_Framework_MockObject_MockBuilder%3A%3AgetMock/8) to get one.

```
$query = $this->getMockBuilder('\Drupal\Core\Entity\Query\QueryInterface')
->getMock();
```

You can see in this example that we pass in the name of a class and then call `getMock()`.

### Disable OriginalConstructor

This pattern will work for most classes. Some classes have a `__construct()` function that needs to be removed. The easiest way to know this is to run your test with the basic pattern and if you receive an error, add a `disableOriginalConstructor()` method to remove the constructor.

```
$query_factory = $this->getMockBuilder('\Drupal\Core\Entity\Query\QueryFactory')
->disableOriginalConstructor()
->getMock();
```

### Abstract Classes

In some situations, you will be working with an abstract class. Again, try the normal pattern. If you get an error, you can use the following:

```
`$filter_plugin = $this->getMockBuilder('\Drupal\filter\Plugin\FilterBase')
->getMockForAbstractClass();
```
### Mock methods

When you need to return test values from a mock, you can create a replacement for any of the class's methods.

```
uery = $this->getMockBuilder('\Drupal\Core\Entity\Query\QueryInterface')
->getMock();

$query->expects($this->any())->method('execute')
->willReturn([1 => 1]);

// or
$query->expects($this->any())->method('execute')
->will($this->returnValue([1 => 1]));
```

In PHPUnit, there are many alternative ways to write things. The first implementation is a shorthand for the second. In both, we are telling the system that if execute is called on our query object, we should return the array `[1 => 1]`. For most cases, passing `$this->any()` is sufficient as a condition. For more complex mocks, you can send an alternative value.

### Mock methods with maps

In the previous example, we returned a value from the mock function. We can also return different values depending on the input.

This example returns a map array. The first items in the array are the parameters that get passed to the method get and the last item is the return value. We are telling the mock object to return the string `test_field` when `$fieldDefinition->get('field_name')` is called. Similarly, when `$fieldDefinition->get('field_type')` is called, we return `text`. If the method took two inputs, you might have a map with an item, [`'argument_one', 'argument_two', 'return_value']`.

```
$fieldDefinition = $this->getMockBuilder('\Drupal\Core\Field\BaseFieldDefinition')
->getMock();

$fieldDefinition->expects($this->any())->method('get')
->willReturnMap([
['field_name', 'test_field'],
['field_type', 'text'],
['fieldStorage', $fieldStorage],
]);
```

### Mock methods with callback returns

If you need to do complex processing, or access class variables that may change, you can provide a function as the return value. Newer versions of PHP support anonymous functions, so you can put the function declaration as an argument.

```
$this->field = $this->getMockBuilder('\Drupal\Core\Field\FieldItemList')
->disableOriginalConstructor()
->getMock();

$this->field->expects($this->any())->method('getValue')
// We use a function here so that the value is calculated at runtime.
->will($this->returnCallback(
function() {
return $this->testValue;
}
));
```
