## Image Layers
Images are templates from which containers are created.
These images are not
made up of just one monolithic block but are composed of many layers. The first layer in
the image is also called the base layer (base image).
Each individual layer contains files and folders. Each layer only contains the changes to the
filesystem with respect to the underlying layers. Docker uses a [Union filesystem](architecture.md#Unionfs).
The layers of a container image are all immutable, the only possible operation affecting the layer is its
physical deletion.
Example of a custom layered image:
1. Ubuntu Linux
2. Add Nginx
3. Add static files

Each layer only contains the delta of changes in regard to the previous set of layers. The
content of each layer is mapped to a special folder on the host system, which is usually a
subfolder of `/var/lib/docker`.
An advantage of the immutability of
image layers is that they can be shared among many containers created from this image. All
that is needed is a thin, writable container layer for each container.

## Copy-on-write
Docker uses the copy-on-write technique,Copy-on-write is a
strategy for sharing and copying files for maximum efficiency. If a layer uses a file or folder
that is available in one of the low-lying layers, then it just uses it. If, on the other hand, a
layer wants to modify, say, a file from a low-lying layer, then it first copies this file up to
the target layer and then modifies it.

## How build process works?!
It starts with the base image. From this base image,
once downloaded into the local cache, the builder creates a container and runs the first
statement of the Dockerfile inside this container. Then, it stops the container and persists
the changes made in the container into a new image layer. The builder then creates a new
container from the base image and the new layer and runs the second statement inside this
new container. Once again, the result is committed to a new layer. This process is repeated
until the very last statement in the Dockerfile is encountered. After having committed the
last layer of the new image, the builder creates an ID for this image and tags the image with
the name we provided in the build command,

# Create an Image
## Method 1: Interactive Image Creation
It consists on interactively building a container that contains all the additions and changes one desires,
and then committing those changes into a new image. 
 
We start creating a container with the base layer. `-it` is used to attach a reletypewriter (TTY).
```sh
docker container run -it --name example ubuntu /bin/sh
```
Now a TTY is open so we can install anything (like `traceroute`) in the container using the installed package manager (in this case apt). When we have finish the customization of the layer we can type `exit` to close the container
If we want to see what has changed in our container in relation to the base image, the output present a list of all modifications done on the filesystem:
```sh
docker container diff example
```
We can now use the `docker container commit` command to persist our
modifications and create a new image from them:
```sh
docker container commit example my-ubuntu
```
If we want to see how our custom image has been built:
```sh 
docker image history my-ubuntu
```

## Method 2: Using Dockerfiles
It is a declarative way of building images.
Let's look an example of a `Dockerfile`:
```
FROM python:2.7             
RUN mkdir -p /app
WORKDIR /app
COPY ./requirements.txt /app/
RUN pip install -r requirements.txt
CMD ["python", "main.py"]
```
We can now build an image called pythoner from the preceding Dockerfile, as follows:
```sh
docker image build -t pythoner .
```
NB = Each line of the Dockerfile results in a layer in the resulting image, we are containerizing a Python application. A layer distribution in a Dockerfile is upside-down

### FROM keyword
Every Dockerfile starts with the `FROM` keyword. With it, we define which base image we
want to start building our custom image from.
N.B = On [Docker Hub](https://hub.docker.com/), there are curated or official images.
### RUN keyword
The argument for RUN is any valid Linux command.
Is important to explicit set the navigation (using `cd`) if we want to run or create something into a specific directory.
If we `use more than one line`, we need to put a backslash \ at the end of the lines to
indicate to the shell that the command continues on the next line.
### COPY and ADD keywords
These two keywords are used to copy files and folders from the host into the image that
we're building. The two keywords are very similar, with the exception that
the ADD keyword also lets us copy and unpack TAR files, as well as providing a URL as a
source for the files and folders to copy. Let's look an example of how to use:
```
COPY ./web /app/web
COPY sample.txt /data/my-sample.txt
ADD sample.tar /app/bin/
ADD http://example.com/sample.txt /data/
```
NB = Wildcards are allowed.
From a security perspective, it is important to know that, by default, all files and folders
inside the image will have a user ID (UID) and a group ID (GID) of 0. We can change the ownership with the `--chown` flag:
```
ADD --chown=11:22 ./data/web* /app/data/
```
### WORKDIR keyword
Defines the working directory or context that is used when a
container is run from our custom image.All activity that happens inside the image after the preceding line will use this directory as
the working directory.
`The cd command alone is not persisted across layers.`
### CMD and ENTRYPOINT keywords
While all other keywords defined for a
Dockerfile are executed at the time the image is built by the Docker builder, these two are
actually definitions of what will happen when a container is started from the image we
define. When the container runtime starts a container, it needs to know what the process or
application will be that has to run inside that container. That is exactly what CMD and
ENTRYPOINT are used for.
`ENTRYPOINT` is
used to define the command of the expression, while `CMD` is used to define the parameters
for the command.
Example:
```
ENTRYPOINT ["ping"]
CMD ["-c","3","8.8.8.8"]
```
Alternatively, one can also use what's called the shell form, as shown here:
```
CMD command param1 param2
```
### ENV keyword
It is use to provide environment variables
```
ENV foo=123
```
### EXPOSE keyword
It declare all ports that the application is listening on and that need to be accessible from outside.
```
EXPOSE 18181/tcp
```
NB = The multi-steps builds used by Docker is useful because we can use temporary containers as dev env and pass the content to the next container like in this Dockerfile:
```
FROM alpine:3.7 AS build
RUN apk update && \
apk add --update alpine-sdk
RUN mkdir /app
WORKDIR /app
COPY . /app
RUN mkdir bin
RUN gcc hello.c -o bin/hello

FROM alpine:3.7
COPY --from=build /app/bin/hello /app/hello
CMD /app/hello
```
NB2 = When we're rebuilding a previously built image, the only layers that are rebuilt are the ones
that have changed

## Method 3: Import or load from a file
A
container image is nothing more than a tarball(`.tar` file).
So we can export an existing image:
```sh
docker image save -o ./backup/my-ubuntu.tar my-ubuntu
``` 
Or we can have an existing tarball and want to import it as an image
```sh
docker image load -i ./backup/my-ubuntu.tar
```

# Shipping Images
This process occurs when we have to ship the image through the various stages of the software delivery pipeline.
The fist step is give the image a globally unique name (`tag`) and pull to an `image registries` (by default the Docker env use Docker Hub).


A tag is used to version images, if we don't specify the tag, then Docker automaticcaly assumes we're referring to the `latest` tag.
```
docker image pull my-ubuntu:3.5
```

In general the way to define an image is by its fully qualified name like:
`<registry URL>/<User or Org>/<name>:<tag>` 

NB = If we omit the registry URL, the Docker Hub is automaticall taken.


Now we want to actually share
or ship our images to a target environment and suppose to use Docker Hub:
1. We have to tag the image
```sh
docker image tag my-ubuntu:latest accountid/my-ubuntu:1.0
```
2. We have to login
```sh
docker login -u accountid -p mypass
```
3. Now we can push the image
```sh
docker image push accountid/my-ubuntu:1.0
```