---
title: Go - Build serverless apps
date: 2022-03-24 10:30:00 +00:00
tags: [golang]
description: Go - Build serverless apps
---

Source: [Build serverless apps with Go](https://docs.microsoft.com/en-us/learn/modules/serverless-go/)

- [Intro](#intro)
- [Custom handlers](#custom-handlers)
- [Concepts](#concepts)
- [Flow](#flow)

# Intro

Serverless architecture is a type of application development that allows you to run logic in the cloud without having to worry about building server infrastructure.

Logic implemented in Azure Functions runs in response to events that happen in the cloud. Events that trigger a function (custom handlers).

# Custom handlers

A custom handler is a web server. The web server receives events from the Functions host. You then have an opportunity to write code in your preferred language to respond to the events.

# Concepts

- Triggers: is an event that begins running a function
- Bindings: helper code that connects your function to another cloud service
- Functions host: controls the application event flow

# Flow

1. An event occurs that matches a trigger (e.g: HTTP request), a request is sent to the Functions host
2. The function host creates a request payload and sends that to the web server (custom handler)
3. The function executes the logic created and the response is send back to the Functions host
4. The Functions host passes the outgoing data to a functions's output binding for processing