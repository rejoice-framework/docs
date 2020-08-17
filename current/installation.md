---
title: Installation
layout: default
nav_order: 10
---
<h1>Installation</h1>

- [Installation](#installation)
- [The app folder](#the-app-folder)
- [The public folder](#the-public-folder)
- [The config folder](#the-config-folder)
- [The simulator](#the-simulator)
- [Testing the application](#testing-the-application)

## Installation
To install Rejoice and craft your application, you need `composer` installed on your machine. If you don't have composer, you can get it [here](https://getcomposer.org). It will help us manage the dependencies of our application.

Then, create a Rejoice project by running in the console:

```php
composer create-project --prefer-dist rejoice/rejoice my-ussd-app
```
This will create a project in the `my-ussd-app` directory. 

That's all!

You can go into the new created folder.

## The app folder
In the new project folder, you can see an `app` folder. The `app` folder is where our code (functions, classes) will reside.

## The public folder

The `public` directory is where our application will run from. It is where the index of our application is. You then need to configure your web server's root directory to point to this `public` directory.

## The config folder
The configuration files of the application are in the `config/` folder.

Some of the configurations, especially the ones related to credentials can be configured from the `.env` file at the root of the project and referenced in the configuration file using the `env()` function, so that the are not committed in your version control system.

The default configurations are enough for a first application but feel free to go through the files, especially `app.php` and `menu.php`.

## The simulator
The USSD application is meant to be used on a phone with the USSD protocol that is handle by a mobile operator. Rejoice provides two simulators that will help us run and test our application before deploying it.

Let's configure the simulator. We need to set:
- the application URL,
- the test phone number,
- the network [MNC](glossary#mnc) of the phone number (optional),
- the [USSD](glossary#ussd) code on which the application will be available (optional).

Open the .env file at the root of the project and search for these lines and replace the values by your custom values (or use the default values provided here).

```java
APP_ENV=dev

# The Application URL
APP_URL=http://localhost/my-ussd-app/public/


# The Simulator parameters
USSD_URL=${APP_URL}
USSD_PHONE=+000123456789
USSD_NETWORK_MNC=01
USSD_CODE="*000*00#"
USSD_CONSOLE_BEHAVIOR=
```

You do not need to enclose string with double-quotes unless it contains special caracters like: `#`, `!` or `=`. You can notice here we enclose the `USSD_CODE` in double-quotes, because it contains a `#` character, which, without quotes, will be parsed as the begining of a comment.
{: .note .note-warning }

## Testing the application
Rejoice provides a very simple way to test your application. You can test the application either in the console or in your browser.

Rejoice comes with a very handful console command called **`smile`**. It will help us run the simulators.

Open a console at the root of the project and run:

```php
php smile simulator:console
```

This should show in the console:
<div class="phone phone-console">
    <table>
        <tr>
            <td style="color:green;">MENU SCREEN</td>
        </tr>
        <tr>
            <td>It works!</td>
        </tr>
    </table>
</div>

You can also test the application via the web simulator by running the command:

```php
php smile simulator:web
```
This will start a development server at `http://localhost:8001`.
Open your browser at that address and click on the dial button of the test phone on the page.

You should get the test phone screen:
<div class="phone">
    <table>
        <tr>
            <td>It works!</td>
        </tr>
        <tr>
            <td></td>
        </tr>
    </table>
</div>

You can use the shortcuts `php smile sim:con` and `php smile sim:web` to run the simulators.
{: .note .note-info }

Now that we have seen how our applications will look like, let's create our first one.

<div class="d-flex flex-justify-end">
<a href="first-application.html" class="btn">First application</a>
</div>