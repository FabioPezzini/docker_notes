Data volumes are containers that consume, produce data and modify a state, this behavior add a concept of stateful to a container (preferably they are stateless like we have seen in the past).
`When a container that uses a
volume dies, the volume continues to exist`.

In the classic use of Docker seen before, if a container create a file the change persist only before the container removal, in fact if we now remove the container from memory, its container layer will also be removed, and
with it, all the changes will be irreversibly deleted. If we need our changes to persist even
beyond the lifetime of the container we have to use `Data volumes`

## Create data volume
This will create a named volume that can then be mounted
into a container and used for persistent data access or storage
```sh
docker volume create example
```
The default volume driver is the so-called local driver, which stores the data
locally in the host filesystem.

## Find the data volume
The easiest way to find out where the data is stored on the host is to inspect it (the actual location can differ from system to system)
```sh
docker volume inspect example
```
The host folder can be found in the output under `Mountpoint`.
To access the folder we might need sudo privileges.

## Mount data volume
Once we have created a named volume, we can mount it into a container.
First we have to mount the `example` volume to the `/data` folder inside the container:
```sh
docker container run --name testVolume -it -v example:/data ubuntu /bin/sh
```
Now we can create files in `/data` folder:
```sh
cd /data 
echo "Insert data" > data.txt
exit
```
Now every data contained in the `/data` folder will persist even if the container is deleted, the only thing to do is attach it to another container to use its content.

## Removing data volume
It is important to remember that removing a volume destroys the containing data irreversibly.
```sh
docker volume rm example
```

## Sharing data between containers
As always when multiple applications or processes concurrently access data, we have to be very careful to avoid inconsistencies. To avoid concurrency problems such as race conditions, we ideally have only one application or process that is creating or modifying
data, while all other processes concurrently accessing this data only read it. We can enforce a process running in a container to only be able to read the data in a volume by mounting this volume as read-only.
We have to set one of the two containers as read-only (`ro`)
```sh
docker container run -it --name reader -v example-data:/app/data:ro ubuntu:19.04 /bin/bash
```

## Creating Host volume
In certain scenarios,it is very useful to use volumes that mount a specific host folder (to consume the data used by the host, for example creating a web server with nginx and changing the content of a HTML file by the host side or in a dev environment).
Can be achieved using the (`pwd`) that point to the current host location (or we can set the absolute path of the host location that we want to synchronize)
```sh
docker container run -d --name my-site -v $(pwd):/usr/share/nginx/html -p 8080:80 my-website:1.0
```
You can make as many changes in your web files and always immediately see the result in the browser, without having to rebuild the image and restart the container containing your website.
It is important to note that the updates are now propagated bi-directionally. If you make changes on the host, they will be propagated to the container, and viceversa.
