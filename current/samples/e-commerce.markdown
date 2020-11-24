---
title: Simple e-commerce
layout: default
parent: Sample applications
nav_order: 153
---

<h1>Simple e-commerce application</h1>

- [Simple e-commerce application](#simple-e-commerce-application)
  - [Advanced manipulations of the user's responses](#advanced-manipulations-of-the-users-responses)

## Simple e-commerce application

Anywhere in a menu entity, you can access previous responses of the user by using the method `previousResponses` of the menu entity.
The method returns a UserPreviousResponse object that holds all the previous responses of the user and allows you to access. The object returned has two main methods that allows you to access the responses: `get` and `has`. Both methods take as parameter the name of the menu for which you want to retrieve the response.
`has` as you can guess, checks if there is a response for this particular menu.
`get` gets the response for this particular menu.

```php
class EnterAge extends Menu
{
    //

    public function validate($response)
    {
        if ($this->previousResponses()->has('has_a_permit')) {
            return true;
        }
        
        $age = intval($response);
        $wantToDrive = $this->previousResponses()->get('want_to_drive');
        
        if ($wantToDrive && $age > 16) {
            $this->setError('Only adults are allowed to drive.');
            return false;
        }

        return true;
    }
}
```
Some methods of the menu entity get automatically the previousResponses object. as argument. You can decide to use them as argument or stick to the method. Both will give the same result.
The methods `before`, `after`, ``,
Calling `get` on a non existent menu may will throw an Exception.

### Advanced manipulations of the user's responses
Let's suppose you are creating an application in which users can place orders. You want to allow the users to place multiple orders during the same session on the USSD, you need a way of remembering all the previous responses of the user. The framework allows you to implement such logic very easily.

Actually anytime you come to a screen, the response provided by the user is saved in a responses array for that particular menu. You can then get all the responses provided for a same particular menu by calling the `getAll` method on the object returned by the `previousResponses` method.

```php
class ProcessOrder extends Menu
{
    public function before($previousResponses)
    {
        $definedPlateCosts = [
            'beans' => 7,
            'fufu' => 12,
            'jollof' => 10,
            'spaghetti' => 9,
        ];

        // Returns for example ['jollof', 'beans']
        $orderedMeals = $previousResponses->getAll('choose_meal');

        // Returns for example [2, 5]
        $plateNumbers = $previousResponses->getAll('enter_number_plate');

        $cost = 0;
        foreach ($orderedMeals as $key => $meal) {
            $cost += $definedPlateCosts[$meal] * $plateNumbers[$key];
        }

        $message = 'You have ordered ' . implode(',', $orderedMeals) . 
        '. The total cost is '.$cost.
        '. Your request is been processed.';

        $this->terminate($message)
    }
}
```
You can get a specific response by passing as second parameter to the `get` method the index of the response:
```php
class ProcessOrder extends Menu
{
    public function before($previousResponses)
    {
        $secondOrderedMeal = $previousResponses->get('choose_meal', 1); // Yes 1. Remember we count from 0 :)
        $this->terminate('You second meal is ' $secondOrderedMeal)
    }
}
```