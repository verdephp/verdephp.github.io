# Getting started

Install Verde using [composer](https://getcomposer.org):

```sh
composer require "verdephp/verde" --dev
```

NOTE: [runkit7](https://github.com/runkit7/runkit7¬) is needed for Spy and Mocks on functions and classes. Follow the github [installation instructions](https://github.com/runkit7/runkit7#installation) to install and enable it.

## Our first test

Let's write a function that sums two numbers:__

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

To learn more about the other things that **Verde** can do, see [Expect](/en/expect.html), [Matchers](/en/matchers.html), [Spies](/en/spies.html) and [Mocks](/en/mocks.html).
