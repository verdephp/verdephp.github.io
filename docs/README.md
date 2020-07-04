---
home: true
heroText: Verde
tagline: a BDD Style Library for your PHP Tests
actionText: Get Started →
actionLink: /en/getting-started.html
features:
    - title: BDD Style
      details: Easy and clear syntax inspired to Jest and Jasmine
    - title: Spy Everything
      details: Spy on Function and Classes with the power of runkit
    - title: Easy mocking
      details: Easy to use syntax for mocking class  
footer: MIT Licensed | Copyright © 2020-present Alessandro Senese aka Ceceppa
---

## Quick start

```sh
composer require "verdephp/verde" --dev
```

## BDD Style syntax

Easy and clear syntax inspired to Jest and Jasmine

```php
<?php
use function Verde\expect;

$answer = 42;
expect($answer, 'The Answer to Everything')->toBe(42);
```


## Spy Everything

Easy and clear syntax inspired to Jest and Jasmine

```php
<?php
use function Verde\expect;
use function Verde\spyOn;

function getAnswerToEverything() {
    return 42;
}

$spy = spyOn('getAnswerToEverything'); 

getAnswerToEverything();
expect($spy)->toHaveBeenCalledWith();
```

**NOTE**: Spies require [runkit7](https://github.com/runkit7/runkit7) to work!

## Easy mocking

Easy to use syntax for mocking class

```php
<?php
use function Verde\expect;
use function Verde\mock;

class DummyClass {
    public function doSomething(): string
    {
        return 'done';
    }
}

function doIt() {
    $dummy = new \Verde\Test\DummyClass();
    
    return $dummy->doSomething();
}

$func = func(function () { return 'ciao'; });

$mock = mock(DummyClass::class, [
    'doSomething' => $func
]);

$result = doIt();

expect($func)->toHaveBeenCalledWith();
expect($result)->toBe('ciao');
```

**NOTE**: Mocking a class requires [runkit7](https://github.com/runkit7/runkit7) to work!
