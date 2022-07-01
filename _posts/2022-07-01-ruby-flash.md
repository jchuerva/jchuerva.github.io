---
title: Rails flash
date: 2022-07-01 08:00:00 +01:00
tags: [rails]
description: Rails flash
---

- [Flash vs Flash.now](#flash-vs-flashnow)
  - [Flash](#flash)
  - [Flash.now](#flashnow)
- [Flash.clear](#flashclear)
- [References](#references)

# Flash vs Flash.now

## Flash
- flash persist across redirects
- `flash`: redirection, for the next request
- Flash[:notice]’s message will persist to the next action and should be used when redirecting to another action via the ‘redirect_to’ method.



## Flash.now

- flash.now doesn't persist across redirects
- `flash.now`: render template only, for the same request
- Flash.now[:notice]’s message will be displayed in the view your are rendering via the ‘render’ method.

# Flash.clear
In rails we have the method `flash.clear` to clear all flash messages.


# References

- [https://spartchou.gitbooks.io](https://spartchou.gitbooks.io/ruby-on-rails-basic/content/syntax/flash_vs_flashnow.html)
- [http://ionrails.com](http://ionrails.com/2009/09/19/flashnotice-vs-flash-nownotice/):

