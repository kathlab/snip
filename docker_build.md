# Docker build

Like to build docker images? Here's how that works!

Create docker image:
--

```
docker build -f Dockerfile -t local/IMAGE_NAME:TAG .
```

Example: ```docker build -f Dockerfile -t local/jupyternotebook:latest .```

💣 Important: If you want to use local images, you'll need to add a prefix (e.g. local/) to your IMAGE_NAME:TAG. The prefix can be anything but it should not exist on docker hub website.

💡 The default TAG is __latest__.

Create docker container the recommended way (using a docker-compose.yaml):
--

1. Create a docker-compose.yaml inside your project folder

        Modify SERVICE_NAME (= A docker service name you'd like to use)

        Modify IMAGE_NAME (= The docker image name the container is based of)

        Modify CONTAINER_NAME (= Optional name for your container, otherwise will be random generated)

```
version: "3.9"
services:
  SERVICE_NAME:
    image: IMAGE_NAME
    container_name: CONTAINER_NAME
    command: /bin/sh -c "while sleep 1000; do :; done" # keep container open while running
    ports: # Host:Container
      - "8888:8888" # Jupyter Notebook example
      - "8000:80" # A webserver port
    volumes: # Share outside files to your container
      - type: bind # a
        source: ./notebooks
        target: /notebooks
      - type: bind # your vscode project folder
        source: ./
        target: /workspace
    deploy: # Copy'n'paste NVIDIA Cuda in container
      resources:
        reservations:
          devices:
            - driver: nvidia
              count: 1
              capabilities: [gpu]
volumes:
  SERVICE_NAME:
```

2. Create container and start detached (in the background)

```
docker compose up -d
```

3. Check output of the container startup

```
docker logs CONTAINER_NAME
```