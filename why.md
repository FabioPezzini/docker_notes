In the old days, developers would develop a new application. Once that application
was completed in their eyes, they would hand that application over to the operations
engineers, who were then supposed to install it on the production servers and get it
running. If the operations engineers were lucky, they even got a somewhat
accurate document with installation instructions from the developers. So far, so good, and
life was easy.
But things get a bit out of hand when, in an enterprise, there are many teams of
developers that create quite different types of application, yet all of them need to be
installed on the same production servers and kept running there. Usually, each application
has some external dependencies, such as which framework it was built on, what libraries it
uses, and so on. Sometimes, two applications use the same framework but in different
versions that might or might not be compatible with each other. Our operations engineer's
life became much harder over time. They had to be really creative with how they could load
their ship, (their servers,) with different applications without breaking something.

## First approach
One of the first approaches was to use virtual machines (VMs). Instead of running 
multiple applications, all on the same server, companies would package and run a single
application on each VM. With this, all the compatibility problems were gone and life
seemed to be good again. Unfortunately, that happiness didn't last long. VMs are pretty
heavy beasts on their own since they all contain a full-blown operating system such as
Linux or Windows Server, and all that for just a single application.

## The solution
The ultimate solution to this problem was to provide something that is much more
lightweight than VMs, but is also able to perfectly encapsulate the goods it needs to
transport. Here, the goods are the actual application that has been written by our
developers, plus – and this is important – all the external dependencies of the application,
such as its framework, libraries, configurations, and more. This holy grail of a software
packaging mechanism was the Docker container.
### Security
Gartner found that applications running in a
container are more secure than their counterparts not running in a container. Containers
use Linux security primitives such as Linux kernel namespaces to sandbox different
applications running on the same computers and control groups (cgroups) in order to
avoid the noisy-neighbor problem, where one bad application is using all the available
resources of a server and starving all other applications.
Also because docker images are immutable is easy to scan for CVE and find security issues
### Simulate Production Environment
If we can containerize any application, then we can also
containerize, say, a database such as Oracle or MS SQL Server. Now, everyone who has
ever had to install an Oracle database on a computer knows that this is not the easiest thing
to do, and it takes up a lot of precious space on your computer. You wouldn't want to do
that to your development laptop just to test whether the application you developed really
works end-to-end. With containers at hand, we can run a full-blown relational database in
a container as easily as saying 1, 2, 3.
#### Citation
Somebody once said that, today, every company of a certain size has to acknowledge
that they need to be a software company. In this sense, a modern bank is a software
company that happens to specialize in the business of finance. Software runs all businesses.
### Architecture
There are three essential parts:
- On the bottom, we have the Linux operating system
- In the middle, we have the container runtime
- On the top, we have the Docker engine.

Containers are only possible due to the fact that the Linux OS provides some
primitives, such as namespaces, control groups, layer capabilities, and more, all of which
are leveraged in a very specific way by the container runtime and the Docker engine. Linux
kernel namespaces, such as process ID (pid) namespaces or network (net) namespaces,
allow Docker to encapsulate or sandbox processes that run inside the container. Control
Groups make sure that containers cannot suffer from the noisy-neighbor syndrome, where
a single application running in a container can consume most or all of the available
resources of the whole Docker host. Control Groups allow Docker to limit the resources,
such as CPU time or the amount of RAM, that each container is allocated.
The container runtime on a Docker host consists of containerd and runc. runc is the low-level functionality of the container runtime, while containerd, which is based on
runc, provides higher-level functionality. Both are open source and have been donated
by Docker to the CNCF.
The container runtime is responsible for the whole life cycle of a container. It pulls a container image (which is the template for a container) from a registry if necessary, creates a container from that image, initializes and runs the container, and eventually stops and removes the container from the system when asked.
The Docker engine provides additional functionality on top of the container runtime,
such as network libraries or support for plugins. It also provides a REST interface over
which all container operations can be automated. The Docker command-line interface that
we will use frequently in this book is one of the consumers of this REST interface.
