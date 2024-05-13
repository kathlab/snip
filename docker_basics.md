# Docker basics

Here are the basic docker commands for everyday use.

❗ __Don't know a name? TAB is your trusty friend!__ ❗

List all docker images:
--

```
docker image ls -a
```

List all docker containers:
--

```
docker container ls -a
```

Create docker container and start:
--

```
docker compose up -d
```

Start an existing container:
--

```
docker start CONTAINER_NAME
```

Attach a shell to a running container:
--

```
docker exec -it CONTAINER_NAME /bin/bash
```

Start bash in a new container
---

```
docker run --rm -it --entrypoint bash IMAGENAME:TAG
```

Stop a running container:
--

```
docker stop CONTAINER_NAME
```

Remove container:
--

```
docker container rm CONTAINER_NAME
```

Remove image:
--

```
docker image rm IMAGE_NAME
```

Cleanup containers:
--

```
docker container purge
```

Cleanup system:
--

This one you'll need to keep an eye on! Your disk may be cached up by massive amounts of data overwise.

```
docker system df
docker system prune
```

Export containers:
--

1. create image from running container:

```
docker commit CONTAINERNAME local/CONTAINERNAME:run
```

2. export image to tar:

```
docker save local/CONTAINERNAME:run > CONTAINERNAME.tar
```

Import images:
--

```
docker load < CONTAINERNAME.tar
```