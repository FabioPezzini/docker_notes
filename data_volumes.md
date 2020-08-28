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
