# LSR-2 Testing

## Abstract

This document is an extension of [[Spec: Coding]]. The goal of this document is to keep core tests consistent. We suggest you follow the same rules in your own projects. For more information see the section on [Lithium's Unit Testing Framework](http://lithify.me/docs/manual/quality-code/testing.wiki) in the manual.

## Class Names

Class names should be the name of the class you are testing with the `Test` suffix.

```php
class ExampleTest extends \lithium\test\Unit {}
```

## Mocks

Often you need to create a mock class to override some functionality of the class you are testing. A mock class will have a prefix of `Mock` and be located in the a parallel namespace of the class being tested. 

```php
namespace lithium\tests\mocks\http;

class MockExample extends \lithium\util\Example {}
```

In the above example we are going to be using the Mock to test a class in the `lithium\http` namespace.

## Assertions

Assertions should follow the `expected` -> `result` format.

```php
$expected = 'works';
$result = Do::somthing();
$this->assertEqual($expected, $result);
```

Always use the right assertion that does the job. The assertion methods are made available through the `Unit` class from which most tests directly or indirectly inherit. Have a look at the Class Methods section on [API Browser/Unit](http://lithify.me/docs/lithium/test/Unit).

```php
$this->assertTrue($result);
$this->assertFalse($result);
$this->assertNull($result);
// ...
```

## Isolation 

It is very important that a test runs as part of a sequence of tests without being affected by previous tests or in turn itself affecting tests that run afterwards. All tests need to be correctly isolated. Changes to global state or i.e. configurations must be backed up and than restored after the test run.  

### Backing up and Restoring

Two methods are relevant for backing up and restoring global state. Within `setUp()` the current state is by convention backed up into the `$_backup` property of the test class. In `tearDown()` the state is restored.

```php
class ExampleTest extends \lithium\test\Unit {

    protected $_backup = array();

    public function setUp() {
        $this->_backup['catalog'] = Catalog::config();
        Catalog::config(array(
            'runtime' => array('adapter' => new Memory())
        ));
    }

    public function tearDown() {
        Catalog::reset();
        Catalog::config($this->_backup['catalog']);
    }
}
```

## Coverage

The Lithium test coverage policy requires:

1. A corresponding test exists for each class.
2. All classes have a test coverage of **85%** and higher.

These 2 conditions must be met before the project reaches stable. 

The current status for Lithium regarding test coverage is tracked [through a ticket](https://github.com/UnionOfRAD/lithium/issues/17) in our tracking system. Missing tests or coverage are considered a bug.

