---
title: ideas - docker
date: May 14, 2025
tags: [docker, networking]
---

Bridge networking
----
Only user-defined bridge networks support automatic service discovery. If you need to use service discovery with containers, don’t use the default bridge, create a new one.

Host networking
----
Since there is no network address translation (NAT), running a container on the host network can optimize performance. When you run a container on the host network, its network will not be isolated from the host, and it will not get its own IP; these are the limits of this type of network.
