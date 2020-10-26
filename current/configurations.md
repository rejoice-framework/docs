---
title: Configurations
layout: default
nav_order: 49
---

<h1>Configurations</h1>

- [Defining a configuration setting](#defining-a-configuration-setting)
- [Retrieving a configuration setting](#retrieving-a-configuration-setting)

## Defining a configuration setting

All the configurations are in the files in the `config/` folder. You can also add your custom configurations there. A configuration file must return an array that contains the specific configurations. You can take a look at `config/app.php` to see the typical format the file must follow.

## Retrieving a configuration setting

Anywhere in a menu class, you can access a configuration by calling the `config` method. The `config` method takes the dot-separated value where the first part is the name of the config file and the subsequent the index of the configuration value.

```php
$this->config('app.environment');
$this->config('menu.default_end_message');
```

For multidimentional arrays, use the same format, separating the indexes by a dot.
{: .note .note-info }
