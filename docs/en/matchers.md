# Matchers

**Verde** uses matchers to validate the expected.

## Methods

- [toBe(value)](#tobevalue)
- [toBeCloseTo(value)](#tobeclosetonumber-digits)
- [toBeFalse()](#tobefalse)
- [toBeFalsy()](#tobefalsy)
- [toBeFinite()](#tobefinite)
- [toBeGreaterThan(value)](#tobegreaterthanvalue)
- [toBeGreaterThanOrEqual(value)](#tobegreaterthanorequalvalue)
- [toBeInstanceOf(value)](#tobeinstanceofclass)
- [toBeLessThan(value)](#tobelessthanvalue)
- [toBeLessThanOrEqual(value)](#tobelessthanorequalvalue)
- [toBeNaN()](#tobenan)
- [toBeNegativeInfinity()](#tobenegativeinfinity)
- [toBeNull()](#tobenull)
- [toBePositiveInfinity()](#tobepositiveinfinity)
- [toBeTrue()](#tobetrue)
- [toBeTruthy()](#tobetruthy)
- [toContain(value)](#tocontainvalue)
- [toCount(value)](#tocountvalue)
- [toHaveBeenCalled(value)](#tohavebeencalled)
- [toHaveBeenCalledBefore(spy)](#tohavebeencalledbeforespy)
- [toHaveBeenCalledTimes(value)](#tohavebeencalledtimesvalue)
- [toHaveBeenCalledWith(...value)](#tohavebeencalledwitharguments)
- [toHaveBeenNthCalledWith(...value)](#tohavebeennthcalledwithnthtime-arguments)
- [toHaveLength(length)](#tohavelengthlength)
- [toHaveMethod(name)](#tohavemethodname)
- [toThrow(message)](#tothrowmessage)
- [toThrowError(error)](#tothrowerrorerrorclass-message)

### Not

Negates the assertion that follows in the chain.

```php
<?php

use function Verde\expect;

expect(43)->not()->toBe(42);
```

## Reference

### toBe(value)

`toBe` compares the received and expected values using the === identical operator.

```php
<?php

use function Verde\expect;

test('The Answer to Everything is 42', function() {
    $answer = 42;
    $everything = [
        'answer' => 42,
    ];

    expect($answer)->toBe(42);
    expect($everything)->toBe(['answer' => 42]);
});
```

**NOTE:** Don't use `toBe` with floating-point numbers. For example, due to rounding, in PHP `0.2 + 0.1` is not strictly equal to `0.3`.
If you have floating-point numbers, try [toBeCloseTo](#tobeclosetonumber-digits) instead.

### toBeCloseTo(number, ?digits)

Use `toBeCloseTo` to compare floating-point numbers for approximate equality.

The optional _digits_ argument limits the number of digits to check after the decimal point.
For the default value 2, the test criterion is Math.abs(expected - received) < 0.005 (that is, 10 \*\* -2 / 2).
Intuitive equality comparisons often fail, because arithmetic on decimal (base 10) values often have rounding errors in limited precision binary (base 2) representation.
For example, this test fails:

```php
<?php

expect(0.2 + 0.1)->toBe(0.3); // Fails!
```

It fails because of the [Floating point precision](https://www.php.net/manual/en/language.types.float.php#warning)
So, `toBeCloseTo` solve this kind of problem by comparing the error between the expected and received number.

```php
<?php

expect(0.2 + 0.1)->toBeCloseTo(0.3); // pass
expect(0.5 / 0.6)->toBeCloseTo(0.83, 3); // pass (we limit the check to the first 3 digits)
```

### toBeCountable()

Use `toBeCountable` to check that the received value is coutable.
This matcher uses [is_coutable](https://www.php.net/manual/en/function.is-countable.php) underneath.

```php
<?php

use function Verde\expect;

expect([1, 2, 3]])->toBeCountable();
expect(new ArrayIterator(['foo', 'bar', 'baz']))->toBeCountable();
```

### toBeFalse()

Use `toBeFalse` to check that the received value is identical to `false`.

```php
<?php

use function Verde\expect;

expect(false)->toBeFalse();
expect(0)->not()->toBeFalse();
expect("")->not()->toBeFalse();
```

### toBeFalsy()

Use `toBeFalsy` to loosely compare if received == false.

```php
<?php

use function Verde\expect;

expect(0)->toBeFalsy();
expect("")->toBeFalsy();
expect(false)->toBeFalsy();
```

For more info: [PHP type comparison tables](https://www.php.net/manual/en/types.comparisons.php)

### toBeFinite()

Use `toBeFinite` to check whether the received value is a legal finite number or not.

```php
<?php

use function Verde\expect;

$finite = 42;
$infinite = log(0);
$nan = acos(2);

expect($finite)->toBeFinite();
expect($infinite)->not()->toBeFinite();
expect($nan)->not()->toBeFinite();
```

### toBeGreaterThan(value)

Use `toBeGreaterThan` to compare `received > expected` for number values.

```php
<?php

use function Verde\expect;

test('ounces per can is at least 10', function() {
    expect(ouncesPerCan())->toBeGreaterThan(10);
});
```

### toBeGreaterThanOrEqual(value)

Use `toBeGreaterThan` to compare `received >= expected` for number values.

```php
<?php

use function Verde\expect;

test('ounces per can is at least 12', function() {
    expect(ouncesPerCan())->toBeGreaterThanOrEqual(12);
});
```

### toBeInstanceOf(class)

Use `toBeInstanceOf(class)` to check that the received value is an instance of class.
This matcher uses [instanceof](https://www.php.net/manual/en/internals2.opcodes.instanceof.php) underneath.

```php
<?php

use function Verde\expect;

class A {}

expect(new A())->toBeInstanceOf(A::class);
```

### toBeLessThan(value)

Use `toBeLessThan` to compare `received < expected` for number values.

```php
<?php

use function Verde\expect;

test('ounces per can is at least 20', function() {
    expect(ouncesPerCan())->toBeLessThan(20);
});
```

### toBeLessThanOrEqual(value)

Use `toBeLessThanOrEqual` to compare `received <= expected` for number values.

```php
<?php

use function Verde\expect;

test('ounces per can is at least 12', function() {
    expect(ouncesPerCan())->toBeLessThanOrEqual(12);
});
```

### toBeNaN()

Use `toBeNaN` to check that the received value is a NaN.
This matcher uses [is_nan](https://www.php.net/manual/en/function.is-nan.php) underneath.

```php
<?php

use function Verde\expect;

expect(asin(2))->toBeNaN();
```

### toBeNegativeInfinity()

Use `toBeNegativeInfinity()` to check that received === -INF

```php
<?php

use function Verde\expect;

expect(log(0))->toBeNegativeInfinity();
```

### toBeNull()

Use `toBeNull()` to check that received === null.
This matcher uses [is_null](https://www.php.net/manual/en/function.is-null.php) underneath.

```php
<?php

use function Verde\expect;

expect(doSomething())->toBeNull();
```

### toBePositiveInfinity()

Use `toBePositiveInfinity()` to check that received === +INF

```php
<?php

use function Verde\expect;

expect(-log(0))->toBePositiveInfinity();
```

### toBeTrue()

Use `toBeTrue` to check that the received value is identical to `true`.

```php
<?php

use function Verde\expect;

expect(true)->toBeTrue();
expect(1)->not()->toBeTrue();
```

### toBeTruthy()

Use `toBeTruthy` to loosely check if received == `true`.

```php
<?php

use function Verde\expect;

expect(1)->toBeTruthy();
expect("-1")->toBeTruthy();
expect("php")->toBeTruthy();
```

### toContain(value)

Use `toContain` to check if the received string or array contains the expected value.

```php
<?php

use function Verde\expect;

expect("hello world")->toContain("o w");
expect(['hello', 'world'])->toContain('hello');
```

### toCount(value)

Use `toCount` to compare to check that the size of received array.

```php
<?php

use function Verde\expect;

expect(['hello', 'world'])->toCount(2);
```

### toHaveBeenCalled()

Use `toHaveBeenCalled` to ensure that the mock/spied function/method has been called.

So, let's say have a `getPizzaIngredients(string $name, callable $getIngredients)` function that takes a string and a callback as arguments.
We might want to make sure that the callback gets called at least once whe the name is unknown.

```php
<?php

use function Verde\expect;

function getPizzaIngredients(string $name, callable $getIngredients) {
    $pizze = [
        'Margherita' => ['Mozzarella', 'Pomodoro'],
        'Napolitana' => ['Origano', 'Mozzarella']
    ];

    return $pizze[$name] ?? $getIngredients();
}

test('gets the custom ingredients when the pizza is not in the menu', function() {
    $getIngredients = func();

    getPizzaIngredients('Diavola', $getIngredients->getCallable());

    expect($getIngredients)->toHaveBeenCalled();
});
```

**NOTE**: For more information about `func` have a look at [mock function](mock-function.html)

### toHaveBeenCalledBefore(spy)

Use `toHaveBeenCalledBefore` to ensure that the mock/spiy function/method has been called before another spy.

So, let's say have a `getPizzaIngredients(string $name, callable $getIngredients)` function that takes a string and a callback as arguments.
We might want to make sure that the callback gets called at least once whe the name is unknown.

```php
<?php

use function Verde\expect;
use function Verde\spyOn;

function makePizza() {
    $ingredients = getPizzaIngredients();

    bakePizza($ingredients);
}

test('gets the pizza ingredients before baking it', function() {
    $getIngredients = spyOn('getIngredients');
    $bakePizza = spyOn('bakePizza');

    makePizza();

    expect($bakePizza)->toHaveBeenCalledBefore($getIngredients);
});
```

**NOTE**: For more information about `spyOn` have a look at [Spies](spies.html)

### toHaveBeenCalledTimes(value)

Use `toHaveBeenCalledTimes` to ensure that a mock function got called exact number of times.

In this example we want be sure our mock function gets called only once:

```php
<?php

use Verde\Func;
use function Verde\expect;

function getPizzaIngredients(string $name, callable $getIngredients) {
    $pizze = [
        'Margherita' => ['Mozzarella', 'Pomodoro'],
        'Napolitana' => ['Origano', 'Mozzarella']
    ];

    return $pizze[$name] ?? $getIngredients();
}

test('gets the custom ingredients when the pizza is not in the menu', function() {
    $getIngredients = func();

    getPizzaIngredients('Diavola', $getIngredients->getCallable());

    expect($getIngredients)->toHaveBeenCalledTimes(1);
});
```

### toHaveBeenCalledWith(...\$arguments)

Use `toHaveBeenCalledWith` to ensure that a mock function got called, at least once, with specific arguments.

`toHaveBeenCalledWith` can be also used to make sure that the mock function has been called without any arguments:

```php
<?php

use Verde\Func;
use function Verde\expect;

function getPizzaIngredients(string $name, callable $getIngredients) {
    $pizze = [
        'Margherita' => ['Mozzarella', 'Pomodoro'],
        'Napolitana' => ['Origano', 'Mozzarella']
    ];

    return $pizze[$name] ?? $getIngredients();
}

test('gets the custom ingredients when the pizza is not in the menu', function() {
    $getIngredients = func();

    getPizzaIngredients('Diavola', $getIngredients->getCallable());

    expect($getIngredients)->toHaveBeenCalledWith();
});
```

`toHaveBeenCalledWith` check through all the callback invocations to see if the parameters of any them matches the expectation:

```php
<?php

use Verde\Func;
use function Verde\expect;

function doSomething(callable $callback, $customArgs = null) {
    return $callback($customArgs ?? ['hello', 'world']);
}

test('checks that the parameters of any callback invocation', function() {
    $callback = func();
    doSomething($callback->getCallable());
    doSomething($callback->getCallable(), 'This is custom');

    expect($callback)->toHaveBeenCalledWith(['hello', 'world']);
    expect($callback)->toHaveBeenCalledWith('This is custom');
});
```

`toHaveBeenCalledWith` you can also check that your mock function has been called with ANY function or array using a special constant:

```php
<?php

use Verde\ANY;
use function Verde\expect;

function doSomething(callable $callback, $customArgs = null) {
    return $callback($customArgs);
}

test('checks that the mock is called with a function', function() {
    $callback = func();
    doSomething($callback->getCallable(), function() { return 42; });

    expect($callback)->toHaveBeenCalledWith(ANY::FUNCTION);
});

test('checks that the mock is called with an array', function() {
    $callback = func();
    doSomething($callback->getCallable(), [1, 2, 3]);

    expect($callback)->toHaveBeenCalledWith(ANY::ARRAY);
});
```

### toHaveBeenNthCalledWith(nthTime, ...\$arguments)

If you have a mock function, you can use .toHaveBeenNthCalledWith to test what arguments it was nth called with.

```php
<?php

$func = func();
$callable = $func->getCallable();

$callable(123);
$callable(42);

expect($func)->toHaveBeenNthCalledWith(1, 123);
expect($func)->toHaveBeenNthCalledWith(2, 42);

```

### toHaveLength(length)

Use `toHaveLength` to check that the size of an array, or the length of a string, is a certain numeric value.

```php
<?php

expect("Ciao")->toHaveLength(4);
expect([1,2, 3])->toHaveLength(3);
expect("")->not()->toHaveLength(42);
```

### toHaveMethod(name)

Use `toHaveMethod` to check that the class method exists.
This matcher uses [method_exists](https://www.php.net/manual/en/function.method-exists.php) underneath.

```php
<?php

expect(Dummy::class)->toHaveMethod('doSomething');
```

### toThrow(?message)

Use `toThrow` to test that a function throws when it is called.
For example, if we want to test that `getPizzaIngredients` throws an error when the pizza is unknown.

```php
<?php

function getPizzaIngredients(string $name) {
    $pizze = [
        'Margherita' => ['Mozzarella', 'Pomodoro'],
        'Napolitana' => ['Origano', 'Mozzarella']
    ];

    return $pizze[$name] ?? throw new Error("Ingredients not found")
}

it("throws the error when we don't know the ingredients for the pizza requested", function() {
    expect(function() {
        return getPizzaIngredients('Diavola');
    })->toThrow();

    /**
     * You can provide an optional argument to test that a specific error  message is thrown:
     */
    expect(function() {
        return getPizzaIngredients('Diavola');
    })->toThrow("Ingredients not found");
})

```

**NOTE**: This method checks only the error message. If you want to check the type of Error thrown, use [toThrowError](#tothrowerrorerrorclass-message).

### toThrowError(errorClass, ?message)

Use `toThrowError` to test that a function throws a specific error class when it is called.
The difference with the `toThrow` is this method does also check that the error class thrown matches the expectation.
For example, if we want to test that `getPizzaIngredients` throws an error when the pizza is unknown.

```php
<?php

function getPizzaIngredients(string $name) {
    $pizze = [
        'Margherita' => ['Mozzarella', 'Pomodoro'],
        'Napolitana' => ['Origano', 'Mozzarella']
    ];

    return $pizze[$name] ?? throw new PizzaUnknown(name + " not found")
}

it("throws the IngredientsNotFound error when we don't know the ingredients for the pizza requested", function() {
    expect(function() {
        return getPizzaIngredients('Diavola');
    })->toThrowError(PizzaUnknown::class);

    /**
     * You can provide an optional argument to test that a specific error  message is thrown:
     */
    expect(function() {
        return getPizzaIngredients('Diavola');
    })->toThrowError(PizzaUnknown::class, "Diavola not found");
})
```
