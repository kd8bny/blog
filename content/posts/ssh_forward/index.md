+++
title = "SSH port forwarding"
date = "2025-05-23T09:59:34-05:00"
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = "daryl"
# cover = "cover.png"
tags = ["linux", "ssh", "networking"]
keywords = ["linux", "ssh", "networking", "port", "forward"]
description = "Great references for SSH Remote or Local port forwarding. Now with Pictures!"
showFullContent = false
readingTime = true
hideComments = false
+++
ssh Is such a powerful tool. One the best techniques it enables, in my opinion, is forwarding remote or local ports. Leveraging this ability allows one to shift access from various parts of a network securely.

## An example eh

Imagine a world where you're running a web application on a remote server where the web application is server locally only.

Maybe it doesnt need to be openly available. Maybe it doesn't need to be accessed often. Maybe limited access best reduces the risk to the security posture. *legacy cough cough*
Using ssh, we can forward this port to our system, accessing it as if it were local. ssh ensures traffic is encrypted and transported through the established tunnel.

```text
|------REMOTE-------| <<<ssh tunnel>>> |------ME-----------|
| [my local app]:80<====================>8000:[local port] |
|-------------------| <<<ssh tunnel>>> |-------------------|
```

TL;DR, the remote application's port `80` is served on the local port `8000`. How neat is that?

## What's the point here

I'm not here to regurgitate what others have better explained but to share one of my favorite references. I can say every time, without exaggeration, I look to use a port forward I always refer to this specific [stackexchange post](https://unix.stackexchange.com/questions/115897/whats-ssh-port-forwarding-and-whats-the-difference-between-ssh-local-and-remot/115906#115906). The author Erik explains the concept best with these exquisite drawings.

### Local port forwarding

> local: -L Specifies that the given port on the local (client) host is to be forwarded to the given host and port on the remote side.
>

{{< figure src="local.png" alt="local forward" position="center" caption="Using local port forward" captionPosition="center" >}}

### Remote port forwarding

> remote: -R Specifies that the given port on the remote (server) host is to be forwarded to the given host and port on the local side.
>

{{< figure src="remote.png" alt="remote forward" position="center" caption="Using remote port forward" captionPosition="center" >}}

props to Erik for these! I couldn't give you enough upvotes on stackexchange, but I could spread the word.

-daryl
