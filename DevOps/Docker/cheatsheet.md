Build a Docker image :
````
docker build -t tag_name .
```

---

Run a command in a running container :
```
docker run --rm -ti 8a773166616c /bin/bash
```
Useful for debugging

---

Run an image into a container :
```
docker run -d -p 8080:8080 -e WHO="Sean and Karl" example/docker-node-hello:latest
```
Options :
- `-d` : detach and run in the background
- `-p` : attach port of the Docker container into a port of the host
- `-e` : Set environment variables

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

Rmove a non-running container :
```
docker rm 92b797f12af1
```

---

Show the history of an image :
```
docker history
```

