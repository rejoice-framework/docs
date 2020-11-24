---
title: User response
layout: default
nav_order: 60
---

<h1>User response</h1>

- [User response](#user-response)
  - [Retrieve the user current response](#retrieve-the-user-current-response)
  - [Retrieve a user's response from any menu](#retrieve-a-users-response-from-any-menu)
  - [Multiple responses](#multiple-responses)


## User response

Most of the times, you need to have access to the user's responses to be able to perform other actions like storing the user provided info in the database, etc.

### Retrieve the user current response

In a menu class you can easily access the current user's response using the method `userResponse`.

If you have modified the response by using the `saveAs` method or the `save_as` parameter, you can access the modified response by calling the method `userSavedResponse`. The userResponse method or userTrueResponse will always give the true response that the user gave will userSavedResponse will give the modified response if the response has been modified or the true response if the response has not been modified. 

```php
class EnterAge extends Menu
{
    public function message()
    {
        // Retrieve the response gave on the previous menu
        $name = $this->userResponse();

        return "Cool {$name}! Kindly enter your age:";
    }
}
```

The user response is automatically injected into the `validate` and `saveAs` methods.

```php
// Notice this is the enter name menu and not enter age as the previous one - A response is validated on its menu not another menu.
class EnterName extends Menu
{
    public function validate($response)
    {
        if (strlen($response) < 3) {
            $this->setError('The name must be at least 3 characters');

            return false;
        }

        return true;
    }
}
```

It is important to always see a menu class in two parts: the part that runs **before** the screen is rendered to the user and the part that runs **after** the user has sent the response.<br>Then when you request for the user response in the <em>before-rendering</em> part, you are actually requesting the response giving on the previous screen and based on that response you want to write your logic to render the screen for the current menu. When you request for the user response in the <em>after-response</em> part, you are requesting the response given on the current menu, maybe for validation purpose or to modify the response before saving it, etc.
{: .note .note-warning }

### Retrieve a user's response from any menu

You can easily have access to the response response of the user on any menu by using the `previousResponses` helper method.

You will pass the menu name to the method.

```php
public function before()
{
    $name = $this->previousResponses('enter_username');

    $this->respond("Thank you {$name}. You have successfully registered.");
}
```

Without argument, `previousResponses` returns an object that allows us to retrieve the user response.

The object has two useful methods: `get` and `has`.
`get` allows to retrieve a response.
`has` allows to check if a response exists (if a response associated to a particular menu exists.)

```php
public function before()
{
    $responses = $this->previousResponses();
    $name = $responses->has('enter_username') ? $responses->get('enter_username') : 'dear user';

    $this->respond("Thank you {$name}. You have successfully registered.");
}
```

**An exception will be thrown if the response does not exist**.

You can pass a second parameter as default value that will be returned if the response is not found or use the `has` method to check if the response exists before accessing it.

```php
public function before()
{
    $name = $this->previousResponses('enter_username', 'dear user');

    $this->respond("Thank you {$name}. You have successfully registered.");
}
```

Or Using the `get` method:

```php
public function before()
{
    $name = $this->previousResponses()->get('enter_username', 'dear user');

    $this->respond("Thank you {$name}. You have successfully registered.");
}
```

### Multiple responses

Sometimes it is useful to have multiple answers for the same menu. Let's take the example of a simple e-commerce application where the user can choose multiple products. To have access to all the chosen products.

Having access to all the responses on a menu is as simple as calling the `getAll` method on the object returned by `previousResponses`.

```php
public function before()
{
    $products = $this->previousResponses()->getAll('choose_product');

    // Process the chosen products here

    $this->respond("Your order is being processed.");
}
```

The `getAll` method returns an array containing the responses of the user on the `choose_product` menu. The first response is at the first index, the second at the second index, etc.

See it in activity in this [sample application](samples/e-commerce).
