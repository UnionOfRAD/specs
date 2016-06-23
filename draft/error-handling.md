## Abstract

This specification describes the lithium way of dealing with exceptional situations and clarifies the difference between exceptions and errors.

## Current Development

A code draft exists for the [Error Handler](http://pastie.org/private/xmyraplnc42ptrurbpudlg), as well as associated [modifications to the switchboard](http://pastie.org/private/tnrvwihsqa3ckuorin6jw).

## What's The Difference Between Exceptions and Errors?

n/a

## Exceptions

### Use Only In Exceptional Situations

The situation is really exceptional and doesn't occur under **normal** conditions.

Examples:

*  Try reading a file and file does not exist/is not readable

### Don't Use For Flow Control

Exceptions shouldn't be used for flow control because they're quite expensive (a stack trace must be generated).

### Use Instead Of Error Codes

```
function foo($bar) {
    if (!$bar) {
        return ERROR_FOO_BAR;
    }
    return true;
}
```

```
function foo($bar) {
    if (!$bar) {
        throw new Exception("Foo Bar!");
    }
    return true;
}
```

### Describe

Describe the exception using a message. One message may consist of multiple sentences. Start the message with a capital letter and end with a period. Always use double quotes. The message **must not** include the method/class name.

```
throw new \lithium\error\Exception("The operation `{$name}` failed.");
```

Only create new subclasses if you need to pass additional information.

```
class DatabaseException extends \lithium\core\Exception {

    protected $_sql;

    public function __construct($message = null, $info = []) {
        extract($info);
        $this->_sql = $sql;
        parent::__construct($message, $code);
    }

    /// ...

}
```

### Use Other Built-In Exceptions

* `\BadFunctionCallException` A callback refers to an undefined function or some arguments are missing.
* `\BadMethodCallException` A callback refers to an undefined method or some arguments are missing.
* `\DomainException`
* `\InvalidArgumentException` An argument does not match with the expected value.
* `\LengthException` A length is invalid.
* `\LogicException` A logic expression is invalid.
* `\OutOfBoundsException` A value is not a valid key.
* `\OutOfRangeException` A value does not match with a range.
* `\OverflowException`  When you add an element into a full container.
* `\RangeException` When an invalid range is given.
* `\RuntimeException`:An error which can only be found on runtime occurs.
* `\UnderflowException` When you try to remove an element of an empty container.
* `\UnexpectedValueException` A value does not match with a set of values.


### Catch What You Can Handle

Cited from http://www.c2.com/cgi/wiki?CatchWhatYouCanHandle:

> [...] Ideally a catch clause should either _try some other strategy_ to accomplish the method's goal or, more often, _report the error_ to the end user and return the program to a passive state. Alternatively a catch clause may _clean up_ resources and _re-throw the exception_ [...] In all cases **decisions are only made by decision making code**.


## Errors

Only `E_USER_NOTICE ` and `E_USER_DEPRECATED` should be triggered. In all other situations use an exception.


## The Exception & Error Handler

n/a

## References

* http://onjava.com/pub/a/onjava/2003/11/19/exceptions.html
* http://php.net/manual/en/language.exceptions.extending.php
* http://www.c2.com/cgi/wiki?ExceptionPatterns

