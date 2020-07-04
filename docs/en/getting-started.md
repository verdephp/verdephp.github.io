# Getting started

Install Verde using [composer](https://getcomposer.org):

```sh
composer require "verdephp/verde" --dev
```

NOTE: [runkit7](https://github.com/runkit7/runkit7¬) is needed for Spy and Mocks on functions and classes. Follow the github [installation instructions](https://github.com/runkit7/runkit7#installation) to install and enable it.

## BDD Style

The library provides the `expect` helper method for writing BDD style tests, example:

```php
<?php
use function Verde\expect;

$answer = 43;

// error: 
// expected(expected)->toEqual(received)
//
// Expected: 43
// Received: 42.
expect($answer)->toBe(42);

// You can also include a custom assert message
expect($answer, 'The Answer to Everything')->toBe(42);
```

## Our first test

Let's write a function that sums two numbers:

```php
function sum($number1, $number2)
{
    return $number1 + $number2;
}
```

Now let's write a test for it:

```php
class TestSum extends PHPUnit_TestCase
{
    public function test_sum_one_and_two_equals_three() {
        $result = sum(1, 2);

        expect($result)->toBe(3);
    }
}
```

If you're using [pestphp](https://pestphp.com/):

```php
it('sum 1 + 2 returns 3', function() {
    $result = sum(1, 2);

    expect($result)->toBe(3);
});
```

That's all, easy not?

### What's next?

To learn more about the other things that _Verde_ can do, see [Expect](expect.md), [Matchers](matchers.html), [Spies](/en/spies.html) and [Mocks](/en/mocks.html).
