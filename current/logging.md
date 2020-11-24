---
title: Logging
layout: default
nav_order: 95
---

<h1>Logging</h1>

- [Logging](#logging)

## Logging

Anywhere in the menu class, you can log by using the `log` method and specifying the log level as second parameter. By default, the log level is `info`.

```php
$this->log('Balance sent')
```

The log will be stored in the `storage/logs/{date}/{name_of_the_menu_that_is_logging}.log` file.

The available log levels are:

- debug
- info
- notice
- warning
- error
- critical
- alert
- emergency

Rejoice uses the `Notify` package as default log package. It is meant to log very simply. Consider using a more performant log package like `Monolog` if you want extra features.
