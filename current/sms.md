---
title: SMS
layout: default
nav_order: 110
---

<h1>SMS</h1>

- [Sending SMS](#sending-sms)
  - [Simply send SMS](#simply-send-sms)
  - [Send and exit the script](#send-and-exit-the-script)

## Sending SMS

This is only available if your SMS endpoint expect a post request with parameters `message`, `sender`, `recipient`. If it does not, you can implement your custom SMS
{: .note note-info }

### Simply send SMS

```php
$this->sendSms('Your request is been processed');
```

This assumes that you have configured the sms endpoint and the default sender name in the .env file or the app config file.

```java
SMS_ENDPOINT=https://my-sms-api-endpoint
SMS_SENDER_NAME=MYAPP
```

If not, you can directly send SMS like this:

```php
$sms = 'Your request is been processed';
$receiver = $this->tel();
$endpoint = 'https://my-sms-api-endpoint';
$sender = 'MySenderName';

$this->sendSms($sms, $receiver, $sender, $endpoint);
```

By default, the receiver is the current user of the application, which can be retrieved via the `$this->tel()` method. If not passed, it is automatically passed by Rejoice.

### Send and exit the script

```php
$this->sendSmsAndExit('Your request is been processed');
```
