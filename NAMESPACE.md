# Namespace
- Namespaces are a feature of the Linux kernel that partitions kernel resources such that one set of processes sees one set of resources while another set of processes sees a different set of resources.
- There are many types of namespace: 
    - Mount (mnt)
    - Process ID (pid)
    - Network (net)
    - Interprocess Communication (ipc)
    - UNIX Timesharing System (uts)
    - User ID (user)
    - Control group (cgroup) : available from March 2016 in Linux 4.6.
    - Time: available from March 2020 in Linux 5.6. [[ref]](https://lwn.net/Articles/779104/)
- Some namespaces can be nested.
- List all namespaces in the system with `lsns`:
```console
$ lsns
        NS TYPE   NPROCS   PID USER COMMAND
4026531835 cgroup      3  5205 hqt  /lib/systemd/systemd --user
4026531836 pid         3  5205 hqt  /lib/systemd/systemd --user
4026531837 user        3  5205 hqt  /lib/systemd/systemd --user
4026531838 uts         3  5205 hqt  /lib/systemd/systemd --user
4026531839 ipc         3  5205 hqt  /lib/systemd/systemd --user
4026531840 mnt         3  5205 hqt  /lib/systemd/systemd --user
4026531993 net         3  5205 hqt  /lib/systemd/systemd --user
```
- Illustration of a namespace:
# <img src="images/namespace_process.png" height="256"/>


# Examples
**unshare** : run program with some namespaces unshared from parent

Examples will go through the `unshare` command to show how does the `unshare` can be used to isolate namespaces from the parent.

### Isolate the Hostname:
Allow a single system to appear to have different host and domain names to different processes.

Running `unshare` command with the `--uts` flag.

```console
# run the "sh" program with the uts namespace is isolated
hqt@localhost:~$ sudo unshare --uts sh
$

# hostname will be inherited from the parent shell
$ hostname
localhost

# change the host name
$ hostname github
github

# exit the "sh" program
$ exit

# run again the hostname command. original hostname does not change
hqt@localhost:~$ hostname
localhost
```

