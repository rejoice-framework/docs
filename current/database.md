---
title: Database
layout: default
nav_order: 70
---

<h1>Connecting to the database</h1>

## Configuring databases
Application database connections in Rejoice are configured in the `config/database` folder.

You can define as many database connections as you want by giving each of them a name. There is a default database configured by default.

This is an example of configuration:
```php
// config/database.php

return [

    'default' => [
        'user'     => env('APP_DEFAULT_DB_USER', ''),
        'password' => env('APP_DEFAULT_DB_PASS', ''),
        'host'     => env('APP_DEFAULT_DB_HOST', ''),
        'port'     => env('APP_DEFAULT_DB_PORT', ''),
        'dbname'   => env('APP_DEFAULT_DB_NAME', ''),
    ],

    'log_db' => [
        'user'     => env('LOG_DB_USER', ''),
        'password' => env('LOG_DB_PASS', ''),
        'host'     => env('LOG_DB_HOST', ''),
        'port'     => env('LOG_DB_PORT', ''),
        'dbname'   => env('LOG_DB_NAME', ''),
    ]

];
```

You will define the environment variables in the `.env` file in the root directory.

<div class="note note-warning">
We do not recommend specifying the database credentials directly in the config files.
</div>

## Using a database connection
Using a database connection in a menu class is as easy as calling the `db` method.
The db method takes one parameter which is the name of the connection you want to use.
You can also call the `db` method without parameter. The `default` connection will be automatically used.
The `db` method returns a `PDO` connection to the database. So you will use it exactly how a PDO connection is used, and using a plain query.
These are some examples:

### Inserting to the database

```php
public function before()
{
    $name = $this->userPreviousResponses('enter_username');

    $statement = $this->db()->prepare("INSERT INTO users (name) VALUES (?)");
    $statement->execute([$name]);
    $statement->closeCursor();

    $logStatement = $this->db('log_db')->prepare("INSERT INTO logs (log_text) VALUES (?)");
    $logStatement->execute(["User {$name} has been created"]);
    $logStatement->closeCursor();
}
```

The first insertion uses the default database connection. The second uses the log database configurations.

### Retrieving from the database

```php
public function retrieveUser()
{
    $id = $this->userPreviousResponses('choose_user');

    $statement = $this->db()->prepare("SELECT * FROM users WHERE id = :user_id");
    $statement->execute(['user_id' => $id]);

    $result = $statement->fetch(\PDO::FETCH_ASSOC);
    $statement->closeCursor();

    return $result;
}

public function message()
{
    $user = $this->retrieveUser();
    $message = $user ? "Hello {$user['name']}" : "User not found"; 
    
    return $message;
}
```
Here we used another similar PDO syntax to prepare the query. Notice how the `user_id` is named in the query and referenced in the `execute` method. This is just to show this syntax. Having only one variable (`user_id`), using the question mark syntax would have been easier. We will prefer the `named` variable syntax when we have more variables.

### Updating the database

```php
public function before()
{
    $name = $this->userPreviousResponses('edit_username');

    $statement = $this->db()->prepare("UPDATE users SET name = ?");
    $statement->execute([$name]);
    $statement->closeCursor();
}
```
### Deleting from the database

```php
public function before()
{
    $name = $this->userPreviousResponses('edit_username');

    $statement = $this->db()->prepare("DELETE * FROM users WHERE name = ?");
    $statement->execute([$name]);
    $statement->closeCursor();
}
```

### Transactions

```php
public function before()
{
    $name = $this->userPreviousResponses('enter_username');

    $this->db()->beginTransaction();

    try {
        $statement = $this->db()->prepare("INSERT INTO users (name) VALUES (?)");
        $statement->execute([$name]);
        $statement->closeCursor();

        $this->db()->commit();
        $this->respond("Your information has been successfully saved.");
    } catch (\Throwable $th) {
        $this->db()->rollBack();       
        $this->respond("An error happened.");
    }
}
```

We are using the `respond` method to send the response to the user. This implies this screen will be the last screen.

If you want to do the same thing on a screen that is not the last screen, you can do:
```php
protected $nameSavedSuccessfuly = false;

public function before()
{
    $name = $this->userPreviousResponses('enter_username');

    $this->db()->beginTransaction();

    try {
        $statement = $this->db()->prepare("INSERT INTO users (name) VALUES (?)");
        $statement->execute([$name]);
        $statement->closeCursor();

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