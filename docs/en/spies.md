# Spies
Spies let you spy on a function or class method when it is called indirectly by some other code. It also allows you to change its behavior, if needed.
You can [spy a function](#spy-on-a-function) or [class method](#spy-on-a-class-method) by using the `\Verde\spyOn` helper method.

**NOTE**: Spies require [runkit7](https://github.com/runkit7/runkit7) to work!

## Syntax

both methods below return a [mock function](/en/mocks.html#mock-function)

### Spy on a function:

`$spy = spyOn(functionName)`

### Spy on a class method:

`$spy = spyOn(className, classMethod)`

## Example

Suppose you want to make a pizza, and so your function needs to retrieve the list of ingredients before baking everything.

```php
<?php

function getPizzaIngredients(string $name)
{
    $content = file_get_contents('http://example.com/api/v1/get-ingredients/?name=' . $name);

    return json_decode($content, TRUE); 
}

function bakePizza(array $ingredients)
{
    // here where the cooking happens :)
}

function makePizza(string $name)
{
    $ingredients = getPizzaIngredients($name);

    bakePizza($ingredients);
}
```

In this case you want make sure that `getPizzaIngredients` gets called first, and after `bakePizza`, so the test will looks like:

```php
<?php

use function Verde\expect;
use function Verde\spyOn;

test('retrieves the ingredient first and then bake the pizza', function () {
    $spyGetPizzaIngredients = spyOn('getPizzaIngredients');
    $spyBakePizza = spyOn('bakePizza');
    
    // We don't want to make the HTTP request
    $spyGetPizzaIngredients->mockReturnValue(['Mozzarella', 'Pomodoro']);

    makePizza('Margherita');

    // Here we make sure that the functions are called in the right order
    expect($spyGetPizzaIngredients)->toHaveBeenCalledBefore($spyBakePizza);
    
    // We can also check the arguments passed
    expect($spyGetPizzaIngredients)->toHaveBeenCalledWith('Margherita');
    expect($spyBakePizza)->toHaveBeenCalledWith(['Mozzarella', 'Pomodoro']);
});
```
