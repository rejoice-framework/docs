---
title: Menu class
layout: default
parent: Menus
nav_order: 42
---

<h1>Menu class</h1>

- [The menu methods](#the-menu-methods)
  - [The `message` method](#the-message-method)
  - [The `actions` method](#the-actions-method)
  - [The `validate` method](#the-validate-method)
  - [The `saveAs` method](#the-saveas-method)
  - [The `before` hook](#the-before-hook)
  - [The `after` hook](#the-after-hook)
  - [The `onBack` hook](#the-onback-hook)
  - [The `onMoveToNextMenu` hook](#the-onmovetonextmenu-hook)
  - [Order of the methods](#order-of-the-methods)

We've created our first application and that was pretty cool and rather simple. I'm sure you will need more in your applications.
Now that we have all the basics, the rest will not be a big deal.

The first thing that worth knowing is Menu classes.
They are object representaion of a menu. The same menu we created in the `menus.php` file above. We will now create it using rather an object. Actually, the menu class does not replace the menu defined in the `menus.php` file. Both can coexist. But the parameters defined in the menu class will for most of them overwrite the ones defined in the menus.php file (except the message parameter, which will rather be combined).

To create a menu, use the command:

```php
php smile make:menu InspiringQuote
```

Here, `InspiringQuote` is the name of the menu.

This command will generate this menu class in the `Menus` folder:

```php
<?php

namespace App\Menus;

use App\Menus\Menu;

class InspiringQuote extends Menu
{
    /**
     * The message to display at the top of the screen.
     *
     * @param Rejoice\Foundation\UserResponse $previousResponses
     * @return string|array
     */
    public function message($previousResponses)
    {
        return '';
    }

    /**
     * The actions to display at the bottom of the top message.
     *
     * @param Rejoice\Foundation\UserResponse $previousResponses
     * @return array
     */
    public function actions($previousResponses)
    {
        $actions = [];

        return $this->withBack($actions);
    }
}
```

You can generate the menu with other menu methods apart from the default `message` and `actions` methods. Use `php smile help make:menu` to know more about it.

### The menu methods

#### The `message` method

By convention, the menu screen is divided in two parts, the message and the actions:

<div class="phone">
    <table>
        <tr>
            <td>This is the message<br>Select an option<br><br>1. Action 1<br>2. Action 2<br>3. Action 3<br>0. Action 4</td>
        </tr>
        <tr>
            <td><input></td>
        </tr>
    </table>
</div>

The `message` method will generate whatever will be displayed in the message part of the menu. It is the equivalent of the `message` parameter of a menu in the `menus.php` resource file.

This method must return a string or an array of placeholders mapped to their values.

If a string is returned, the string will be combined to any message of the same menu from the menu resource file.
If an array is returned, Rejoice will assume you their are placeholders in the message from the menu resource file and will attempt to change this placeholders with their respective values. You can create a placeholder like this:

```php
// resources/menus/menus.php

return [
    'welcome' => [
        //
    ],

    'GetName' => [
        //
    ],
    
    'DisplayUserId' => [
        'message' => 'You are the user number :user_number:',
    ],
];
```

Then in the `DisplayUserNumber` menu class:

```php
<?php

namespace App\Menus;

use App\Menus\Menu;

class DisplayUserId extends Menu
{
    public function message()
    {
        $name = $this->previousResponses('GetName');

        $userNumber = User::where('name', '=', $name)->value('id');

        return [
            'user_number' => $userNumber,
        ];
    }

    //
}
```

#### The `actions` method

The actions method displays the actions part of the menu screen.

The method must return an action bag. An action bag is a normal PHP array. It looks like:

```php
<?php

namespace App\Menus;

use App\Menus\Menu;

class DisplayUserId extends Menu
{
    //

    public function actions()
    {
        $actions = [
            '1' => [
                'display' => 'Send ID to your email.',
                'next_menu' => 'SendIdToEmail'
            ],
        ];

        return $actions;
    }
}
```

The action bag contains each predefined response of the user mapped to a short message to display near the predefined response and the next menu to call when the user selects that option. Learn more about menu actions [here](actions).

#### The `validate` method

The validate method allow to easily apply validation rules to the response of the user. Rejoice ships with some amazing validation rules ready to use. Yet, you can easily implement your custom validation to the response. Just return `true` if the validation passes and `false`, if not. You can learn more [here](validation).

#### The `saveAs` method

The `saveAs` method allows to change the response of the user before saving it. By default the input of the user is saved as the response. By you can easily save another value, based on the response of the user, by using this method.

```php
// Example
public function saveAs($response)
{
    $goodLength = 15;

    if (strlen($response) < $goodLength) {
      return str_pad($response, $goodLength, '_');
    }

    return $response;
}
```

This method will be used only if the response of the user is not predefined. For example, when you are requesting for the user's name. When the response of the user is predefined (in the actions), you will rather use the `save_as` parameter of the actions.

#### The `before` hook

The before method allows to run any logic that must run before the menu is displayed. Sometimes, this method will be used to directly send the message to the user. In those cases, the message and actions are no more needed. This situation happens most of the time on the last screen of the application.

#### The `after` hook

The after method allows to run any logic that must run after the user has sent back a response.

#### The `onBack` hook

The onBack method run when the user is going back.

#### The `onMoveToNextMenu` hook

The onMoveToNextMenu method allows to run any logic that must run when the user is actually moving to the following menu. This method has the same purpose as the after hook with the only difference that it will not run if the user is moving to a menu back in the history.

#### Order of the methods

In order to know very well how to use this methods, it is very important to know the order in which they are called.
This is the order in which the menu class methods are called.

Methods called before the menu is rendered to the user:

- `before`
- `message`
- `actions`

After the menu is rendered to the user **and the user has sent a response back**:

- `validate`
- `saveAs`
- `after`
- `onMoveToNextMenu`

<div class="note note-primary">
You need to consider this:

A property created or updated in a `before` method (before, message or actions) will not be available with the same valuein the `after` methods (validate, saveAs, after, onMoveToNextMenu.)

Any property created or updated in an after method will not be available with the same value in a method that has already run (before, message, actions).
</div>
