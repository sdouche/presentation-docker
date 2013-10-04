!SLIDE
# LXC

"chroot on steroids”

!SLIDE
# Installation de LXC

```bash
$ sudo aptitude install lxc lxc-templates
```

!SLIDE
# Templates

```bash
$ dpkg -L lxc-templates

/usr/share/lxc/templates/lxc-altlinux
/usr/share/lxc/templates/lxc-oracle
/usr/share/lxc/templates/lxc-sshd
/usr/share/lxc/templates/lxc-debian
/usr/share/lxc/templates/lxc-alpine
/usr/share/lxc/templates/lxc-opensuse
/usr/share/lxc/templates/lxc-fedora
/usr/share/lxc/templates/lxc-archlinux
/usr/share/lxc/templates/lxc-busybox
/usr/share/lxc/templates/lxc-ubuntu
/usr/share/lxc/templates/lxc-ubuntu-cloud
```

!SLIDE
# Verification du noyau

```bash
$ sudo lxc-checkconfig

Kernel configuration not found at /proc/config.gz; searching...
Kernel configuration found at /boot/config-3.8.0-31-generic
--- Namespaces ---
Namespaces: enabled
Utsname namespace: enabled
Ipc namespace: enabled
Pid namespace: enabled
User namespace: missing
Network namespace: enabled
Multiple /dev/pts instances: enabled

--- Control groups ---
Cgroup: enabled
Cgroup clone_children flag: enabled
Cgroup device: enabled
Cgroup sched: enabled
Cgroup cpu account: enabled
Cgroup memory controller: enabled
Cgroup cpuset: enabled

--- Misc ---
Veth pair device: enabled
Macvlan: enabled
Vlan: enabled
File capabilities: enabled
```

!SLIDE
# Exemple de création de VM

```bash
$ sudo lxc-create -n test -t ubuntu
Checking cache download in /var/cache/lxc/raring/rootfs*amd64 ... 
Downloading ubuntu raring minimal ...
I: Retrieving InRelease
I: Failed to retrieve InRelease
I: Retrieving Release
I: Retrieving Release.gpg
...
'ubuntu' template installed
'test' created
```

!SLIDE
# Lancement

```bash
$ sudo lxc-start -name test
```