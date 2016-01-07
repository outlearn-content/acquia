# Testing in Drupal 8

Testing in Drupal 8 has been broken into two primary types: unit testing and functional testing. Testing is used in a variety of ways. Some tests are written before development begins and helps developers build towards desired functionality. Other tests are written to help identify regression issues. The goal of this lesson is to introduce these two types of testing, but not to explain when to write these tests.

The key difference between unit and functional testing is the scope of the tests. Unit tests aim to test a small amount of functionality. In Drupal 8, custom services are a good use case for unit tests. This is because services are designed to be decoupled from the functionality that uses the services. These services do not depend on a running Drupal website or on live configuration and data.

Functional testing to test functionality that integrates many components, such at and end-to-end use of a feature. For example, we may want to perform one change in the system and then ensure that another system reacts to this change. In order to accomplish this, we need an actual Drupal website. You can do a larger range of things with functional tests, but they are generally slower.

## In this lesson...

*   Write unit tests.
*   Execute unit tests.
*   Write functional tests.
*   Execute functional tests.
