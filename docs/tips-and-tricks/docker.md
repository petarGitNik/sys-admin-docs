# Docker

## Basic workflow

Login and logout:

```bash
docker login
docker logout
```

Run a container, and give it a custom name:

```bash
docker run -d --name web1 -p 8081:80 tutum/hello-world
```

List containers (running, all):

```bash
docker ps
docker ps -a
```

Start and stop a container:

```bash
docker stop web1
docker start web1
```

## Dealing with docker images on your machine

Remove a container by ID (you can get the ID from `docker ps`):

```bash
docker rm <CONTAINER_ID>
```

List all images on your machine:

```bash
docker images
docker image ls
```

Remove image from your machine:

```bash
docker image rm <IMAGE_ID>
```

If ID does not work, try name, or combination of name and tag.

## Build and push image to Docker hub

Build an image (you must use path to directory containing `Dockerfile`, not path to `Dockerfile` itself):

```bash
docker image build -t <username>/<container_app_name>:<tag> /image/directory/
```

Push to your repository on Docker hub:

```bash
docker push <username>/<container_app_name>:<tag>
```

## Tips and Tricks

If you're not sure on what port the Docker container is exposed you can use:

```bash
docker inspect <CONTAINER_ID> | grep -i port
```

In general, there are a lot of useful information given by `docker inspect`.

Start a bash session in container:

```bash
docker exec -it <CONTAINER_ID> bash
```
