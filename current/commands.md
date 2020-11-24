---
title: Commands
layout: default
nav_order: 140
---
<h1>Commands</h1>

- [Creating a command](#creating-a-command)
- [Configuring the command](#configuring-the-command)
  - [Adding arguments](#adding-arguments)
  - [Adding options](#adding-options)
- [The fire method](#the-fire-method)
- [Writing](#writing)
- [Display in color](#display-in-color)
  - [Supported colors](#supported-colors)
  - [Get a colorized string](#get-a-colorized-string)
- [Colors helper methods](#colors-helper-methods)
  - [Display a green text:](#display-a-green-text)
  - [Error text](#error-text)
  - [Question](#question)
- [Tables](#tables)
- [Create a table](#create-a-table)
  - [Table head](#table-head)
  - [Separation line](#separation-line)
  - [Table header and footer titles](#table-header-and-footer-titles)
  - [Control the border style](#control-the-border-style)
- [Ask question](#ask-question)
- [Ask for confirmation](#ask-for-confirmation)
- [The input object](#the-input-object)
- [The output object](#the-output-object)
- [Going further](#going-further)

## Creating a command

```php
php smile make:command MyCommand
```

Will create a new command in the `app/Console/Commands`{:.php} folder.

## Configuring the command

It helps you set the name of the command, the help message associated, the arguments the command takes, the options the command takes.

```php
public function configure()
{
    $this->setName('create:file')
        ->setDescription('Create a new file')
        ->setHelp('Create a new file in the root project directory. You must have write-right on the root folder.');
}
```

We initialise our command with a name, a description and a help message.

The name of the command has a colon in-between. This is not compolsory. But using the colon will make the command belong to a namespace. The namespace is the left part of the colon. So here `create` will be the namespace and it will be automatically created. Then later you can create other command under the same namespace.
{: .note .note-info }

### Adding arguments

We can add argument by using the `addArgument` method.

```php
public function configure()
{
    $this->setName('create:file')
        ->setDescription('Create a new file')
        ->setHelp('Create a new file in ...')
        ->addArgument(
            'name',
            Argument::REQUIRED,
            'The name or the of the file'
        );
}
```

The `addArgument` method takes three parameters:

- The name of the argument,
- A constant specifying if the argument is required or not:
  - `Argument::OPTIONAL`,
  - `Argument::REQUIRED`
- and a description of the variable that will be display aside the argument in the help message.

You need to import the class:

```php
use Rejoice\Console\Argument;
```

You can add as many arguments as you want.

### Adding options

```php
public function configure()
{
    $this->setName('create:file')
        ->setDescription('Create a new file')
        ->setHelp('Create a new file in the root project directory. You must have write-right on the root folder.')
        ->addArgument(
            'name',
            Argument::REQUIRED,
            'The name or the of the file'
        )
        ->addOption(
            'fill',
            'f',
            Option::OPTIONAL,
            'Use this option to specify a default text to fill the new file.',
            'This is a default message'
        );
}
```

The `addOption` method takes four parameters.

- The name of the option
- A one-letter shortcut for the option
- A constant specifying if the option is required or not. The possible values are:
  - `Option::OPTIONAL`
  - `Option::REQUIRED`
- And the default value of the option.

You need to import the class:

```php
use Rejoice\Console\Option;
```

You can add as many options as you want.

## The fire method

```php
public function fire()
{
    // The code of your command here

    return SmileCommand::SUCCESS;
}
```

The `fire`{:.php} method must return `SmileCommand::SUCCESS`{:.php} if the command performs everything ok, and `SmileCommand::FAILURE`{:.php} in case an error happens.

## Writing

```php
$this->write('Cool');
```

```php
// end with a new line
$this->writeln('Cool');
```

The variable in writeln can be an array of string. Each string will be displayed on a new line.

```php
$this->writeln(['Hi Prince!', 'This is amazing.']);
```

## Display in color

```php
$this->writeWithColor('Cool', 'red', 'cyan');
```

The first color is the foreground color. The second, the background color. Both are optional. The default is white for the foreground, black for the background.

### Supported colors

- <span class="color-box white"></span> white
- <span class="color-box black"></span> black
- <span class="color-box red"></span> red
- <span class="color-box green"></span> green
- <span class="color-box yellow"></span> yellow
- <span class="color-box blue"></span> blue
- <span class="color-box magenta"></span> magenta
- <span class="color-box cyan"></span> cyan

### Get a colorized string

If you don't want to write directly but rather want to get the colorized string in a variable, you can use the `colorize` method.

```php
$greetings = $this->colorize('Cool', 'red', 'cyan');
```

## Colors helper methods

### Display a green text:

```php
$this->info('Cool');
$this->success('Cool');

// Equivalent to
$this->writeWithColor('Cool', 'green', 'black');
```

### Error text

```php
$this->error('Uhhh');

// Equivalent to
$this->writeWithColor('Uhhh', 'white', 'red');
```

### Question

```php
$this->question('Why?');

// Equivalent to
$this->writeWithColor('Why?', 'black', 'cyan');
```

## Tables

## Create a table

```php
$table = $this->table()
    ->body([
        [ 1, 'Edna',   22 ],
        [ 2, 'Prince', 25 ],
        [ 3, 'Edwige', 24 ],
    ])
    ->show(); // Display the table
```

Or define the rows one by one:

```php
$table = $this->table()
    ->addRow([ 1, 'Edna',   22 ])
    ->addRow([ 2, 'Prince', 25 ])
    ->addRow([ 3, 'Edwige', 24 ])
    ->show();
```

### Table head

You can add a table head:

```php
$table = $this->table()
    ->head(['ID', 'Name', 'Age'])
    ->body([
        [ 1, 'Edna',   22 ],
        [ 2, 'Prince', 25 ],
        [ 3, 'Edwige', 24 ],
    ])
    ->show();
```

### Separation line

By default the rows are not separated by a line. To show a separtion line use the `tableLine` method.

```php
$table = $this->table()
    ->head(['ID', 'Name', 'Age'])
    ->body([
        [ 1, 'Edna',   22 ],
        [ 2, 'Prince', 25 ],
        $this->tableLine()
        [ 3, 'Edwige', 24 ],
    ])
    ->show();
```

### Table header and footer titles

```php
$table->headTitle('Users')
$table->footTitle('Page 1/2')
$table->show();
```

### Control the border style

```php
// Default
$table->border('default')

// No border
$table->border('none')

// Only top, bottom and header lines
$table->border('row')

// All borders
$table->border('all')

// All borders with double-line
$table->border('all-double')

$table->show();
```

## Ask question

```php
$filename = $this->ask('What is the file name?');
```

The `ask` method returns the response given by the user.

## Ask for confirmation

```php
if ($this->confirm('Do you want to overwrite this file?', 'no');) {
    //
}
```

Takes as first argument, the question and as second argument the default response.
The default response must be one of: `y`, `yes`, `n`, `no`.

## The input object

The input of the user is represented by an object that Smile uses under the hood to retrieve the arguments and options provided by the user. For advanced manipulation of the command, you may need to use this object. You can retrieve it by:

```php
$input = $this->getInput();
```

## The output object

The output object controls what the command will display and how it will be displayed. It is the object use under the hood when calling a method like `writeln`. For advanced manipulation of the command, you may need to use this object. You can retrieve it by:

```php
$output = $this->getOutput();
```

## Going further

You customize more your command by visiting the awesome Symfony Console documentation. Smile is fully based on Symfony Console.
