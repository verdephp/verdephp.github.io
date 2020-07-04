# Mocks
A *mock* allows you to test the links between code by erasing the actual implementation of a function,
capturing calls to the function (and the parameters passed in those calls) and allowing test-time configuration of return values.

We have two ways of doing so:

- [using mock function](#using-a-mock-function)
- [mocking a class](#mocking-a-class)

**NOTE**: Mocking a class requires [runkit7](https://github.com/runkit7/runkit7) to work!
 
## Mock function

Mock functions are function (spies) with pre-programmed behaviour. Use a mock when you want to:

1. control the function's behaviour from a test to force the code down a specific path
2. capture calls and parameters passed to the function

Use the helper `\Verde\func` to create a mock function, example:

```php
<?php

use \Verde\expect;
use \Verde\func;

function theAnswerToEverything(callable $callback) {
    $callback(42);
}

test('the mock function is called with the correct argument', function() {
    $mockFunction = func();
    
    theAnswerToEverything($mockFunction->getCallable());

    // We can spy on the mock to see if it has been called and with which argument    
    expect($mockFunction)->toHaveBeenCalledWith(42);
})
```

Have a look at the [Mocking API](/en/mock-spy-methods.html) to know more about the methods available.

## Mocking a Class

This is useful if you need to erase or control the implementation of a class. 
When you mock a class, all its methods gets replaced with a mock function that does nothing and returns null.
It also allows us to control the behaviour of the specified methods if we need to.

### Syntax

Replace all the class methods with mock functions:

`$mock = mock(className)`

To specify custom mock functions for methods:

`$mock = mock(className, [mockedMethods])`

where `mockedMethods` is an array:

```
[
    'methodNameToMock' => mockFunction,
]
```

**NOTE:** You must call the `$mock->mockRestore()` method at the end of your test! Or your class will remain mocked 
until the end of all tests!
 
### Example

Suppose we have a *Todo List App* with following three classes:

1. **Database**: Is responsible to read and write data to the database
```php
<?php

final class TodoListDatabase
{
    public function getRecords() {}
    public function addToDo(string $listName, array $items) {}
}
```

2. **HTMLGenerator**: Is responsible to generating the HTML table for the list

```php
<?php
final class HTMLGenerator
{
    const GET_RECORDS_ERROR = 1;

    public function printItems(array $items) {}
    public function printError(int $errorType) {}
} 
```

3. **TodoListApp**: Is responsible to allow the communication between the two other classes.

```php
<?php

final class TodoListApp
{
    private $database;
    private $htmlGenerator;

    public function __construct(TodoListDatabase $database, HTMLGenerator $printer) {
        $this->database = $database;
        $this->printer = $database;
    }

    public function printItems(): string
    {
        try {
            $items = $this->database->getRecords();

            $this->htmlGenerator->printItems($items);
        } catch (\Throwable $error) {
            $this->htmlGenerator->printError(HTMLGenerator::GET_RECORDS_ERROR);
        }
    }
}
```

In the test you:

1. don't want to execute the original implementation of the classes, i.e. we don't want to use communicate with a database at all
2. want to control the `TodoListDatabase` behaviour, to make sure our `TodoListApp` behave correctly in different scenarios

```php
<?php

use \Verde\expect;
use \Verde\func;
use \Verde\mock;


test('retrieves all the records from the database before printing them', function() {
    // We mock the entire class as we don't care what the class is doing
    $htmlGeneratorMock = mock(HTMLGenerator::class);

    // We want to spy on the 'getRecords' method
    $getRecordsMock = func();

    $todoListDatabseMock = mock(TodoListDatabase::class, [
        'getRecords' => $getRecordsMock // Now we can spy on 'getRecords'
    ]);

    $todoApp = new TodoListApp(new TodoListDatabase());
    $todoApp->printItems();

    expect($getRecordsMock)->toHaveBeenCalledWith();
    
    // we must restore the mock at the end of the test!
    $htmlGeneratorMock->restoreMock();
    $todoListDatabseMock->restoreMock();
});

test('shows the correct error when there we cannot retrieve the data from the database', function() {
    $printItems = func();
    $printError = func();

    $getRecordsMock = func(function () {
        throw new Error('Something went wrong');
    });

    $htmlGeneratorMock = mock(HTMLGenerator::class, [
        'printItems' => $printError,
        'printError' => $printError
    ]);

    $todoListDatabseMock = mock(TodoListDatabase::class, [
        'getRecords' => $getRecordsMock
    ]);

    $todoApp = new TodoListApp(new TodoListDatabase());
    $todoApp->printItems();

    expect($printItems)->not()->toHaveBeenCalled();
    expect($printError)->toHaveBeenCalledWith(HTMLGenerator::GET_RECORDS_ERROR);

    // we must restore the mock at the end of the test!
    $htmlGeneratorMock->restoreMock();
    $todoListDatabseMock->restoreMock();
});
```

**REMEMBER:** Mocks are not destroyed at the end of each test! So you must call the `$mock->mockRestore()` method at the end of your test, 
or it will remain mocked until the end of all tests!

To know more about the mock/spy methods, please check [Mock/Spy](/en/mock-spy-methods.html)

**NOTE**: Mocking a class requires [runkit7](https://github.com/runkit7/runkit7) to work!
**NOTE**: Mocking works perfectly with PHPUnit too.
