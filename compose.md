Docker Compose is a tool provided by Docker that is mainly used where you need to run and orchestrate containers running on a single Docker host. This includes, but is not limited to, development, continuous integration (CI), automated testing, manual QA, or demos.

Docker Compose uses files formatted in YAML as input. By default, Docker Compose expects these files to be called `docker-compose.yml`, but other names are possible. The content of a docker-compose.yml is said to be a declarative way of describing and running a containerized application potentially consisting of more than a single container.

When we use Docker compose each application service runs in its own container. Let's have a look at a simple `docker-compose.yml`:
```
version: "2.4"
    services:
        web:
            image: repooftheimage/ch11-web:2.0
            build: web
            ports:
            - 80:3000
        db:
            image: repooftheimage/ch11-db:2.0
            build: db
            volumes: pets-data:/var/lib/postgresql/data
    volumes:
        pets-data:
```
There most important section is the `services`, here are presented all the containers (in the example each service define a different container) and what they have to run.
In `volumes` are displayed all the volumes that we want to use, if the volume exists it will be used otherwise it will be created and then attached.
NB = each service and the relative images must located in a subfolder with the exact name defined in the `build`, by default the services are connected to the same network.

## Build images with Docker Compose
Navigate to the subfolders that contains the `docker-compose.yml` and run:
```sh
docker-compose build
```

## Run application with Docker Compose
```sh
docker-compose up
```
### -d parameter (background activity)
If we want run the application in the background (runs as a daemon) we can do
```sh
docker-compose up -d
```

## List all container of a Docker Compose
```sh
docker-compose ps
```

## Stop and clean Docker Compose
```sh
docker-compose down
```
### Stop and clean (also the volumes) Docker Compose
```sh
docker-compose down -v
```

## Scale a service of Docker Compose
If the application become popular we have to scale (run multiple instances) the service. We can scale a specific service like this
```sh
docker-compose up --scale web=3
```
In this case I create 3 copies of the service instance.
NB = We have to leave to Docker the decision of which port use for the web service or they will collide, so we have to modify the `docker-compose.yml`
```
...
    build: web
    ports: 3000
...
```

## Push Docker Compose (to DockerHub)
```sh
docker login -u username -p password
docker-compose push
```

## Override Env variables in Docker Compose
If we have a `Dockerfile` with different ENV but we want to override that in the `docker-compose.yml` we can add in the file in this manner:
```
version: "2.4"
    services:
        web:
            build: web
            ports:
                - 5000:3000
            environment:
                POSTGRES_PASSWORD: ci-pass
        db:
            build: db
            environment:
                POSTGRES_PASSWORD: ci-pass
```
Then to override the previously defined and build `docker-compose.yml` we have to do
```sh
docker-compose -f docker-compose.yml -f docker-compose-ci.yml up -d --
build
```