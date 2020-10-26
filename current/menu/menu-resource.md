---
title: Menu resource
layout: default
parent: Menus
nav_order: 41
---

<h1>Menu resource</h1>

- [Introduction](#introduction)
- [Format](#format)
- [The first application with menu resource](#the-first-application-with-menu-resource)

## Introduction

If you have followed the first application tutorial, you surely know how to create menu with classes. But actually, there is another simpler way of creating menus. That is the menu resource file.

A menu resource is just an array representation of a menu, the same way the menu class is an object representaion of a menu.

It is useful for menu on which there is no logic, menus that have for only purpose display a static information and get an eventual user response.

The menus resource are defined in the `resources/menus/menus.php` file. The file returns an associative array containing the menus. The indexes of the array are the name of the menus.

A menu screen is composed of two main parts: the **message** and the **actions**.

A menu without any action will be considered as an end menu, as the user can no more input something. The session will then be ended.

The array returned in the `resources/menus/menus.php` file must contain `welcome` menu. The welcome menu is the only menu required.

Actually, the welcome menu in the menu resource can be omitted, if there is a welcome menu class.
{: .note .note-info }

The last thing to know about the menus resource file is, unlike the menu classes, all the menus must be in the `menus.php` file.

## Format
```php
// resources/menus/menus.php

return [

    'menu_name' => [
        'message' => '',

        'actions' => [
            '1' => [
                'display'   => '',
                'next_menu' => '',
                'save_as'   => ''
            ],
        ],

        'default_next_menu' => '',
        'validate'          => '',
        'save_as'           => ''
    ],

    //...
];
```

All the indexes are optional and depend on the menu that is being created.

## The first application with menu resource

Let's rewrite the [first application]({{ site.url }}{{ site.baseurl }}/current/first-application.html) using the menu resource.

```php
// resources/menus/menus.php

return [
  
    'welcome' => [
        'message' => 'Welcome to the registration panel. Select an option',

        'actions' => [
            '1' => [
                'display'   => 'Register',
                'next_menu' => 'enter_username'
            ]
        ]
    ],
  
    'enter_username' => [
        'message' => 'Kindly enter your name',

        'actions' => [
            '0' => [
                'display' => 'Back',
                'next_menu' => '__back'
            ]
        ],

        'validate'          => 'alphabetic|min_len:3|max_len:50'
        'default_next_menu' => 'register_user',
    ],

    'register_user' => [
        'message' => '',
    ],
];
```

That's the whole application. But you have surely noticed that certain we could not replicate all the implementation from the menu classes:

- The `welcome` menu was fully implemented.
- On, the `enter_username` Everything was implemented, except from the `saveAs`. We will rely on the Menu class for that. (The menu resource and the class are compatible and can coexist.)
- On the last menu, the `register_user` menu, we will also rely on the menu class to save the user in the database and also send the right response to the user (whether saving to the database was successful or not.) So the message is empty and actually we can delete it.

We no more need the `welcome` menu class. It can be deleted as it is all handled in the menu resource.

We still need the `EnterUsername` class and it will be:

```php
namespace App\Menus;

use Prinx\Str;

class EnterUsername extends Menu 
{
    public function saveAs($response)
    {
        return Str::capitalise($response);
    }
}
```

The `RegisterUser` class will remain the same.

```php
class RegisterUser extends Menu
{
    public function before()
    {
        $name = $this->previousResponses('enter_username');

        try {
            // Save the user name to the database here

            $this->respond("Thank you {$name}. You have successfully registered.");
        } catch (\Throwable $th) {
            $this->respond("Sorry, an error happened!");
        }
    }
}
```

As you can see, for a very simple application that just display information, using the menu resource is ok and very useful. But as soon as the application handles some back-end logic, we will need to rely on classes.

You can create a **menu resource first** application, where every menu is created first in the menu resource and is supported by a menu class only when there is a need. Or you can decide to go **the only-classes way**. Then you completely ignore the menu resource and do everything with classes.
