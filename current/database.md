---
title: Database
layout: default
nav_order: 70
---

<h1>Connecting to the database</h1>

## Connections

Application database connections in Rejoice are configured in the `config/database.php` file.

You can define as many database connections as you want by giving each of them a name. There is a default database configured by default.

You will define the environment variables in the `.env` file in the root directory.

<div class="note note-warning">
We do not recommend specifying the database credentials directly in the config files.
</div>

## Creating Models

Rejoice ships with the Eloquent ORM provided by the wonderful `illuminate/database` package which allows to create Models reflecting the tables in our database.

A model can be created using the commands:

```php
php smile make:model Country
```

This command will create a class named `Country` in a newly created `app/Models/Country.php` file.

This model is then automatically mapped to the `countries` table in the database. If the countries table has another name (for example `all_countries`), you can simply specify that name in the class:

```php
<?php

namespace App\Models;

use Rejoice\Database\Model;

class Country extends Model
{
    protected $table = 'all_countries';
}
```

You can then start using like:

```php
public function before()
{
    $name = $this->previousResponses('ChooseCountry');
    $countryCode = Country::where('name', '=', $name)->value('code');
}
```

You can use all the wonderful features that come with the Eloquent ORM, like relationships, collections, mutators, etc. Learn more about Eloquent [here](laravel.com/docs/8.x/eloquent#eloquent-model-conventions).

## Query Builder

Together with the Eloquent ORM, you have access to the query builder simply by calling the `Rejoice/Database/DB` class.

You can then use it like:

```php
public function before()
{
    $name = $this->previousResponses('ChooseCountry');
    $countryCode = DB::table('all_countries')->where('name', '=', $name)->value('code');
}
```

Learn more about Query Builder [here](https://laravel.com/docs/8.x/queries).

## Using directly the PDO connection

Getting the PDO connection is as easy as calling the `db` method.
The db method takes one parameter which is the name of the connection you want to use.
You can also call the `db` method without parameter. The `default` connection will be automatically used.
The `db` method returns a `PDO` connection to the database. So you will use it exactly how a PDO connection is used, and using a plain query.
These are some examples:

## Inserting to the database

### Using Eloquent

Assuming we have created a `User` Model:

```php
<?php

namespace App\Models;

use Rejoice\Database\Model;

class User extends Model
{
}
```

Then in the menu class:

```php
public function before()
{
    $firstName = $this->previousResponses('EnterFirstName');
    $lastName = $this->previousResponses('EnterLastName');

    $user = new User;
    $user->first_name = $firstName;
    $user->last_name = $lastName;
    $user->save();
}
```

We can do the same via mass-assignement by defining the attributes that will be mass-assignable in the `fillable` property of the model:

```php
<?php

namespace App\Models;

use Rejoice\Database\Model;

class User extends Model
{
    protected $fillable = [
        'first_name', 'last_name'
    ];
}
```

Then in our menu:

```php
public function before()
{
    $firstName = $this->previousResponses('EnterFirstName');
    $lastName = $this->previousResponses('EnterLastName');

    $user = User::create([
        'first_name' => $firstName,
        'last_name'  => $lastName,
    ]);
}
```

### Using Query Builder

```php
public function before()
{
    $firstName = $this->previousResponses('EnterFirstName');
    $lastName = $this->previousResponses('EnterLastName');

    DB::table('users')->insert([
        'first_name' => $firstName,
        'last_name' => $lastName,
    ]);
}
```

### Using PDO connection

```php
public function before()
{
    $firstName = $this->previousResponses('EnterFirstName');
    $lastName = $this->previousResponses('EnterLastName');

    $statement = $this->db()->prepare("INSERT INTO users (first_name, last_name) VALUES (:first_name, :last_name)");
    $statement->execute([
        'first_name' => $firstName,
        'last_name' => $lastName,
    ]);
    $statement->closeCursor();
}
```

## Retrieving from the database

### Using Eloquent

```php
public function message()
{
    $id = $this->previousResponses('choose_user');

    $user = User::find($id);

    $message = $user ? "Hello {$user->first_name}" : "User not found";

    return $message;
}
```

### Using Query Builder

```php
public function message()
{
    $id = $this->previousResponses('choose_user');

    $user = DB::table('users')->where('id', '=', $id)->get();

    $message = $user ? "Hello {$user->first_name}" : "User not found";

    return $message;
}
```

### Using PDO connection

```php
public function message()
{
    $id = $this->previousResponses('choose_user');

    $statement = $this->db()->prepare("SELECT * FROM users WHERE id = ?");
    $statement->execute([$id]);

    $user = $statement->fetch(\PDO::FETCH_ASSOC);

    $statement->closeCursor();

    $message = $user ? "Hello {$user['first_name']}" : "User not found";

    return $message;
}
```

## Updating the database

### Using Eloquent

```php
public function before()
{
    $id = $this->previousResponses('choose_user');

    User::where('id', $id)->update(['active' => 0]);
}
```

### Using Query Builder

```php
public function before()
{
    $id = $this->previousResponses('choose_user');

    DB::table('users')->where('id', $id)->update(['active' => 0]);
}
```

### Using PDO connection

```php
public function before()
{
    $id = $this->previousResponses('choose_user');

    $statement = $this->db()->prepare("UPDATE users SET active = 0 WHERE id = ?");
    $statement->execute([$id]);
    $statement->closeCursor();
}
```

## Deleting from the database

### Using Eloquent

```php
public function before()
{
    $id = $this->previousResponses('choose_user');

    User::where('id', $id)->delete();
}
```

### Using Query Builder

```php
public function before()
{
    $id = $this->previousResponses('choose_user');

    DB::table('users')->where('id', $id)->delete();
}
```

### Using PDO connection

```php
public function before()
{
    $id = $this->previousResponses('choose_user');

    $statement = $this->db()->prepare("DELETE * FROM users WHERE id = ?");
    $statement->execute([$id]);
    $statement->closeCursor();
}
```

## Transactions

### Using the `transaction` method

The `DB` class provides a handful `transaction` method to manage transactions.

```php
public function before()
{
    DB::transaction(function () {
        // First query
        // Second query
        // ...
    });
}
```

Committing or rolling back are automatically handled by the `transaction` method.

The `transaction` method accepts a second argument that is the number of times the transaction will be retried if it failed.

```php
public function before()
{
    $retry = 3;

    DB::transaction(function () {
        // First query
        // Second query
        // ...
    }, $retry);
}
```

An exception will be thrown if all the attempts are exhausted.

### Manually handling committing and rolling back

If for any reason you want to manually handle committing and rolling back, you can easily do it:

```php
public function before()
{
    $this->db()->beginTransaction();

    try {
        // First query
        // Second query
        // ...

        $this->db()->commit();

        $this->respond("Operation successful.");
    } catch (\Throwable $th) {
        $this->db()->rollBack();

        $this->respond("An error happened.");
    }
}
```

<div class="note note-info">
Instead of `$this->db()->beginTransaction()`, you can also use `DB::beginTransaction()`. Same for `$this->db()->commit()` and `$this->db()->rollBack()`.
</div>

We are using the `respond` method to send the response to the user. This implies this screen will be the last screen.

If you want to do the same thing on a screen that is not the last screen, you can do:

```php
protected $nameSavedSuccessfuly = false;

public function before()
{
    $this->db()->beginTransaction();

    try {
        // First query
        // Second query
        // ...

        $this->db()->commit();
        $this->nameSavedSuccessfuly = true;
    } catch (\Throwable $th) {
        $this->db()->rollBack();
    }
}

public function message()
{
    return $this->nameSavedSuccessfuly ? 
        "Your information has been successfully saved." :
        "An error happened.";
}

public function actions()
{
    $actions = [];

    if ($this->nameSavedSuccessfuly) {
        $actions = [
            '1' => [
                'display' => 'Profile',
                'next_menu' => 'edit_profile'
            ],
            '2' => [
                'display' => 'Return to welcome menu',
                'next_menu' => '__welcome'
            ]
        ];
    } else {
        $actions = [
            '1' => [
                'display' => 'Try again',
                'next_menu' => '__same'
            ]
        ];
    }

    return $actions;
}
```

<div class="note note-warning">
While this works fine, we recommend to interact with the database on the last menu as possible as it can be. Try to use the <a href="session.html">session</a> to store as much as possible and save values to the database only on the last menu.
</div>