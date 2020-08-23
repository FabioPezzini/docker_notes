# Docker
Docker is a Container managing software. Containers or in general Container based virtualization uses the kernel on the host's OS to tun multiple guest istances. Each guest istance is called a container, and each container has its own:
- root fs
- processes
- memory
- devices
- network ports

An example is when you have to run multiple versions of java for different applications, each one using a different virtual machine.
Developers use Docker containers to package their applications, frameworks, and
libraries into them, and then they ship those containers to the testers or operations
engineers. To testers and operations engineers, a container is just a black box. It is a standardized black box, though. All containers, no matter what application runs
inside them, can be treated equally. The engineers know that, if any container runs on their servers, then any other containers should run too.

The advantages of using Containers vs VMs are:
- containers are more lightweight
- no need to install guest OS
- less cpu, ram, storage space required
- more containers per machine than VMs
- greater portability
