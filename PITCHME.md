---
marp: true
title: Inspirate to Automate
paginate: true
header: |
  ![height:50px](https://media.cdn.teamtailor.com/images/s3/teamtailor-production/logotype-v3/image_uploads/d4bf50a4-1efe-4e1d-8811-fc1ddcd9b866/original.png)
footer: |
style: |
    h1,h2,h3,h4,h5 {
        text-align: center;
    }

---
<!--
_header: ""
_footer: ""
_paginate: skip
-->
![bg](https://media.cdn.teamtailor.com/images/s3/teamtailor-production/logotype-v3/image_uploads/d4bf50a4-1efe-4e1d-8811-fc1ddcd9b866/original.png)

---

## About me

![](https://www.gravatar.com/avatar/5983458d689321f9a3b0d1bd86738694.jpg?s=300)

- Name: Olle Dencker
- Senior Consultant @Goteborg 

---

# Improving container security
## Running your containers rootless with podman

---

## Why do we like running containers with docker?

It's easy!

- Deployment
- Network
- Volumes
- Server-client architecture with Remote management API

---

## Why Docker just works

- Client <=> Server/service architecture
- The service is running as root
- To start a docker container, users either have to use sudo or gets added to the `docker` group.

```bash
erik@erik13:~$ ps aux | grep dockerd
root      625302  0.0  0.1 1752820 46812 ?       Ssl  mai09   1:28 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock
erik      972300  0.0  0.0  20740  2708 pts/2    S+   10:36   0:00 grep --color=auto dockerd
erik@erik13:~$ 
```

---

## Whats the problem with rootfull containers?

As a regular user i can gain access to protected files.

```bash
docker run -it --rm \
    -v /etc/passwd:/files_to_edit/passwd \
    -v /etc/shadow:/files_to_edit/shadow \
    -v /etc/group:/files_to_edit/group \
    alpine bash
```

---
<!--
## Can we run docker rootless?

Yes, but needs modification/configuration.

Limits:
- storage drivers
- overlay network
- and more...

---
-->
## Podman to the rescue

![](https://podman.io/images/podman.svg)

#### <https://podman.io>

---

## Whats the catch with rootless containers?

---

## Network limitations

Can only publish ports above 1024 ( unprivileged ports )

---

## File access

Container does not have access to "all" files ( because your are not running it as root )

---

## Limitation workarounds

Challenge accepted!

---

## Networking

---

## Iptables portforwarding

- "Forward port 80 to localhost:8080"

---

## Run the one container that need access as root

- "its ok to run a container as root, if there is no other alternative."

---

## Reverse proxy/load balancer service

- Install a loadballancer ( haproxy ) on server and route traffic to "correct" port.

---

## File/storage/dev access

Make shure the user 

## But what about docker-compose?

`podman-compose` to the rescue!

- "An implementation of Compose Spec with Podman backend."
- Focus on running podman rootless and with no running deamon

```bash
$ pip3 install podman-compose
$ podman-compose up -d
```

---

## Alternative ways to start up containers?

What about systemd?

```bash
$ loginctl enable-linger erik
$ podman run -d --volume /home/erik/nginx/www:/usr/share/nginx/html:ro --name nginx -p 8080:80 nginx:latest
$ podman generate systemd --new --files --name nginx
$ mkdir -p $HOME/.config/systemd/user
$ cp container-nginx.service $HOME/.config/systemd/user/.
$ systemctl --user enable container-nginx.service
$ systemctl --user start container-nginx.service


```

## Thank you!

---

## References
- <https://podman.io>
- <https://github.com/containers/podman-compose>
- <https://developers.redhat.com/blog/2020/09/25/rootless-containers-with-podman-the-basics>
- <https://docs.docker.com/engine/security/rootless/>
- <https://github.com/rootless-containers/rootlesskit>
- <https://www.redhat.com/sysadmin/podman-run-pods-systemd-services>
- `man podman-generate-systemd`