# Expect

The `expect` helper allows to validate a given value against a number of matches.

## Syntax

`expect(receivedValue, customMessage = '')->{matcher}(?expectedValue);`

**Parameters**:

|Name|Type|Description|
|---|---|---|
|receivedValue|mixed/callback|The value expected.<br />When passing a callback its return value will be used as expectation|
|customMessage|string|The optional message to print in case the test fails|

For the full list of matchers see [Matchers](matchers.html)

## Example

Usage with a value

```php
<?php

use function Verde\expect;

$answer = 43;
expect($answer, 'The Answer to Everything')->toBe(42);

$callback = function() {
    return false;
};

expect($callback())->not()->toBeTrue();
```
