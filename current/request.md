---
title: Request
layout: default
nav_order: 90
---

<h1>Request</h1>

- [Retrieving the request parameters](#retrieving-the-request-parameters)
  - [Any request POST parameter](#any-request-post-parameter)
  - [Any request GET parameter](#any-request-get-parameter)
  - [The phone number of the user](#the-phone-number-of-the-user)
  - [The network](#the-network)
  - [The session ID](#the-session-id)
  - [The request type (a.k.a service operator code)](#the-request-type-aka-service-operator-code)
  - [The user's response (a.k.a USSD string)](#the-users-response-aka-ussd-string)
  - [The channel](#the-channel)


## Retrieving the request parameters

### Any request POST parameter

```php
$this->request()->input('name_of_the_input');
```

For POST parameters, the request method can be used directly:

```php
$this->request('name_of_the_input');
// Equivalent to
$this->request()->input('name_of_the_input');
```

### Any request GET parameter

```php
$this->request()->query('name_of_the_input');
```

While this two methods are useful, Rejoice has helper methods for some of the parameters:

### The phone number of the user

```php
$this->tel();
// or
$this->msisdn();
```

### The network

```php
$this->network();
```

This returns the [MNC](glossary#mnc) of the user, not the network name.

### The session ID

```php
$this->sessionId();
```

The session ID is retrieved from the mobile operator.

### The request type (a.k.a service operator code)

```php
$this->ussdRequestType();
```

### The user's response (a.k.a USSD string)

```php
$this->userResponse();
```

### The channel

Useful when building an application not only for USSD. You can do extra stuff, customize the response, etc., based on the channel.

```php
$this->channel(); // Will be USSD by default.
```
