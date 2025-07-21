Podman on macOS & VSCode
===

## Install Podman Desktop

Podman macOS: [https://podman-desktop.io/docs/installation/macos-install](https://podman-desktop.io/docs/installation/macos-install)

## Install Podman Desktop compose extension

Settings ▶️ Resources ▶️ Compose ▶️ Setup

## Install VSCode extension

**VSCode remote containers:** ms-vscode-remote.remote-containers

**VSCode docker:** ms-azuretools.vscode-docker

## Configure VSCode Dev Containers & Docker Extensions

### Extension: Dev Containers

Dev ▶️ Containers ▶️ Docker Path: ```/opt/podman/bin/podman```

Dev ▶️ Containers ▶️ Docker Compose Compose Path: ```/usr/local/bin/docker-compose```

Dev ▶️ Containers ▶️ Docker Socket Path: ```/var/run/docker.sock```

### Extension: Docker

Might not work!

Docker ▶️ Docker Path: ```/opt/podman/bin/podman```

Docker ▶️ Docker Compose Path: ````/usr/local/bin/docker-compose```
