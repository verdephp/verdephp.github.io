# Mock/Spy
Mock functions or "spies", let you spy on the behaviour of a function that is called indirectly by some other code.

Mocks are created using the [mock function](/en/mocks.html#mock-function), and can be also used to [mock a class](en/mocks.html#mocking-a-class).
While, to create spies use the [spyOn](/en/spies.html#syntax) syntax.

## Methods

- [getCallable()](#getcallable)
- [mockClear()](#mockclear)
- [mockRestore()](#mockrestore)
- [mockImplementation(function)](#mockimplementationfunction)
- [mockImplementationOnce(function)](#mockimplementationoncefunction)
- [mockReturnValue(value)](#mockreturnvaluevalue)
- [mockReturnValueOnce(value)](#mockreturnvalueoncevalue)

## Reference

### getCallable()

Returns the spy function for the mock. You can call this directly or pass as callback.
It will automatically take care of running the original function, or the mocked implementation for you. 
And, will also record all the invocations and their parameters.

```php
<?php

$func = func();

func(); // this is wrong, $func is not a callable
func()->getCallable()(); // returns null by default
```

### mockClear()

Reset only the information about the calls spied by the mock.
This is useful when you want to clean up a mock's usage data between two assertions.

### mockRestore()

Does everything `mockClear` does, and also restore the original (non-mocked) implementation.
This is useful when you want to mock functions in certain test cases and restore the original implementation in others.

`mockRestore` works for mocks created with `spyOn`, `func` and `mock`, but it has a different behaviour for each of them.

#### Restore a spy

Restoring a spy clear the calls history and removes the custom implementations/return values.
You'll be still able to spy the function calls, and re-mock the implementation again.
 
```php
<?php
/* spyOn */
$spy = \Verde\spyOn('strlen');

$spy->mockImplementation(function() { return 'ciao'; });
strlen('whatever'); // return "ciao"

// Restore the original function
$spy->mockRestore();
strlen('whatever'); // return 8
```

#### Restore a mock

Restoring a mock clear the calls history and restore the custom implementation, if passed during the creation.

```php
<?php
/* with custom implementation */
$func = func(function() { return 42; });

$func->mockImplementation(function() { return 'ciao'; });
$func->getCallable()(); // return "ciao"

// restore the custom implementation
$func->mockRestore();

func(); // returns "42"


/* without custom implementation */
$func = func();

$func->mockImplementation(function() { return 'ciao'; });
$func->getCallable()(); // return "ciao"

// restore the custom implementation
$func->mockRestore();

func(); // returns "null" by default

```

#### Restore a class mock

Restoring a class mock clear the calls history and restore the original class methods, this means that you won't be able to
keep spying on its method, unless you mock it again.


```php
<?php
/* without custom mocked methods */
$mock = mock(DummyClass:class);

// this execute the mock function automatically generated during the mock
DummyClass->doSomething();

// restore the original class
$mock->mockRestore();

// execute the original method
DummyClass->doSomething();


/* without custom mocked methods */
$doSomethingMock = func(function() { return 42; });
$mock = mock(DummyClass::class, [
    'doSomething' => $doSomethingMock,
]);

// executes the $doSomethingMock
DummyClass->doSomething(); // returns 42

// this does NOT restore the original 'doSomething' method of the class
$doSomethingMock->mockRestore();

// this still returns 42!
DummyClass->doSomething();
```

*NOTE*: Class mocks are not destroyed automatically, so you must restore them manually at the end of your test.

### mockImplementation(function)

Accepts a function that should be used as the implementation of the mock.
The mock itself will still record all calls made to it.

`mockImplementation` works for mocks created with `spyOn` or `func`.
 
```php
<?php

$spy = spyOn('strlen');
$spy->mockImplementation(function() { return 'ciao'; });

strlen('whatever'); // returns "ciao"

// or
$func = func();

$func->mockImplementation(function() { return 'ciao'; });
$func->getCallable()(); // return "ciao"

``` 

### mockImplementationOnce(function)

Accepts a function that will be used as an implementation of the mock for one call to the mocked function. 
Can be chained so that multiple function calls produce different results.

`mockImplementationOnce` works for mocks created with `spyOn` or `func`.

```php
<?php

$spy = spyOn('strlen');
$spy->mockImplementationOnce(function() { return 'ciao'; });

strlen('whatever'); // returns "ciao"
strlen('whatever'); // returns 8

// or

$spy = spyOn('strlen')
    ->mockImplementationOnce(function() { return 'ciao'; })
    ->mockImplementationOnce(function() { return 'hello'; });

strlen('whatever'); // returns "ciao"
strlen('whatever'); // returns "hello"
strlen('whatever'); // returns 8

``` 

*NOTE:* When the mocked function runs out of implementations defined with mockImplementationOnce, 
it will execute the original (for `spyOn`) or default (for `func`) implementation. 

### mockReturnValue(value)

Accepts a value that will be returned whenever the mock function is called.

`mockReturnValue` works for mocks created with `spyOn` or `func`.

```php
<?php

$spy = spyOn('strlen');
$spy->mockReturnValue("ciao");

strlen('whatever'); // returns "ciao"
strlen('whatever'); // returns 8
``` 

### mockReturnValueOnce(value)

Accepts a value that will be returned for one call to the mock function. 
Can be chained so that successive calls to the mock function return different values. 
When there are no more mockReturnValueOnce values to use, calls will return a value specified by mockReturnValue or will call the original implementation.

`mockReturnValueOnce` works for mocks created with `spyOn` or `func`.

```php
<?php

$spy = spyOn('strlen');
$spy->mockReturnValueOnce("ciao");

strlen('whatever'); // returns "ciao"
strlen('whatever'); // returns 8

// no more return values
$spy = spyOn('strlen')
    ->mockReturnValueOnce("ciao")
    ->mockReturnValueOnce("hello");

strlen('whatever'); // returns "ciao"
strlen('whatever'); // returns "hello"
strlen('whatever'); // returns 8

// custom mockReturnValue
$spy = spyOn('strlen')
    ->mockReturnValue("ciao")
    ->mockReturnValueOnce("hello");

strlen('whatever'); // returns "ciao"
strlen('whatever'); // returns "hello"
strlen('whatever'); // returns "ciao" (for this and next calls, unless the mock is restored).

``` 

*NOTE:* When the mocked function runs out of mocked return values it will use the `mockReturnValue` information. 
If the latter has not been specified, it will then fallback on the original function (for `spyOn`) or default implementation (for `func`). 
