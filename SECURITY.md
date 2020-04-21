# Security

Discuss some security aspects when deploying and running containers.

# Container's user role

By default, containers will run as root.

```
hqt@localhost:~$ whoami
hqt

# 
hqt@localhost:~$ docker run -it alpine sh
$

$ whoami
root

# start a 'sleep' command
$ sleep 1000
```

On a different terminal, we check the information of the process that run the 'sleep' command:

```console
# sleep command is actually run by root
hqt@localhost:~$ ps -fC sleep
UID        PID  PPID  C STIME TTY          TIME CMD
root      5957  5907  0 21:09 pts/0    00:00:00 sleep 1000
```

By this example, we see that:
- Containers run as root, even when initiated by non-root user, which is a form of privilege escalation.
- If an attacker can escape a container which is run as root, they will have the full root access to the host.

## Solutions
There are many solutions to avoid running a container as root.

### Solution 1: Using a non-root user for the container
- Specifying a user id and/or group id for the container to avoid the running container as root.
- Using the `--user [user_id]:[group_id]` flag when running docker images:

```console
hqt@localhost:~$ docker run -it --user 5000:1000 -v /etc/passwd:/etc/passwd:ro -v /etc/group:/etc/group:ro ubuntu bash
groups: cannot find name for group ID 1000
I have no name!@b03998b4428d:/$ 

I have no name!@b03998b4428d:/$ whoami
whoami: cannot find name for user ID 5000

$ sleep 1000
```

On the different console:
```console
# The process which executed the sleep command has UID 5000
hqt@localhost:~$ ps -fC sleep
UID        PID  PPID  C STIME TTY          TIME CMD
5000      7094  7038  0 03:59 pts/0    00:00:00 sleep 1000
```

- We might get user id and group id from the environment:
```console
hqt@localhost:~$ docker run -it --user $(id -u):$(id -g) ubuntu bash
groups: cannot find name for group ID 1000
I have no name!@88af256ca8d1:/$ 

# we might mount /etc/passwd and /etc/group to container for the group and user available
hqt@localhost:~$ docker run -it --user $(id -u):$(id -g) \
    -v /etc/passwd:/etc/passwd:ro -v /etc/group:/etc/group:ro \
    ubuntu bash
hqt@b09d2761ac74:/$ 
```

- Using `USER` instruction when creating Dockerfile.
- Prioritize Docker images that run as normal, unprivileged users. [[Docker nginx unprivileged]](https://github.com/nginxinc/docker-nginx-unprivileged) [[Bitnami non-root registry]](https://bitnami.com/stacks/containers)

**Disadvantages**
- Cannot bind on ports < 1024 (Permission: `CAP_NET_BIND_SERVICE`)
- Cannot install packages when running
- Might fail when writing on the mounted volumes

**Reference**
- [Post-installation steps for Linux](https://docs.docker.com/engine/install/linux-postinstall/)
- [Running a docker container as a non root user](https://medium.com/redbubble/running-a-docker-container-as-a-non-root-user-7d2e00f8ee15)
- [Why non root containers are important for security](https://engineering.bitnami.com/articles/why-non-root-containers-are-important-for-security.html)

### Solution 2: Run containers in different user namespace.

### Solution 3: Rootless container
- The Docker daemon runs as the root user which is exposed surfaces to some types of attack. [Docker daemon attack surface](https://docs.docker.com/engine/security/security/#docker-daemon-attack-surface)
- Rootless containers make use of the **user namespace**, which allows a normal non-root user ID on the host can be mapped to root inside the container.
- Libraries / implementations: [[Podman]](https://podman.io/) [[runc]](http://runc.io/) [[Rootless Docker]](https://medium.com/@tonistiigi/experimenting-with-rootless-docker-416c9ad8c0d6) [[Kubernetes rootless]](https://github.com/rootless-containers/usernetes)
- Limitations: [shortcomings of rootless containers](https://opensource.com/article/19/5/shortcomings-rootless-containers)
