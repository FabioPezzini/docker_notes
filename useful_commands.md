#### Delete all containers in a host
```sh
docker container rm -f $(docker container ls -a -q)
```
#### Get ID of container by name
```sh
export CONTAINER_ID=$(docker container ls -a | grep ContainerName | awk '{print
$1}')
```
