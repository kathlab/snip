# Docker basics

Here are the basic docker commands for everyday use.

❗ __Don't know a name? TAB is your trusty friend!__ ❗

List all docker images:
--

```
$ docker image ls -a
```

List all docker containers:
--

```
$ docker container ls -a
```

Create docker container and start:
--

```
$ docker compose up -d
```


Start an existing container:
--

```
$ docker start CONTAINER_NAME
```

Attach a shell to a running container:
--

```
$ docker exec -it CONTAINER_NAME /bin/bash
```

Stop a running container:
--

```
$ docker stop CONTAINER_NAME
```

Remove container:
--

```
$ docker container rm CONTAINER_NAME
```

Remove image:
--

```
$ docker image rm IMAGE_NAME
```

Cleanup containers:
--

```
$ docker container purge
```

Cleanup system:
--

This one you'll need to keep an eye on! Your disk may be cached up by massive amounts of data overwise.

```
docker system df
docker system prune
```