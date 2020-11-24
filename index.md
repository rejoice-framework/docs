---
title: Introduction
layout: default
nav_order: 1
---

# THE REJOICE FRAMEWORK

## Introduction

Rejoice is a PHP framework for USSD applications. It main goal is to speed up your USSD application development while having at anytime a scalable code.

The Rejoice Framework allows you to create simple to complex USSD applications.

## You said USSD?'

Yes! [USSD](current/glossary#ussd) stands for **Unstructured Supplementary Service Data**. It is a network protocol allowing mobile operators to send information and communicate with phone users.

A USSD application is basically a series of popups (which are called *menus* or *pages* or *screens*) that display or request information from the user via their mobile phones.

## How does it works?

Unlike a normal web application, a USSD request is created when a user dials a *USSD code* on their phone. A USSD code usually starts with a star (`*`) and ends with a hash (`#`), eg: **\*000#**. The request is forward to the mobile operator. The mobile operator then checks if the shortcode is mapped to an internal application or a third-party application. If an application (proudly developed with Rejoice) is mapped to the shortcode, they create a session and forward the request to the application. The interaction user-application happens in a session automatically created when the user firstly dialed the USSD code, which ends when either the user has completed the USSD flow or when the user decides to cancel the session themselves.

## What do I need to know before starting?

You don't need to know anything apart from Object-Oriented with the PHP language. The rest we will guide you step-by-step to make your best application.

## Our Philosophy

You must be shouting for joy while building your application 😀

<div class="d-flex flex-justify-end">
<a href="current/installation.html" class="btn">Getting Started</a>
</div>
