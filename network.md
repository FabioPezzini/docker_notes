Docker has defined a very simple networking model, the so-called `container network model (CNM)`, to specify the requirements that any software that implements a container network has to fulfill.
CNM has three elements that can be [viewed](https://prnt.sc/uis6me) in this image.
- Sandbox (or Network namespace): perfectly isolates a container from the outside world. No inbound network connection is allowed into the sandboxed container. But, it is very unlikely that a container will be of any value in a system if absolutely no communication with it is possible. To work around this, we have element number two, which is the endpoint.
- Endpoint: is a controlled gateway from the outside world into the
network's sandbox that shields the container. The endpoint connects the network sandbox (but not the container) to the third element of the model, which is the network.
- Network: is the pathway that transports the data packets of an
instance of communication from endpoint to endpoint or, ultimately, from
container to container.

NB = a network sandbox can have zero to many endpoints.

Docker has always had the `mantra of security` first. This philosophy had a direct influence on how networking in a single and multi-host Docker environment was designed and implemented. Software-defined networks are easy and cheap to create, yet they perfectly firewall containers that are attached to this network from other non-attached containers, and from the outside world. All containers that belong to the same network can freely
communicate with each other, while others have no means to do so (don't attach them to the network).

## Bridge network
When the Docker daemon runs for the first time, it creates a Linux bridge and calls it `docker0`. This is the default behavior and can be changed by changing the configuration. Docker then creates a network with this Linux bridge and calls the network bridge. All the containers that we create on a Docker host and that we do not explicitly bind to another
network leads to Docker automatically attaching to this bridge network.
`IPAM` is apiece of software that is used to track IP addresses that are used on a computer. Theimportant part of the IPAM block is the `Config` node with its values for `Subnet` and `Gateway`.
A typcal bridge configuration can be seen in the [picture](https://prnt.sc/uj0e8h). We can see the network namespace of the host, which includes
the host's `eth0` endpoint, which is typically a NIC if the Docker host runs on bare metal or a virtual NIC if the Docker host is a VM. All traffic to the host comes through eth0. The Linux bridge is responsible for routing the network traffic between the host's network and
the subnet of the bridge network.

NB = By default, only egress traffic is allowed, and all ingress is blocked. What this means is that while containerized applications can reach the internet, they cannot be reached by any outside traffic. Each container attached to the network gets its own virtual ethernet (veth)
connection with the bridge.

NB2 = For security reasons, it is strongly recommended that you do not run any such container attached to the host network on a production or production-like environment.

### List Docker networks
```sh
docker network ls
```
### Create custom bridge network
```sh
docker network create --driver bridge sample-net
```
If we want to define the subnet we can do
```sh
docker network create --driver bridge --subnet "10.1.0.0/16" sample-net
```
### Remove custom bridge network
```sh
docker network rm sample-net
```
### Remove unused network
```
docker network prune --force
```
### Check settings of bridge 
```sh
docker network inspect bridge
```
### Attach a container to an existing container namespace
```sh
docker container run -it --rm --network container:ContainerName alpine:latest /bin/sh
```

## Managing container ports
What if we want make a container accessible by the outside world?! We can expose a port mapping it to an available port on the host like in the [picture](https://prnt.sc/uj0rqd)

### Map a container port to a host port
We can let Docker decide (map the port 80 of the container with one random on the host): 
```sh
docker container run --name web -P -d nginx:alpine
```
And then we can find the choosen port (or list all the informtion about the container)with:
```sh
docker container port web
```
Or we can decide the port:
```sh
docker container run --name web2 -p 8080:80 -d nginx:alpine
```
