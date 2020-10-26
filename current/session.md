---
title: Session
layout: default
nav_order: 80
---

<h1>Session</h1>

- [Introduction](#introduction)
- [Session driver](#session-driver)
- [Setting a session variable](#setting-a-session-variable)
- [Getting a session variable](#getting-a-session-variable)
- [Checking a session variable](#checking-a-session-variable)
- [Removing a session variable](#removing-a-session-variable)
- [Handling the last screen timeout](#handling-the-last-screen-timeout)
- [Discarding the session ourself](#discarding-the-session-ourself)
  - [Send the response and continue the script](#send-the-response-and-continue-the-script)
  - [Send the response and terminate the script](#send-the-response-and-terminate-the-script)

## Introduction

USSD application rely heavily on session. Each request of the user is contained in a session. You will then most of the time be saving and retrieving from the session.

You can configure the session in the `config/session.php` file. But typically, not to commit your credentials to the version control, you will do it in the `.env` file and let Rejoice automatically add it to the `session.php` file for you.

## Session driver

Rejoice supports two drivers for the sessions: file and database. They are basically where the session data will be saved.

- For file session, no extra configuration is needed.
- For database session, you will need to provide the database configuratons.

No matter which driver you are using, interacting with the session is exactly the same.

You can configure the session in the `.env` file:

```java
SESSION_DRIVER=file
```

Or provide the session database configurations:

```java
SESSION_DRIVER=database

USSD_SESSION_DB_USER=root
USSD_SESSION_DB_PASS=
USSD_SESSION_DB_HOST=localhost
USSD_SESSION_DB_PORT=3306
USSD_SESSION_DB_NAME=
```

## Setting a session variable

You can easily save a variable in the session by using the `sessionSave` method.

```php
$this->sessionSave('user_firstname', 'prince');
```

You can save arrays in the session.
{: .note .note-info }

## Getting a session variable

You can easily retrieve a variable from the session by using the `sessionGet` method.

```php
$firstname = $this->sessionGet('user_firstname');
```

You can use its shortcut `session` method:

```php
$firstname = $this->session('user_firstname');
```

You can pass a default value (both to `session` and `sessionGet`) that will be returned in case the parameter specify does not exist in the session.

```php
$firstname = $this->session('user_firstname', 'Guest');

// Same as

$firstname = $this->sessionGet('user_firstname', 'Guest');
```

If the value is not found in session and no default value was passed, a `RuntimeException` will be thrown.
{: .note .note-warning }

## Checking a session variable

You can easily check if a variable is in the session by using the `sessionHas` method.

```php
if ($this->sessionHas('user_firstname')) {
    //
}
```

## Removing a session variable

You can easily remove a variable from the session by using the `sessionRemove` method.

```php
$this->sessionRemove('user_firstname');
```

## Handling the last screen timeout

<div class="note note-info">This section applies only to the last screen sent to the user.</div>

By default, the Rejoice send the menu screen to the user's phone based on the configured `message` and `actions` (both in the menus.php ressource file and in the menu class). But sometimes, the last screen does not show up and the user rather gets a `connection`[`MMI`](glossary#mmi) error.

The usual workaraound is to set the `allow_timeout` parameter to `false` in the `config/app.php` file. This will result to the user being able to input a response (even though that response will not have any meaning and will automatically being discarded by Rejoice). This is just to make sure the user can see the last response no matter the session has timed out or not.

<a name="discard-session"></a>

## Discarding the session ourself

This section applies only to the last menu screen sent to the user.
{: .note .note-info }

Rejoice takes care of discarding the session for us on a last screen. But sometimes, it is uselful to discard the session ourself, may it be to send the response to the user and being able to continue a business logic, or we need to send another push request to the user's phone (another USSD-like request passing by the mobile operator), or we need to call an API that will take long to respond or any other reason. Rejoice provides some in-built method to easily do it.

### Send the response and continue the script

We need the `respond` method:

```php
class ProcessBalanceRequest extends Menu
{
    public function before($previousResponses)
    {
      $this->respond('Your request is been processed');

      // Continue the script (insert in database, call an API, etc.)
    }
}
```

Instead of the `respond` method, we can use:

```php
$this->respondAndContinue('Your request is been processed');
```

or

```php
$this->softEnd('Your request is been processed');
```

The three methods (`respond`, `respondAndContinue`, `softEnd`) are aliases. Just choose the one you like.

### Send the response and terminate the script

```php
class ProcessBalanceRequest extends Menu
{
    public function before($previousResponses)
    {
        // Your business logic here

        // Sends the response and exit the script automatically
        $this->terminate('Your request is been processed'); 

        // Anything here will not run
    }
}
```

Instead of the `terminate` method, we can use:

```php
$this->respondAndExit('Your request is been processed');
```

or

```php
$this->hardEnd('Your request is been processed');
```

The three methods (`terminate`, `respondAndExit`, `hardEnd`) are aliases. Just choose the one you like.
