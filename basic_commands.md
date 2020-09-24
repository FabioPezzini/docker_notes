## Check Docker installation
```sh
docker version
```

## Run Container
Notice that docker run = docker create + docker start, hence any time we do a docker run, a new container is created.
In order to have persistance among states, we should launch docker with start and attach image, then re-run the command. Docker will create (i.e., pull) the image if it doesn't exist.
```sh
docker container run ContainerImage
```
The general syntax to manage the containers is:
```sh
docker container run [options] [image] [command] [args]
```

## Run commands inside container
This will execute the command inside the container
```sh
docker container run ContainerImage ping 127.0.0.1
```
### -d parameter (daemon)
Tells Docker to run the process running in the container as a Linux daemon, like a background service.
```sh
docker container run -d ContainerImage
```
### --name parameter (assign a name)
Can be used to give the container an explicit name, if we don't specify any name Docker will assign a random unique name.
```sh
docker container run --name Sample ContainerImage
```
### -p parameter (mapping port)
To map a port of the host with a port of the container we can use the parameter p.
```sh
docker container run -p 8080:80 ContainerName
```
### --entrypoint parameter
This flag override the existing entrypoint set in an image
```sh
docker container run -it --entrypoint /bin/sh ContainerName
```
### -it parameter
To run and maintain up a container we can do:
-i flag signifies that we want to run the additional process interactively. 
-t tells Docker that we want it to provide us with a TTY.
```sh
docker container run -it -d ubuntu
```

## Check status of containers
Check only the status of the running containers
```sh
docker container ls
```
### -a parameter (all containers)
Check the status of all containers in the host.
```sh
docker container ls -a
```
### -q parameter (list IDs)
List the IDs of all containers.
```sh
docker container ls -q
```
Output  fields of the command:
- Container ID : Unique ID of the container in SHA-256.
- Image : Name of the container image from which this container is
instantiated.
- Command : Command that is used to run the main process in the
container.
- Created : Date and time when the container was created.
- Status : Status of the container (created, restarting, running, removing,
paused, exited, or dead).
- Ports : List of container ports that have been mapped to the host.
- Names : Name assigned to this container (multiple names are possible).

## Stop containers
Docker sends a Linux SIGTERM signal to the main process running inside the container. If the process doesn't react to this signal and terminate itself, Docker waits for 10 seconds and then sends SIGKILL, which will kill the process forcefully and terminate the container. We can stop container by name or by ID.
```sh
docker container stop ContainerName
```

## Start containers
If a container is stopped, it can be started using the following command.
```sh
docker container start ContainerName
```

## Inspect containers
Containers are runtime instances of an image and have a lot of associated data that
characterizes their behavior. To get more information about a specific container (IP etc) is possible to inspect it, then we can use `grep` for filter for a specific detail using linux `pipeline`. The response is a big JSON object
```sh
docker container inspect ContainerName
```
### -f parameter (filter)
it is used to define the filter. The filter expression itself uses
the Go template syntax, the example show the content of the State value.
```sh
docker container inspect -f "{{json .State}}" ContainerName | jq .
```

## Exec in running container
Sometimes, we want to run another process inside an already-running container. A typical reason could be to try to debug a misbehaving container. For example if we want to run a shell inside a running container we can do:
```sh
docker container exec -i -t ContainerName /bin/sh
```
 -i flag signifies that we want to run the additional process interactively. 

 -t tells Docker that we want it to provide us with a TTY.
 
 If we want run a process inside a container without accessing it (It will redirect the output to the stdout):
 ```sh
 docker container exec ContainerName ps
```

## Attach a running container
We can use the attach command to attach our terminal's standard input, output, and
error to a running container.
```sh
docker container attach ContainerName
```
To quit the container without stopping or killing it, we can press the key
combination Ctrl + P+ Ctrl + Q. This detaches us from the container while leaving it running
in the background. On the other hand, if we want to detach and stop the container at the
same time, we can just press Ctrl + C.

## Delete container
It is possible to remove container by the name or by ID
```sh
docker container rm ContainerName
```
### -f parameter (force remove)
Sometimes removing a container will not work as it is still running, so we can force it.
```sh
docker container rm -f ContainerName
```

## Retrieve container logs
Docker includes multiple logging mechanisms,the default logging driver is `json-file`, we can also use `journald`,`syslog`,`gelf`,`fluentd`.
Only json-file and journald can use the logs command.

To access the logs of a given container, we can use:
```sh
docker container logs ContainerName
```
### -t parameter (tail)
To limit the number of logs to retrieve (latest)we can do
```sh
docker container logs -t 5 ContainerName
```
### -f parameter (follow)
Output only the following logs
```sh
docker container logs -f ContainerName
```
### Change log drivers
```sh
docker container run --log-driver none
```

## Delete container
Stopped containers can waste precious resources, you should remove them.
```sh
docker container prune ContainerName
```

## Attach the container to a specific network
```sh
docker container run --name c3 -d --network sample-net alpine:latest
```
NB = We can attach more than one network to a single container

