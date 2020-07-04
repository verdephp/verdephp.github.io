# ANY

The class `\Verde\ANY` provides some constants that can be used inside [toBe](/en/matchers.html#tobe-value),
[toHaveBeenCalledWith](/en/matchers.html#tohavebeencalledwith-arguments) or [toHaveBeenNthCalledWith](/en/matchers.html#tohavebeencalledwith-arguments), 
to check that the type of the expected and received values matches.

For example, you want to check the output random generator function that can returns either a random number or a string, according to its parameter. 
In this case, you cannot predict what the exact output is. So, for this, can be useful to check that the output type matches your expectation:

```php
<?php

function getRandomNumber($random_float: bool)
{
    return $random_float ? (mt_rand() / mt_getrandmax()) : uniqid();
}

// We want to make sure that our function returns the expected type
expect(getRandomNumber(true))->toBe(ANY::FLOAT);
expect(getRandomNumber(false))->toBe(ANY::STRING);
```

Another usage is with the `toHaveBeenCalledWith` and `toHaveBeenNthCalledWith` functions.
For example, if you want to check that a mock function is called with a number:

```php
<?php

function randomCall($callback) {
  return $callback(floor(rand() * 6 + 1));
}

test('randomCall calls its callback with a number', () => {
  $fn = func();

  randomCall($fn->getCallable());

  expect($fn)->toHaveBeenCalledWith(ANY::FLOAT);
});
```

## Constants

This is the list of the types can be used for your expectation.

- ARRAY
- BOOL
- CALLABLE
- COUNTABLE
- FLOAT
- INFINITE
- INT
- ITERABLE
- NUMERIC
- OBJECT
- RESOURCE
- STRING

*NOTE*: All the constants uses the equivalent PHP `is_[CONSTANT name]` underneath.
