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
root@srv1:/sys/fs/cgroup# ls *1
blkio
cpu
cpuacct
cpuset
devices
freezer
hugetlb
memory
perf_event
```

Un process se place dans le groupe de son parent. 

!SLIDE
# Limitation des ressources

On peut limiter beaucoup de choses :

* la mémoire
* CPU
* Block I/O
* ...

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
