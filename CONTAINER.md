# Container

Combine all knowledge to understand how does the container works.

## Building an example container

Step 1: Firstly, we will start download a Linux distro to run as a new environment

```console
$ mkdir alpine
$ cd alpine
$ curl -o alpine.tar.gz http://dl-cdn.alpinelinux.org/alpine/v3.10/releases/x86_64/alpine-minirootfs-3.10.0-x86_64.tar.gz
$ tar xvf alpine.tar.gz
$ rm alpine.tar.gz
$ cd ..
```

Step 2: 
```bash
sudo chroot alpine sh
```

**References**
 - https://github.com/lizrice/containers-from-scratch
 - https://github.com/p8952/bocker
