---
title: First application
layout: default
nav_order: 20
---
<h1>First application</h1>

- [USSD menus](#ussd-menus)
- [The default menus](#the-default-menus)
- [The Next menus](#the-next-menus)
  - [EnterUsername Menu](#enterusername-menu)
- [Default next menu](#default-next-menu)
- [Validation](#validation)
- [The saveAs method](#the-saveas-method)
  - [RegisterUser Menu](#registeruser-menu)
    - [The `before` method](#the-before-method)
- [Going further](#going-further)

## USSD menus

A USSD application consists of Menus also known as Screens, that follow each other and process the user's interaction in a USSD session. Building a USSD application will be actually implementing this interactions with the user. Rejoice allows you to build the USSD menus and implement how they follow each other in a simple and beautiful way. It allows you to have full control on the user's interaction, the user's answer to each menu.

Let's see how to create our USSD menus.
All your menus will be in the app/Menus folder (or sub-folders).

## The default menus

When you open the `app/Menus` folder, You will notice that there is already existing `Menu.php` and a `Welcome.php` files in it. Those two are the minimum for every application.

The `Menu.php` contains the `Menu` class which is the base menu class that all your menus will extend.

By default it is empty but you can put in it methods that you want to be available in all your menus.

The `Welcome.php` contains the `Welcome` menu which is the *default* entry point of your application. It is the first menu that will be displayed to the user. Hence, every application must have the Welcome menu.

A menu class consists of different predefined methods that control what is displayed and how is handled the logic on the menu. 

A menu Screen is divided in two parts: the `message` part at the top and the various choices, what we call `actions`, at the bottom.

So in our menu class, we will have a `message` method that controls the message that will be displayed and an `actions` method that controls the actions and their behaviors.

Open the Welcome.php file in your text editor. You will see the `message` method of the welcome menu. 

Feel free to modify the text returned. We will modify it to `"Welcome to the registration panel. Select an option"` (the sample application we are creating).

The second menu method that we will need is the `actions` method.
The `actions` methods defines the different available actions on the menu. For example in the actions method, we will define things like: if the user responds `1` go to this menu, if the user responds `0`, go back to the previous menu.
The action method returns an array of actions called the `action bag`.

Create the action method like this:

```php
public function actions()
{
    return [
        '1' => [
            'display' => 'Register',
            'next_menu' => 'enter_username'
        ]
    ];
}
```

As you can see an action bag is just a plain PHP array associating the expected user inputs to the next menus to call.
Here the action bag contains only one action associating the user input `1` to the menu `enter_username`. The `display` property defines the explanatory text the user will see associated to the input.
Our welcome menu looks like:

```php
namespace App\Menus;

class Welcome extends Menu
{
    public function message()
    {
        return "Welcome to the registration panel. Select an option";
    }

    public function actions()
    {
        return [
            '1' => [
                'display' => 'Register',
                'next_menu' => 'enter_username'
            ]
        ];
    }
}
```

And will look on phone like:

<div class="phone">
    <table>
        <tr>
            <td>Welcome to the<br>registration panel. <br>Select an option<br><br>1. Register</td>
        </tr>
        <tr>
            <td><input></td>
        </tr>
    </table>
</div>

The `next_menu` property defines the next menu to call if the user inputs `1` as response.
That's all for the welcome menu.

Let's create the next menu the user will see after selecting the option on the welcome menu. The next menu will ask the user for their name.

## The Next menus
In the actions on the welcome menu, we defined that, if the user chooses `1` we should take them to the `enter_username` menu. Let's then create that menu.

### EnterUsername Menu
Open the console in the project root folder. Run the command:

```php
php smile make:menu EnterUsername
```

This command will create a simple menu class in a newly created file `app/Menus/EnterUsername.php`.

You can add the flag `-x` to the command to create the menu without the comments around the method.

Run `php smile help make:menu` to see all the available flags that can help you modify the menu you are creating.
{: .note .note-info }

You will notice that the name of the menu class is in `PascalCase` when the name in the action bag is in `snake_case`. This is intentional and just to show that Rejoice can automatically guess the name of the class from it. In a real application you may use the true name of the menu directly 'EnterUsername'. Note that `enter-username`, or even `enter username` in the action bag will also produce the same result.
{: .note .note-info }

Now open the `EnterUsername.php` file.

In the message method, return the string `"Kindly enter your name"`.

Here the `actions` method is slightly different but actually it is the same as the one in the welcome menu. It is just using the in-built `withBack` helper method to return a predefined action bag.

The `withBack` helper method automatically adds a *go-back* action to the action bag so the user will be able to go back. You can still add your own actions to the `actions` variable.

On this menu we are not defining any custom action. This is because on this menu, we are not expecting any *exact* response from the user, as it was with the welcome menu. Here all we know is we want to get the name of the user. The user can input any name they want unlike on the welcome menu where the user must send exactly `1` so that we understand they want to register.

The *go-back* action being the only action on this menu, we can replace the actions method by:

```php
public function actions()
{
    return $this->backAction();
}
```

`backAction` is another helper method that returns an action bag containing only a go-back action and it is actually the method used under the hood by the `withBack` method.

## Default next menu
On the welcome menu, the next menu to call was specified in the action bag. Here we don't have any custom action, how do we specify the next menu we should call when the user sends a response? We will use the `defaultNextMenu` method of the menu class, which will return the name of the next menu.

```php
public function defaultNextMenu()
{
    return 'register_user';
}
```

So on any menu where the user can input something that is not exactly defined in the action bag we must set a default menu. If not, the user will get an Invalid input error when the input is different from what is in the action bag. If the response cannot be defined in the action bag, set at `defaultNextMenu` method.

## Validation
One must not be too confortable with the fact that we are letting the user go to the next menu no matter what they enter as name. It is true, and the menu class provide a method to validate the user input which is called... `validate`.

```php
public function validate()
{
    return 'alphabetic|min_len:3|max_len:50';
}
```
We are defining that the response should be all letters, with at least 3 characters and at most 50. This is the simplest yet equally-powerful way to validate in Rejoice. There are other way to validate. Learn more about the validation [here](menu/validation.md).

## The saveAs method
Every response sent by the user is stored in the session. You may want to modify the user's response before it is stored in the session. Let's say here we want to capitalise the name of the user before it is saved. We can easily do it using the `saveAs` method. It takes the user's response as parameter.

```php
public function saveAs($response)
{
    return ucfirst($response);
}
```

The saveAs method returns the modified response. Here we just make the first letter of the name uppercase. We can use a true capitalisation method provided by the `Str` class, the string utility-class that comes with Rejoice.
Import the `Str` class with the code:

```php
use Prinx\Str;
```
just after the namespace declaration.Let's its `capitalise` method:

```php
public function saveAs($response)
{
    return Str::capitalise($response);
}
```
To summarize, our EnterUsername menu looks like:
```php
namespace App\Menus;

use Prinx\Str;

class EnterUsername extends Menu 
{
    public function message()
    {
        return "Kindly enter your name";
    }

    public function actions()
    {
        return $this->backAction();
    }

    public function defaultNextMenu()
    {
        return 'register_user';
    }

    public function validate()
    {
        return 'alphabetic|min_len:3|max_len:50';
    }

    public function saveAs($response)
    {
        return Str::capitalise($response);
    }
}
```

As specify in the `defaultNextMenu` method, we want to call the menu `registerUser` as soon as the user give us their name.

Let's create that menu.

### RegisterUser Menu

Create a new menu called `RegisterUser`:
```php
php smile make:menu RegisterUser -x
```
Replace the `message` method by:

```php
public function message($previousResponses)
{
    $name = $previousResponses->get('enter_username');

    return "Thank you {$name}. You have successfully registered.";
}
```

The `$previousResponses` argument is an object containing all the previous responses given by the user. You access a specific response by using its `get` method with the name of the menu as argument. Just make sure the name of the menu is the same used in the `next_menu` of the action bag or in the `defaultNextMenu` method.

There are other slightly different ways of retrieving the user's response. Learn more about it [here](user-response).

This menu will be the last menu we want to show to the user. So it will not have any action on it. We just want to give a feedback to the user that their registration was successful (or not). And the session will be automatically killed. So we will delete the `actions` method.

This is how Rejoice determine that the menu is a last menu. To make a menu the last menu, just create the menu without an `actions` and `defaultNextMenu` method.
{: .note .note-info }

An `actions` or `defaultNextMenu` method in the menu means the user can perform actions on the menu, and then it is not a last menu. On the last menu, the user cannot perform any action. They just receive a feedback they can read and close the popup.
{: .note .note-info }

Our class looks then like:
```php
class RegisterUser extends Menu
{
    public function message()
    {
        $name = $this->previousResponses('enter_username');

        // Save the user name to the database here

        return "Thank you {$name}. You have successfully registered.";
    }
}
```

#### The `before` method

Our application is perfect so far. But we are saving the user's name to the database in the `message` method. This is actually not the recommend. While this is possible, the `message` method's role is not to interact with the database. Let's see some other ways we can display the last menu. We will make use of the `before` method of the menu class.

The `before` method also called `before hook` allows us to run *any* logic we want *before* the menu is processed and displayed to the user. We will typically do things like interacting to the database there.

```php
class RegisterUser extends Menu
{
    public function before()
    {
        $name = $this->previousResponses('enter_username');

        // Save the user name to the database here
    }

    public function message()
    {
        // The message will then just display the feedback to the user.

        $name = $this->previousResponses('enter_username');

        return "Thank you {$name}. You have successfully registered.";
    }
}
```

Now let's imagine that we were not able to save the name to the database. We surely don't want to show to the user that the registration was successful! As the `before` method runs *before* any other custom method of the menu, we can do something like:
{: .pt-2 }
```php
class RegisterUser extends Menu
{
    protected $registrationSuccessful = true;

    public function before()
    {
        $name = $this->previousResponses('enter_username');

        try {
            // Save the user name to the database here
        } catch (\Throwable $th) {
            $this->$registrationSuccessful = false;
        }
    }

    public function message()
    {
        return $this->$registrationSuccessful ? 
            "Thank you {$name}. You have successfully registered." : 
            "Sorry, an error happened! Kindly try again.";
    }
}
```
We now give to the user the proper feedback. And this is cool. We have a message method that tell us at glance what we expect the user to see and the other logic is in the before method.

But we can see that apart from saving and sending the message, we are not doing anything else. We can go further by moving the sending of the message to the `before` method itself and remove the `message` method. This is possible by using the in-built `respond` method of the menu class.

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

Calling the `respond` method has precedence on the `message` and `actions` method. It means when it is called, the `message` method is no more called.<br>
By calling the `respond` method, we no more need to worry about an actions method is there or not. Rejoice understands that the menu is a last menu and will send the message as soon as the before method is called, ignoring then any other method, including `message` and `actions`.
{: .note .note-warning }

Another thing about the `respond` method: it allows us to send a response to the user before to start any backend logic. It does not end the script. This is extremely useful. USSD protocol times out very quickly. It does not allow session to be alive for more than at best 180 seconds. Sometimes, the last menu will not show on the user's device because of that. Using the respond method is the first way of mitigating such a behavior. More info about the `respond` method [here](session#discard-session).
{: .note .note-info }

Congratulations! 🤸‍♀️ You have your first USSD application.

If you have configured your simulator as shown here, you can run the `php smile simulator:console` or `php smile simulator:web` to test the application.

## Going further
You must also want to learn about:

- [Menus](menu)
- [Using database in your application](database)
- [Pagination](menu/pagination)
- [Using database session](session)
- [Logging](logging)
- [Commands](commands)
- [Deploying your application](deployment)