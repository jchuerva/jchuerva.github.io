---
title: Hits to remember when doing a git rebase commands
date: 2020-08-24 17:00:00 +01:00
tags: [git]
description: Hits to remember when doing a git rebase commands
---

# Commands:

### Get last n commits in `oneline`:
```
git log -n 10 --oneline | bat
```

### Rebase
#### Locate your last good commit and use it for rebase:

Example:
```
❯ git log -n 10 --oneline | bat
───────┬───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
       │ STDIN
───────┼───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
   1   │ 90ab7be Merge branch 'add-hydro' into add-hydro-backtrace-new-event
   2   │ af9751f fix MemorySink 😅
   3   │ 53f82c2 code refactor
   4   │ 09160f9 update protobuf gem
   5   │ dec9378 add schema into events
   6   │ 0b34308 add hydro client gem <-- I'm good bellow it
   7   │ e404882 script for getting the hydro client gem
   8   │ 4e0c998 add hydro event in backtrace new
   9   │ e40b04c add hydro schema
  10   │ ad1bdce extract new backtrace hydro event
  ```


#### Rebase the code using :point_up: commit:
```
git rebase -i 0b34308
```

#### Hits during the rebase:
- First commit cannot be a `squash`
- Next: `Shitf` + `Z` + `Z`
