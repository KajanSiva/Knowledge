# Docker cheatsheet

## Containers


Creating a container from an image :
```
docker create -p 6379:6379 redis:2.8
```

---

Start a non-running container :
```
docker start 6b785f78b75e
```

---

Run an image into a container (execute `docker create` and `docker start`) :
```
docker run -d -p 8080:8080 -e WHO="Sean and Karl" example/docker-node-hello:latest
```
Options :
- `-d` : detach and run in the background
- `-p` : attach port of the Docker container into a port of the host
- `-e` : Set environment variables
- `--mount` : Attach a filesystem mount to the container

By default, volumes are persisted. But we can define non persisted volums with the tmpfs type. Useful to run test containers : https://docs.docker.com/storage/tmpfs/

---

List running containers :
```
docker ps | docker container ls
```

List all containers :
```
docker ps -a
```

---

Run a command on a container :
```
docker run --rm -ti 8a773166616c /bin/bash
```
Useful for debugging

---

To get inside a running container :
```
docker exec -i -t 589f2ad30138 /bin/bash
```

---

List opened ports of a container :
```
docker container port 589f2ad30138
```

---

Stop a container :
```
docker stop b7145e06083f
```

---

Kill a container :
```
docker kill 6b785f78b75e
```

---

Pause / unpause a container :
```
docker pause 6b785f78b75e
docker unpause 6b785f78b75e
```

---

Remove a non-running container :
```
docker rm 92b797f12af1
```

---

To delete all of the containers on the Docker hosts, use the following command:
```
docker rm $(docker ps -a -q)
```

---

## Images

Build a Docker image :
```
docker build -t tag_name .
```

---

List all the images :
```
docker images
```

---

Remove an image :
```
docker rmi 873c28292d23
```

---

Remove all images and containers :
```
docker system prune
```

---

To delete all the images on the Docker host, this command will get the job done:
```
docker rmi $(docker images -q)
```

---

Show the history of an image :
```
docker history
```

---

## Network

```
docker network ls
docker network inspect
docker network create --driver
docker network connect
docker network disconnet
```

---
