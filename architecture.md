Containers are specially encapsulated and secured processes running on the host system.
Containers leverage a lot of features and primitives available in the Linux OS. The most
important ones are namespaces and cgroups. All processes running in containers only share
the same Linux kernel of the underlying host operating system. This is fundamentally
different compared with VMs, as each VM contains its own full-blown operating system.
The Docker architecture is composed by three element:
1. Linux OS (cgroups, namespaces, layer capabilities, other OS functionality)
2. Intermediate layer (containerd e runc)
3. Docker engine (RESTful interface)

## Namespaces
A namespace is an abstraction of global resources such as filesystems,
network access, and process trees (also named PID namespaces) or the system group IDs
and user IDs. A Linux system is initialized with a single instance of each namespace type.
If we wrap a running process, say, in a filesystem namespace, then this process has the
illusion that it owns its own complete filesystem(even if is virtual).
From the perspective of the host, the contained process gets a shielded
subsection of the overall filesystem.
The PID namespace is what keeps processes in one container from seeing or interacting
with processes in another container. A process might have the apparent PID 1 inside a
container, but if we examine it from the host system, it would have an ordinary PID,
say 334.

## Cgroups
Linux cgroups are used to limit, manage, and isolate resource usage of collections of
processes running on a system. Resources are CPU time, system memory, network
bandwidth, or combinations of these resources, and so on.

## Unionfs
Unionfs is mainly used on Linux and allows files
and directories of distinct filesystems to be overlaid to form a single coherent filesystem.
Contents of directories that have
the same path within the merged branches will be seen together in a single merged
directory, within the new virtual filesystem.

## runC
runC is a lightweight, portable container runtime. It provides full support for Linux
namespaces as well as native support for all security features available on Linux, such as
SELinux, AppArmor, cgroups...

## Containerd
runC is a low-level implementation of a container runtime; containerd builds on top of it
and adds higher-level features, such as image transfer and storage, container execution, and
supervision as well as network and storage attachments.