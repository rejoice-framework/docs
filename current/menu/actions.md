---
title: Menu actions
layout: default
parent: Menus
nav_order: 43
---
<h1>Actions</h1>

- [Introduction](#introduction)
- [The action bag](#the-action-bag)
- [The actions](#the-actions)
- [The action trigger](#the-action-trigger)
- [Actions properties](#actions-properties)
  - [The display property](#the-display-property)
  - [The `next_menu` property](#the-next_menu-property)
  - [The `save_as` property](#the-save_as-property)
- [Default next menu](#default-next-menu)
- [Predefined next menus - Magic menus](#predefined-next-menus---magic-menus)
- [Inserting predefined actions](#inserting-predefined-actions)
  - [Inserting Back action](#inserting-back-action)
  - [Inserting Welcome action](#inserting-welcome-action)
  - [Inserting Paginate forward action](#inserting-paginate-forward-action)
  - [Inserting Paginate back action](#inserting-paginate-back-action)
- [Later menu](#later-menu)
- [Calling a remote USSD application](#calling-a-remote-ussd-application)

## Introduction

Actions allowed the user to navigate through your application and allow us to get information from the user.

## The action bag

All the actions on a menu are contained in an array called actions bag. The action bag is either returned by the `actions` method of the menu class or is the value of the `actions` index in the menu resource of the specific menu.

## The actions

Each item of the action bag is an action. An action is defined by mapping a trigger to a set o properties.

## The action trigger

The trigger is what the user is supposed to input. It must be a string. The action trigger is usually a number, but you can use any string (inputable by the user) as trigger. The trigger is an index of the actions bag and is mapped to the action which is also an array.

```php
return [
    'my_menu_screen' => [

        // Action bag
        'actions' => [

            // '1' is the trigger
            '1' => [

                // Action properties
            ]
        ]
    ]
];
```

In a menu class:

```php
public function actions()
{
    // Action bag
    $actions = [

        // '1' is the trigger
        '1' => [

            // Action properties
        ]
    ];

    return $actions;
}
```

An integer also will work as action trigger and may be useful for automatically reodering the actions whenever you make a change. But note that the first action displayed will be `0`.
{: .note note-info }

## Actions properties

An action has several properties that allow us to have full control on the navigation in our application and full control over the interaction with the user, especially when requesting information from the user.

These are the properties that an action can take:

- `display`
- `next_menu`
- `save_as`

### The display property

The display property of the action is an very short text indicating what is the action about. It is the text that will be displayed alongside the action trigger on the user's phone.

```php
return [

    'payment_option' => [
        'message' => 'Choose payment mode:',

        'actions' => [
            '1' => [
                'display' => 'Mobile money'
            ],
            '2' => [
                'display' => 'Cheque'
            ],
            '3' => [
                'display' => 'Paypal'
            ]
        ]
    ]
];
```

This will render as:
<div class="phone">
    <table>
        <tr>
            <td>Choose payment mode<br><br>1. Mobile Money<br>2. Cheque<br>3. Paypal</td>
        </tr>
        <tr>
            <td><input></td>
        </tr>
    </table>
</div>

### The `next_menu` property

The next menu property of an action is the property that allows us to implement navigation through our menus. It configuration is simple as providing the name of the menu we want to go to.

```php
return [

    'payment_option' => [
        'message' => 'Choose payment mode',

        'actions' => [
            '1' => [
                'display' => 'Mobile money',
                'next_menu' => 'process_payment',
            ],
            '2' => [
                'display' => 'Cheque'
                'next_menu' => 'process_payment',
            ],
            '3' => [
                'display' => 'Paypal'
                'next_menu' => 'process_payment',
            ]
        ]
    ],

    // process_payment menu
    'process_payment' => [
        //
    ]
];
```

If you are using menu classes, this syntax assumes that the menu class is directly in the `app/Menus` folder. If the menus is in a sub-folder of the `app/Menus` folder, the folders names will be separated by `::` (two colons).

```php
// app/Menus/UserProfile/ShowProfile.php

namespace App\Menus\UserProfile;

use App\Menus\Menu;

class ShowProfile extends Menu
{
    public function actions()
    {
        return [
            '1' => [
                'display' => 'Edit Profile',
                'next_menu' => 'UserProfile::ShowProfile',
            ],
            '2' => [
                'display' => 'Delete profile'
                'next_menu' => 'UserProfile::DeleteProfile',
            ],
        ];
    }
}
```

These assume that classes `ShowProfile` and `DeleteProfile` exist in the `app/Menus/UserProfile` folder.

### The `save_as` property

The `save_as` property allows us to modify the response of the user before it is saved in the session.

In our previous example, if the user want to choose `Mobile Money`, they will send `1` and it will be saved in the session as `1`. But that is not very useful to as. We will need to know the option chosen by the user in a following menu and `1` is not really meaningful. We can use the `save_as` property to save a custom response when the user will send their response:

```php
return [

    'payment_option' => [
        'message' => 'Choose payment mode',

        'actions' => [
            '1' => [
                'display' => 'Mobile money',
                'next_menu' => 'process_payment',
                'save_as' => 'Pay via mobile money',
            ],
            '2' => [
                'display' => 'Cheque'
                'next_menu' => 'process_payment',
                'save_as' => 'Pay via cheque',
            ],
            '3' => [
                'display' => 'Paypal'
                'next_menu' => 'process_payment',
                'save_as' => 'Pay via paypal',
            ]
        ]
    ],

    // process_payment menu
    'process_payment' => [
        //
    ]
];
```

and now in the `ProcessPayment` menu class we can process show an appropriate menu depending on the user's choice.

```php
class ProcessPayment extends Menu
{
    public function actions()
    {
        $paymentOption = $previousResponses->get('payment_option');

        switch ($paymentOption) {
            case 'pay_via_mobile_money':
                    // Return a custom action
                break;
            case 'pay_via_cheque':
                    // Return a custom action
                break;
            case 'pay_via_paypal':
                    // Return a custom action
                break;
        }
    }
}
```

You can do theses checks in any other menu class method.

<a name="default-next-menu"></a>

## Default next menu

Till now, our examples assume that we want the user to send a specific response (`1`, `2`, `3`, etc.)

On some menus, the response of the user is not known in advance or not exactly known. We then need to allow the user to enter any value that we will validate to check if it complies to the type of the value we want.

You can learn about user response validation [here](validation).
{: .note .note-info }

On such menus, we can no more define a next menu in the actions bag. So we need a way to allow the user to send any value even though it is not specified in the actions bag. It is possible by defining the property `default_next_menu` directly in the menu (and not in the actions) or by using the `defaultNextMenu` in the menu class.

You configure the `default_next_menu` the exact way the `next_menu` property of an action is configured. It means the `default_next_menu` will just contain the name of the next menu we want to go to. In a menu class, the `defaultNextMenu` will return the name or the next menu.

## Predefined next menus - Magic menus

Rejoice provides and automatically handles some predefined menu for you to allow you to concentrate on the actual application menus. Those menus are called **magic menus**. Their names starts with double undescore.

These are all the magic menus that Rejoice ships:

- `__back` for going back
- `__welcome` for returning to the main menu
- `__end` to return a last menu message (a.k.a end the session)
- `__same` for displaying the same menu again
- `__paginate_forward` for implementing a forward pagination (this is used by the [Paginator](#implementing-pagination))
- `__paginate_back` for implementing a back pagination (this is used by the [Paginator](#implementing-pagination))

Those are the magic menus you can use directly in your application to navigate. There are other magic menus that are reserved to the framework:

- `__split_next` When a menu overflows, Rejoice automatically detects it and splits it under the hood for you. `__split_next` is the name that will be used to reference any next screen of the splitted menu.
- `__split_back` will be used to reference any back screen of a splitted menu.
- `__continue_last_session` The name of the *continue from last session* menu.

<div class="note note-danger">It is highly recommended not to name any of your own menu with double underscore at the beginning!</div>

## Inserting predefined actions

The available predefined actions are:

- backAction
- mainMenuAction
- paginateForwardAction
- paginateBackAction

### Inserting Back action

You can add a go-back action to your actions by using the `__back` next menu or by calling the `$this->backAction()` method.

```php
namespace App\Menus;

class ProcessBalanceRequest extends Menu
{
    public function actions($previousResponses)
    {
        $actions = [
            // Define some actions here (or not)

            // Back action
            '0' => [
                'display' => 'Back',
                'next_menu' => '__back',
            ]
        ];

        return $actions;
    }
}
```

The trigger is `0` and the message attached is `Back`.

or

```php
namespace App\Menus;

class ProcessBalanceRequest extends Menu
{
    public function actions($previousResponses)
    {
        $actions = [
            //
        ];

        // Add back action
        return $this->mergeAction($actions, $this->backAction());
    }
}
```

or

```php
namespace App\Menus;

class ProcessBalanceRequest extends Menu
{
    public function actions($previousResponses)
    {
        $actions = [
            //
        ];

        return $this->withBack($actions);
    }
}
```

If only the back action will be displayed, you can directly return the back action.

```php
namespace App\Menus;

class ProcessBalanceRequest extends Menu
{
    public function actions($previousResponses)
    {
        return $this->backAction();
    }
}
```

The backAction method can take two arguments, the trigger and the display message associated.
By default the trigger is `0` and the display message is `Back`.

```php
namespace App\Menus;

class ProcessBalanceRequest extends Menu
{
    public function actions($previousResponses)
    {
        return $this->backAction('99', 'Previous menu');
    }
}
```

The method will return the exact array that we harcoded in the first example.
You can modify the trigger by passing your custom trigger to the method as first parameter. You can custom the message attached by passing the sustom message as second parameter.

### Inserting Welcome action

You can add a go-to-welcome action to your actions by using the `__welcome` next menu or by calling the `$this->mainMenuAction()` method.

```php
namespace App\Menus;

class ProcessBalanceRequest extends Menu
{
    public function actions($previousResponses)
    {
        $actions = [
            //...

            '00' => [
                'display' => 'Main menu',
                'next_menu' => '__welcome',
            ]
        ];

        return $actions;
    }
}
```

The trigger is `00` and the message attached is `Main menu`.

Or

```php
namespace App\Menus;

class ProcessBalanceRequest extends Menu
{
    public function actions($previousResponses)
    {
        $actions = [
            //
        ];

        return $this->mergeAction($actions, $this->mainMenuAction());
    }
}
```

If only the main menu action will be displayed, you can directly return it.

```php
namespace App\Menus;

class ProcessBalanceRequest extends Menu
{
    public function actions($previousResponses)
    {
        return $this->mainMenuAction();
    }
}
```

The `mainMenuAction` method can take two arguments, the trigger and the display message associated.
By default the trigger is `00` and the display message is `Main menu`.

### Inserting Paginate forward action

You can add a go-forward-in-pagination action by using the `__paginate_forward` next menu or by calling the `$this->paginateForwardAction()` method.

```php
namespace App\Menus;

class ProcessBalanceRequest extends Menu
{
    public function actions($previousResponses)
    {
        $actions = [
            //...

            '00' => [
                'display' => 'More',
                'next_menu' => '__paginate_forward',
            ]
        ];

        return $actions;
    }
}
```

Since we are going back, we are using the same trigger and message attached as the normal go-back action. The trigger is `0` and the message attached is `Back`.

or

```php
namespace App\Menus;

class ProcessBalanceRequest extends Menu
{
    public function actions($previousResponses)
    {
        $actions = [
            //
        ];

        return $this->mergeAction($actions, $this->paginateBackAction());
    }
}
```

### Inserting Paginate back action

You can add a go-to-welcome action to your actions by using the `__paginate_back` next menu or by calling the `$this->paginateBackAction()` method.

```php
namespace App\Menus;

class ProcessBalanceRequest extends Menu
{
    public function actions($previousResponses)
    {
        $actions = [
            //...

            '0' => [
                'display' => 'Back',
                'next_menu' => '__paginate_back',
            ]
        ];

        return $actions;
    }
}
```

Since we are going back, we are using the same trigger and message attached as the normal go-back action. The trigger is `0` and the message attached is `Back`.

or

```php
namespace App\Menus;

class ProcessBalanceRequest extends Menu
{
    public function actions($previousResponses)
    {
        $actions = [
            //
        ];

        return $this->mergeAction($actions, $this->paginateBackAction());
    }
}
```

## Later menu

Later menu is a powerful concept that helps you to modify the normal flow of the menu. Actually, every menu defined is attached to another menu through the `next_menu` parameter. It restrict you to use a menu for a specific purpose out of it flow. The later menu bypass that limitation and allows you to call a menu outside of its flow. View from other side, it allows us to create a menu that is not dependent on a particular flow but is rather *alone* and used by multiple flows. This is a scenario to help understand the concept and a use case of a later menu.

```php
// resources/menus/menus.php

return [

    'choose_option_screen' => [
        'message' => 'Select an option'
        'actions'=> [
            '1' => [
                'display' => 'Get balance',

                // Notice the format of the next_menu
                'next_menu' => [
                    'menu' => 'next_menu_name',
                    'later' => 'menu_to_call_after_next_menu',
                ],
            ]
        ]
    ],

];
```

In a menu class:

```php
// app/Menus/chooseOptionScreen.php

namespace App\Menus;

class chooseOptionScreen extends Menu
{
    public function message()
    {
        return '';
    }

    public function actions()
    {
        return [
            '1' => [
                'display' => 'Get balance',
                'next_menu' => [
                    'menu' => 'next_menu_name',
                    'later' => 'menu_to_call_after_next_menu',
                ],
            ]
        ];
    }
}
```

**The later menu can be either an array or a string.**
{: .pt-3 }
If an array, the menus inside the array will be called one after the other till the end of the array. It's important to know that a later menu has priority on the normal flow. Whenever the later menu stack is not empty, Rejoice will pick the next menu from the later menu stack till the stack is empty before returning to the normal flow.

In case you are using the `defaultNextMenu` method or the `default_next_menu` index of the menu resource, defining a later menu is the same:

```php
// resources/menus/menus.php

return [

    'enter_username' => [
        'message' => 'Kindly enter your name',
        'default_next_menu' => [
            'menu' => 'next_menu_name',
            'later' => 'later_menu'
        ]
    ],

];
```

In a menu class:

```php
// app/Menus/EnterUsername.php

namespace App\Menus;

class EnterUsername extends Menu
{
    public function message()
    {
        return 'Kindly enter your name';
    }

    public function defaultNextMenu()
    {
        return [
            'menu' => 'next_menu_name',
            'later' => 'later_menu'
        ];
    }
}
```

## Calling a remote USSD application

You can call a remote USSD simply by setting the URL of the USSD application as *next_menu* to an action.

```php
// resources/menus/menus.php

return [

    'choose_option_screen' => [
        'message' => 'Select an option'
        'actions'=> [
            '1' => [
              'display' => 'Get balance',
              'next_menu' => 'https://address.to/the/remote/ussd',
            ]
        ]
    ]

];
```

In a menu class:

```php
// app/Menus/ChooseOptionScreen.php

namespace App\Menus;

class ChooseOptionScreen extends Menu
{
    public function message()
    {
        return 'Select an option';
    }

    public function actions()
    {
        return [
            '1' => [
                'display' => 'Get balance',
                'next_menu' => 'https://address.to/the/remote/ussd',
            ]
        ];
    }
}
```

<div class="note note-warning">
It's important to know that as soon as the USSD has switched to a remote USSD, all subsequent requests will be routed to that remote USSD and this current USSD will be just a relay. No menu of the current USSD can be called again. Everything happens as if the current USSD application has completely handed over to the remote USSD.</div>