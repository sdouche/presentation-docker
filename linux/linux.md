!SLIDE
# Namespace

Service fournit par le noyau pour gérer l'isolation. Les namespaces sont sous forme d'arbre (un parent peut affecter un enfant mais pas l'inverse).

!SLIDE
# PID Namespace

Le PID namespace (Linux 2.6.24) gère l'isolation des ID de process :

* PID 1 init-like par namespace
* chaque namespace a propre numération PID (isolation du hôte)
* le process d'un namespace ne peut envoyer de systemcall sur un autre process d'un autre PID namespace
* gestion d'pseudo-filesystem (ex : /proc) vu par le PID namespace qui le monte
* un process possède 2 ID : un dans le namespace et un en dehors (process vu de l'hôte)

!SLIDE
# Net Namespace

Le Net namespace (Linux 2.6.19-2.6.24) gère l'isolation du réseau. Chacun possèdant :

* ses interfaces
* ses ports
* sa table de routage
* ses règles de firewall (iptables)
* son répertoire /proc/net
* INADDR_ANY (0.0.0.0)

Il est possible de créer des «pair interfaces» (visible de l'intérieur et de l'extérieur).

!SLIDE
# User Namespace

Le User namespace (Linux 2.6.23-3.8) gère l'isolation des utilisateurs et des groupes en :

* séparant les droits selon les différents namespaces
* rendant sûr le partage de namespace à des process sans privilège
 
!SLIDE
# IPC namespace

Le IPC namespace (Linux 2.6.19-2.6.30) gère l'isolation des ressources IPC (semaphores, message queues, et shared memory segments)

Note : Depuis Linux 2.6.30, il gère aussi les files de messages POSIX.

!SLIDE
# Mount namespace

Le Mount namespace (Linux 2.4.19) gère l'isolation des points de montage du système de fichier vu par un groupe de process.

* les points de montage ne sont plus globaux mais spécifique au namespace
* les points de montage peuvent être propagés
* racine propre (chroot)

!SLIDE
# UTS namespace

Le UTS namespace (Linux 2.6.19) gère l'isolation des identifiants de nom et de domaine.

Note : UTS vient de la structure "utsname" passée à l'appel système uname(). UTS voulant dire "UNIX Time-sharing System".

!SLIDE
# Control Group (cgroup)

Service fournit par le noyau pour gérer la limitation de ressource (~ulimit pour un groupe de process).

Très pratique car permet de suivre finement des process (systemd l'utilise) et plus généralement pour limiter vos process. 

!SLIDE
# Pseudo filesysteme

Un cgroup créé des pseudo-fichiers pour manipuler le groupe:

```bash
$ mkdir /sys/fs/cgroup/test
$ echo 123 > /sys/fs/cgroup/test/tasks
$ echo 50000000 > /sys/fs/cgroup/memory.limit_in_bytes
```

!SLIDE
# Cgroup memory

Surveille les pages utilisées pour chaque groupe :

* fichier (lecture/écriture, mmap, swap)
* anonyme (stack, heap, anonymous mmap)
* active / inactive

*Note* : Les pages peuvent être partagées ou individuelles.

!SLIDE
# Cgroup cpu

Surveille l'utilisation de la CPU :

* poids par cgroup
* spécifier des CPUs pour un cgroup

*Note* : On peut mettre des CPUs en réserve.

!SLIDE
# Cgroup block

Surveille l'utilisation des I/O :

* poids par cgroup
* limite sur chaque device

!SLIDE
# Cgroup device

Surveille l'utilisation des devices :

* lecture / écriture
* octets /s ou opérations /s
* permissions lecture/écriture/mknod

!SLIDE
# AUFS

AUFS est un union filesystem :

* Il fusionne des répertoires
* Gère le COW (Copy On Write)

!SLIDE
# AUFS

Avantages :

* fusionner une partie en lecture seule avec une partie en lecture / écriture (gain de temps et d'espace)
* utiliser le méme buffer cache
* upgrader toutes les VMs en même temps
