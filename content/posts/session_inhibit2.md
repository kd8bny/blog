+++
title = "Errata systemd-inhibit sessions"
date = "2025-05-14T21:05:13-05:00"
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = "daryl"
cover = ""
tags = ["linux", "systemd", "bash", "ssh", "inhibit", "errata"]
keywords = ["linux", "systemd", "bash", "ssh", "inhibit"]
description = "Tweaks to keeping sessions alive with systemd-inhibit"
showFullContent = false
readingTime = true
hideComments = false
+++
## What's broke

After using the inhibit solution I posted about [Keeping sessions alive with systemd-inhibit](session_inhibit/index.md) I noticed a couple quirks. If the ssh session exists the inhibit is removed, leaving the system to suspend once again. After thinking about it for a bit this makes sense. Since the login process starts your shell of choice, anything within that session will exit once the ssh session ends. When this happens our background sh process exits too.

*Now we can debate is this a bug or a feature?*

Technically speaking the unblock alias is not required if we leave the configuration as is. However, if you'd prefer the system stay awake regardless of an active connection, until you unblock, the previous defined configuration won't work.

An frankly leaving it alone is boring so let's fix it.

## The fix

In order to our change to work `sh` isn't going to be the correct choice anymore. We have no way of enforcing `sh` stay alive on exit. A common solution to this problem is using `nohup` or No Hangup. This however in my testing doesn't work for with sh as redirecting input and output to the inactive shell wont keep it running.
So I devised a new plan hopefully with the similar compatibility of `sh&`. **The Requirements:** We need a long running process that never exits, can't suck too many resources, and needs to be easy to kill. So lets slap a few things together and see how they work.

```sh
alias block='systemd-inhibit --no-ask-password --what=idle --who="me" --why="cuz I said so" nohup watch --interval 240 head /dev/random &; export INHIBIT_PID=$!'
```

- `nohup` - you know this one
- `watch` - watch - "execute a program periodically, showing output fullscreen"
  - `--interval` - update every 240 sec
- `head` - output the first part of files
- `/dev/random` - system entropy used for keys and other randomness

The concept is to read the first few lines of `/dev/random` every 340 sec. We could do something like `cat /dev/random` but i felt that was uncessary overhead for just keeping a process alive.

*Note:* `/dev/urandom` is perfectly fine too for our use.

Now we also make a minor improvement in our unblock alias. After some consideration, I determined running the commands in a incorrect order or sequence after many sessions could leave you with trying to kill a PID that isn't what you want. So now when unblock is run let's set the PID back to an empty value ensuring we don't kill something we don't expect.

```sh
alias unblock='kill -SIGKILL $INHIBIT_PID; INHIBIT_PID='
```

Boom! toss that in your .*rc file and call it a night!

## What about

So couldn't I just use something like [caffeine](https://github.com/pkage/caffeine) instead of all that bash. Yes, yes you can.

-daryl
