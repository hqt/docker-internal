# Security

Discuss some security aspects when deploying and running containers.

# Container user role

## Containers run as root by default
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

**Solution 1:** Change user id for the image

**Solution 2:** Run containers in different user namespace.

**Solution 3:** Rootless container
- Rootless containers make use of the **user namespace**, which allows a normal non-root user ID on the host can be mapped to root inside the container.
- Libraries / implementations: [[Podman]](https://podman.io/) [[runc]](http://runc.io/) [[Rootless Docker]](https://medium.com/@tonistiigi/experimenting-with-rootless-docker-416c9ad8c0d6) [[Kubernetes rootless]](https://github.com/rootless-containers/usernetes)
- Limitations: [shortcomings of rootless containers](https://opensource.com/article/19/5/shortcomings-rootless-containers)
