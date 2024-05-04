# Docker Engine Installation

Docker functions as a containerization platform, bundling an application and its dependencies within a container to maintain uniform functionality across varied computing setups. Operating at the OS level, it employs virtualization to package software into containers, offering isolation, resource optimization, and simplifying deployment and scaling for CI/CD workflows.

> [!NOTE]
> Use Docker as a non-privileged user, or install in rootless mode.
## I. Install Docker Engine on Ubuntu

> [!NOTE]
> Docker Engine for Ubuntu is compatible with x86_64 (or amd64), armhf, arm64, s390x, and ppc64le (ppc64el) architectures.

### Uninstall old versions

Uninstall all conflicting packages

```shell
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done
```

### Install using the apt repository

1. Set up Docker's apt repository.

```shell
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

2. Install the Docker packages. 

```shell
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

3. Verify that the Docker Engine installation is successful by running the hello-world image.

```shell
sudo docker run hello-world
```

## II. Install Docker Engine on RockyLinux

### Uninstall old versions

```shell
sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```

### Install using the rpm repository

Set up the repository:

```shell
sudo yum install -y yum-utils
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

### Install Docker Engine

1. Install Docker Engine, containerd, and Docker Compose

```shell
sudo yum install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

2. Start Docker.

```shell
sudo systemctl start docker
```

3. Verify that the Docker Engine installation is successful by running the `hello-world` image.

```shell
sudo docker run hello-world
```

## Run docker as non root user

```shell
sudo groupadd docker
sudo usermod -aG docker $USER
