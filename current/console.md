---
title: Console
layout: default
nav_order: 130
---

<h1>Console commands</h1>

- [Console commands](#console-commands)
- [Devtool](#devtool)
- [Running the simulator](#running-the-simulator)
  - [Web simulator](#web-simulator)
  - [Console simulator](#console-simulator)
- [Create a new menu](#create-a-new-menu)
  - [Directly in the Menus folder](#directly-in-the-menus-folder)
  - [In a Menu sub-folder](#in-a-menu-sub-folder)
- [Other console commands](#other-console-commands)
- [Help on a command](#help-on-a-command)

## Console commands

Rejoice ships with useful console features to allow you test quickly your application and other more.

The console application in Rejoice is name `smile`.

## Devtool

Rejoice provides a very handful devtool to interact with your application. You can access the devtool by running in a console (opened in the project root):

```php
php smile devtool
```

You can run any valid PHP code inside the devtool and also use the classes of your application.

## Running the simulator

You have access to two simulators, one in console, the other as a web interface.
You can provide the configuration of the simulator in the .env file:

### Web simulator

Run the console simulator by running the command

```php
php smile serve
```

or

```php
php smile simulator:web
```

### Console simulator

Run the console simulator by running the command

```php
php smile serve -c console
```

or

```php
php smile simulator:console
```

You can use the shortcut `php smile sim:con`
> Note: You can use shortcuts for any simulator command, provided it does not conflict with any other command.

## Create a new menu

### Directly in the Menus folder

```php
php smile make:menu MyMenu
```

Will create the class `MyMenu` in a newly created `app/Menus/MyMenu.php`.

### In a Menu sub-folder

```php
php smile make:menu FirstFlow/MyMenu
```

Will create the class `MyMenu` in a newly created `app/Menus/FirstFlow/MyMenu.php`.
If the sub-folder does not exist, it will be created.

## Other console commands

You can retrieve any other console command by running:

```php
php smile list
```

## Help on a command

To read the help of a command, run:

```php
php smile help name_of_the_command
```
