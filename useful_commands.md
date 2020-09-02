#### Delete all containers in a host
```sh
docker container rm -f $(docker container ls -a -q)
```
#### Get ID of container by name
```sh
export CONTAINER_ID=$(docker container ls -a | grep ContainerName | awk '{print
$1}')
```
#### Formattig output of Docker command
```sh
docker container ps -a --format "table {{.Names}}\t{{.Image}}\t{{.Status}}"
```
#### Filter output of Docker command
The format is: --filter <key>=<value>.
```sh
docker image ls --filter dangling=false
```
#### Limit resources consumed by a container
```sh
docker container run -it --name stress-test --memory 512M ubuntu:19.04 bin/bash
```
We can test the container using `stress` which try to `malloc()` memory in block of 256MB, so we digit in the container bash.
```sh
stress -m 4
```
#### Read-only filesystem
To protect your applications against malicious hacker attacks, it is often advised to definethe filesystem of the container or part of it as read-only.
```sh
docker container run -d --name billing --read-only ubuntu:19.04
```