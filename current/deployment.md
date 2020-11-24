---
title: Deployment
layout: default
nav_order: 30
---
<h1>Deployment</h1>

- [Deployment](#deployment)
- [USSD request parameters](#ussd-request-parameters)
- [You don't do USSD like that?](#you-dont-do-ussd-like-that)

## Deployment

The USSD protocol is highly linked to telecommunication environment of each country. Each request passes through a mobile operator before reaching the application. Hence, you (or your organization) must be in partnership with at least one (ideally all) the mobile operator of your country to be able to process a USSD request.

## USSD request parameters

If you are already in a partnership with a mobile operator, they have surely given you some requests paramters you should wait for from them and the ones you should send to them on each request. This can differ from country to country. The default USSD parameters have been tailored to Ghana. But Rejoice lets you quickly change the parameters if they are different from from what your mobile operators provide. This can be changed in the .env file:

```ini
REQUIRED_PARAM_NAME_MENU_STRING=message
REQUIRED_PARAM_NAME_REQUEST_TYPE=ussdServiceOp
REQUIRED_PARAM_NAME_SESSION_ID=sessionID
REQUIRED_PARAM_NAME_USER_RESPONSE=ussdString
REQUIRED_PARAM_NAME_USER_PHONE=msisdn
REQUIRED_PARAM_NAME_USER_NETWORK=network
```

- `REQUIRED_PARAM_NAME_MENU_STRING=message`
The message to display to the user's phone.

- `REQUIRED_PARAM_NAME_REQUEST_TYPE=ussdServiceOp`
This POST parameter holds a code that is exchanged between the mobile operator and the application to know the kind of request that is sent. These are the current supported code (the codes are the integer numbers):
  - `1` means the first request (the user has just dialed the ussd code and reach the application).
  - `2` The application is waiting for a response from the user.
  - `3`
  - `17` The user has sent a response
  - `18` The last request (the phone will no more display an input for the user to send a response.)
  - `30` Application failed
You can also adjust the codes in the `app/config/app.php` if they are different from what is provided by your mobile operators.

- `REQUIRED_PARAM_NAME_SESSION_ID=sessionID`
This POST parameter hold the session ID sent by the mobile operator.

- `REQUIRED_PARAM_NAME_USER_RESPONSE=ussdString`
The response sent by the user will be retrieve in the "message" POST parameter.

- `REQUIRED_PARAM_NAME_USER_PHONE=msisdn`
The user's phone number.

- `REQUIRED_PARAM_NAME_USER_NETWORK=network`
The network MNC of the user's mobile operator.

## You don't do USSD like that?

If the way you do USSD in your country is still completely different from what Rejoice provides, kindly create a pull request or just send an email to princedorcis@gmail.com explaining how you do it at your side. We are ready to integrate it to Rejoice :)