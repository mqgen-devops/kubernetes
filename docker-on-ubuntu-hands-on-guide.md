# Docker on Ubuntu — Hands-On Installation and First Container Lab

> A practical, terminal-tested walkthrough for installing Docker Engine on Ubuntu, validating the installation, running Docker without `sudo`, pulling images, and launching an NGINX container.

![Ubuntu](https://img.shields.io/badge/Ubuntu-26.04_LTS-E95420?logo=ubuntu&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-Engine_29.7.2-2496ED?logo=docker&logoColor=white)
![Architecture](https://img.shields.io/badge/Architecture-ARM64-6C757D)
![Status](https://img.shields.io/badge/Lab-Tested-success)

---

## Table of Contents

- [1. Lab Environment](#1-lab-environment)
- [2. Prerequisites](#2-prerequisites)
- [3. Check the Ubuntu Version and Architecture](#3-check-the-ubuntu-version-and-architecture)
- [4. Remove Potentially Conflicting Packages](#4-remove-potentially-conflicting-packages)
- [5. Install Docker Engine from Docker's APT Repository](#5-install-docker-engine-from-dockers-apt-repository)
- [6. Verify the Docker Installation](#6-verify-the-docker-installation)
- [7. Test Docker with `hello-world`](#7-test-docker-with-hello-world)
- [8. Run Docker Without `sudo`](#8-run-docker-without-sudo)
- [9. Docker Images vs Containers](#9-docker-images-vs-containers)
- [10. Pull and List Docker Images](#10-pull-and-list-docker-images)
- [11. Run an NGINX Web Server](#11-run-an-nginx-web-server)
- [12. Work Inside a Running Container](#12-work-inside-a-running-container)
- [13. Container Lifecycle Commands](#13-container-lifecycle-commands)
- [14. What Happens When a Container Is Removed?](#14-what-happens-when-a-container-is-removed)
- [15. Useful Docker Commands](#15-useful-docker-commands)
- [16. What I Validated in This Lab](#16-what-i-validated-in-this-lab)

---

## 1. Lab Environment

These notes are based on commands I executed directly from my Ubuntu terminal rather than only copying an installation procedure.

| Item | Value observed in the lab |
|---|---|
| Hostname | `docker-ubuntu` |
| Operating system | Ubuntu 26.04 LTS |
| Codename | `resolute` |
| CPU architecture | `arm64` |
| Docker Engine | `29.7.2` |
| Docker API | `1.55` |
| containerd | `2.3.3` |
| Test container | `hello-world` |
| Web-server image | `nginx:alpine` |
| Published NGINX port | `8080` on the host → `80` in the container |

> [!NOTE]
> Package and image versions change over time. The versions above are the versions I observed during this lab session.

---

## 2. Prerequisites

Before starting, I used a system with:

- a 64-bit Ubuntu installation;
- a user account with `sudo` privileges;
- Internet access; and
- a terminal session.

---

## 3. Check the Ubuntu Version and Architecture

Before installing Docker, I first confirmed the operating system and architecture. This matters because Docker's repository configuration uses both the Ubuntu codename and CPU architecture.

### Check the Ubuntu release

```bash
cat /etc/os-release
```

My terminal returned:

```text
PRETTY_NAME="Ubuntu 26.04 LTS"
NAME="Ubuntu"
VERSION_ID="26.04"
VERSION="26.04 (Resolute Raccoon)"
VERSION_CODENAME=resolute
ID=ubuntu
ID_LIKE=debian
UBUNTU_CODENAME=resolute
```

### Check the CPU architecture

```bash
dpkg --print-architecture
```

Observed output:

```text
arm64
```

> [!IMPORTANT]
> The repository configuration later reads these values automatically, so checking them up front makes troubleshooting much easier.

---

## 4. Remove Potentially Conflicting Packages

Ubuntu may already contain Docker-related packages from other repositories. Before installing Docker Engine from Docker's own repository, I checked for and removed the commonly conflicting packages.

```bash
sudo apt remove $(dpkg --get-selections \
  docker.io docker-compose docker-compose-v2 docker-doc \
  docker-buildx podman-docker containerd runc | cut -f1)
```

In my lab, none of those packages were already installed:

```text
dpkg: no packages found matching docker.io
dpkg: no packages found matching docker-compose
dpkg: no packages found matching docker-compose-v2
dpkg: no packages found matching docker-doc
dpkg: no packages found matching docker-buildx
dpkg: no packages found matching podman-docker
dpkg: no packages found matching containerd
dpkg: no packages found matching runc

Summary:
  Upgrading: 0, Installing: 0, Removing: 0
```

> [!TIP]
> Seeing `no packages found matching ...` here was not an installation failure. It simply confirmed that my system did not have those conflicting packages installed.

---

## 5. Install Docker Engine from Docker's APT Repository

I installed Docker using Docker's APT repository. The process has several small steps, and each one has a specific purpose.

### Step 1 — Refresh the APT package index

```bash
sudo apt update
```

### Why this is required

`apt` maintains a local index of packages available from the repositories configured on the system. Running `apt update` refreshes that metadata before installing prerequisites.

My system successfully reached the Ubuntu repositories:

```text
Hit:1 http://us.archive.ubuntu.com/ubuntu resolute InRelease
Hit:2 http://security.ubuntu.com/ubuntu resolute-security InRelease
Hit:3 http://us.archive.ubuntu.com/ubuntu resolute-updates InRelease
Hit:4 http://us.archive.ubuntu.com/ubuntu resolute-backports InRelease
```

---

### Step 2 — Install repository prerequisites

```bash
sudo apt install -y ca-certificates curl
```

### What these packages do

| Package | Purpose |
|---|---|
| `ca-certificates` | Provides trusted Certificate Authority certificates so HTTPS connections can be validated. |
| `curl` | Downloads content over HTTP/HTTPS; here it is used to retrieve Docker's signing key. |
| `-y` | Automatically answers `yes` to the package installation prompt. |

In my terminal, `ca-certificates` was already current and `curl` was installed successfully.

```text
ca-certificates is already the newest version.
Installing:
  curl
...
Setting up curl ...
```

---

### Step 3 — Create the APT keyring directory

```bash
sudo install -m 0755 -d /etc/apt/keyrings
```

### What this command does

- `install -d` creates the directory if it does not already exist.
- `-m 0755` sets its permissions to `rwxr-xr-x`.
- `/etc/apt/keyrings` is used to store repository signing keys.

No output is expected when this command succeeds.

---

### Step 4 — Download Docker's signing key

```bash
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg \
  -o /etc/apt/keyrings/docker.asc
```

### Why the signing key matters

APT uses the signing key to verify that repository metadata was signed by the expected publisher and has not been silently replaced.

Useful `curl` options here:

| Option | Meaning |
|---|---|
| `-f` | Fail on HTTP errors. |
| `-s` | Silent mode. |
| `-S` | Still show errors when silent mode is enabled. |
| `-L` | Follow redirects. |
| `-o` | Write the downloaded content to the specified file. |

Make the key readable by APT:

```bash
sudo chmod a+r /etc/apt/keyrings/docker.asc
```

This grants read permission to all users while leaving write access restricted.

---

### Step 5 — Add Docker's repository

```bash
sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/ubuntu
Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
Components: stable
Architectures: $(dpkg --print-architecture)
Signed-By: /etc/apt/keyrings/docker.asc
EOF
```

### What happens here

This creates `/etc/apt/sources.list.d/docker.sources` and tells APT:

- where Docker packages are hosted;
- which Ubuntu release/codename to use;
- which architecture to request;
- which repository component to use; and
- which signing key should validate the repository.

Because my host was Ubuntu 26.04 ARM64, the generated values were:

```text
Types: deb
URIs: https://download.docker.com/linux/ubuntu
Suites: resolute
Components: stable
Architectures: arm64
Signed-By: /etc/apt/keyrings/docker.asc
```

> [!NOTE]
> The shell substitutions in the command make the repository definition adapt automatically to the machine instead of hard-coding `resolute` or `arm64`.

---

### Step 6 — Refresh APT again

```bash
sudo apt update
```

### Why run `apt update` a second time?

The first refresh happened **before** Docker's repository existed in APT's configuration. After adding the new repository, APT must refresh its package metadata again so it can discover Docker packages from that source.

This time I could see Docker's repository being contacted:

```text
Get:1 https://download.docker.com/linux/ubuntu resolute InRelease
Get:2 https://download.docker.com/linux/ubuntu resolute/stable arm64 Packages
Hit:3 http://us.archive.ubuntu.com/ubuntu resolute InRelease
Hit:4 http://us.archive.ubuntu.com/ubuntu resolute-updates InRelease
Hit:5 http://us.archive.ubuntu.com/ubuntu resolute-backports InRelease
Hit:6 http://security.ubuntu.com/ubuntu resolute-security InRelease
```

That was a useful confirmation that APT recognized the Docker repository correctly.

---

### Step 7 — Install Docker Engine and plugins

```bash
sudo apt install -y \
  docker-ce \
  docker-ce-cli \
  containerd.io \
  docker-buildx-plugin \
  docker-compose-plugin
```

### What each package provides

| Package | Purpose |
|---|---|
| `docker-ce` | Docker Engine daemon and core runtime integration. |
| `docker-ce-cli` | The `docker` command-line client. |
| `containerd.io` | Container runtime used underneath Docker Engine. |
| `docker-buildx-plugin` | Extended BuildKit-based image building through `docker buildx`. |
| `docker-compose-plugin` | Docker Compose v2 through the `docker compose` command. |

The installation completed successfully and systemd service links were created for both Docker and containerd.

```text
Setting up containerd.io ...
Setting up docker-buildx-plugin ...
Setting up docker-compose-plugin ...
Setting up docker-ce-cli ...
Setting up docker-ce ...
Created symlink ... docker.service
Created symlink ... docker.socket
```

---

## 6. Verify the Docker Installation

### Check the Docker client version

```bash
docker --version
```

Observed output:

```text
Docker version 29.7.2, build a7dcaa6
```

### Check client and server details

```bash
sudo docker version
```

Important values from my output:

```text
Client: Docker Engine - Community
 Version:           29.7.2
 API version:       1.55
 OS/Arch:           linux/arm64

Server: Docker Engine - Community
 Engine:
  Version:          29.7.2
  API version:      1.55
  OS/Arch:          linux/arm64

containerd:
 Version:           v2.3.3
```

### Check the Docker service

```bash
sudo systemctl status docker
```

My service was active and running:

```text
● docker.service - Docker Application Container Engine
     Loaded: loaded (...; enabled; preset: enabled)
     Active: active (running)
     Main PID: 9163 (dockerd)
```

A shorter check is:

```bash
sudo systemctl is-active docker
```

Observed output:

```text
active
```

If Docker is installed but not running, start it with:

```bash
sudo systemctl start docker
```

> [!TIP]
> When viewing `systemctl status docker`, press `q` to leave the status screen.

---

## 7. Test Docker with `hello-world`

The standard first functional test is:

```bash
sudo docker run hello-world
```

The first run on my host showed Docker downloading the image because it was not already available locally:

```text
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
...
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.
```

### What `docker run hello-world` actually does

```text
docker run hello-world
        |
        v
Docker checks the local image cache
        |
        | image not present
        v
Docker pulls hello-world from the registry
        |
        v
Docker creates a container
        |
        v
The container executes /hello
        |
        v
The output is streamed to the terminal
        |
        v
The process exits successfully
```

The container is intentionally short-lived. After it exits, it still exists until removed.

```bash
sudo docker ps -a
```

Example from my terminal:

```text
CONTAINER ID   IMAGE         COMMAND    STATUS                      NAMES
935d819636e8   hello-world   "/hello"   Exited (0) 43 seconds ago   kind_kapitsa
```

> [!NOTE]
> `Exited (0)` means the container's main process finished successfully with exit code `0`.

---

## 8. Run Docker Without `sudo`

Initially, Docker daemon access may require root privileges. To use the Docker CLI as my normal user, I added the account to the `docker` group.

```bash
sudo usermod -aG docker $USER
```

### Command breakdown

| Part | Meaning |
|---|---|
| `usermod` | Modifies an existing user account. |
| `-a` | Appends the group instead of replacing existing supplementary groups. |
| `-G docker` | Adds the user to the supplementary `docker` group. |
| `$USER` | Expands to the current username. |

The safest way to activate the new membership is to **log out and log back in**.

Where available, a new shell can also be started with:

```bash
newgrp docker
```

Then verify group membership:

```bash
groups
```

My account showed `docker` in the group list:

```text
skalyanapu adm cdrom sudo dip plugdev users lpadmin lxd docker
```

I then repeated the test without `sudo`:

```bash
docker run hello-world
```

Observed result:

```text
Hello from Docker!
This message shows that your installation appears to be working correctly.
```

> [!WARNING]
> Membership in the `docker` group effectively provides root-level capabilities on the host. Only trusted users should be added to this group.

From this point onward, the examples use Docker without `sudo`.

---

## 9. Docker Images vs Containers

This distinction is fundamental to working with Docker.

### Docker image

A Docker **image** is a packaged, read-only template containing the filesystem and metadata needed to create containers.

Examples:

```text
ubuntu:24.04
nginx:alpine
python:3.13
redis:alpine
```

Think of an image as a **blueprint**.

### Docker container

A Docker **container** is an instance created from an image. It can be running or stopped.

```text
                  +--> Container 1
                  |
nginx:alpine -----+--> Container 2
                  |
                  +--> Container 3
```

In simple terms:

```text
Image     = blueprint/template
Container = instance created from that blueprint
```

One image can be used to create many independent containers.

---

## 10. Pull and List Docker Images

### Pull syntax

```bash
docker pull IMAGE[:TAG]
```

Equivalent form:

```bash
docker image pull IMAGE[:TAG]
```

### Examples

```bash
# Ubuntu
docker pull ubuntu:24.04

# NGINX on Alpine Linux
docker pull nginx:alpine

# Python slim image
docker pull python:3.13-slim
```

If no tag is supplied, Docker commonly interprets the reference as `:latest`.

```text
ubuntu
```

is equivalent to:

```text
ubuntu:latest
```

> [!TIP]
> For repeatable builds and environments, prefer an explicit version or tag instead of relying on `latest`.

### Pull NGINX

I pulled the Alpine-based NGINX image:

```bash
docker pull nginx:alpine
```

My terminal showed the image layers being downloaded and then confirmed success:

```text
alpine: Pulling from library/nginx
977aedb192ad: Pull complete
5de55e5ef9c0: Pull complete
...
Digest: sha256:4a73073bd557c65b759505da037898b61f1be6cbcc3c2c3aeac22d2a470c1752
Status: Downloaded newer image for nginx:alpine
docker.io/library/nginx:alpine
```

### List local images

```bash
docker images
```

or:

```bash
docker image ls
```

---

## 11. Run an NGINX Web Server

With the image available locally, I started an NGINX container in detached mode and published it on port `8080`.

```bash
docker run -d \
  --name my-nginx \
  -p 8080:80 \
  nginx:alpine
```

Docker returned a container ID:

```text
f99ff501db79a2bb4303cf3e8f4d6246a07cb25f9eb1b762b68b718681c9391b
```

### Command breakdown

| Part | Meaning |
|---|---|
| `docker run` | Creates and starts a new container from an image. |
| `-d` | Runs the container in detached/background mode. |
| `--name my-nginx` | Assigns a human-readable container name. |
| `-p 8080:80` | Maps host TCP port `8080` to container TCP port `80`. |
| `nginx:alpine` | Specifies the image used to create the container. |

### Port mapping

```text
Host / Browser
http://localhost:8080
          |
          | host port 8080
          v
+---------------------------+
| Docker container          |
|                           |
| NGINX listening on :80    |
+---------------------------+
```

The left side of `8080:80` is the **host port**. The right side is the **container port**.

### Test NGINX from the host

```bash
curl http://localhost:8080
```

The response contained the default NGINX page:

```html
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, nginx is successfully installed and working.</p>
</body>
</html>
```

That was the practical proof that:

1. the container was running;
2. NGINX was listening inside the container;
3. Docker port publishing was working; and
4. the Ubuntu host could reach the service through `localhost:8080`.

### Check the running container

```bash
docker ps
```

Observed output:

```text
CONTAINER ID   IMAGE          STATUS              PORTS                                     NAMES
f99ff501db79   nginx:alpine   Up About a minute   0.0.0.0:8080->80/tcp, [::]:8080->80/tcp   my-nginx
```

### View container logs

```bash
docker logs my-nginx
```

A successful startup included entries such as:

```text
/docker-entrypoint.sh: Configuration complete; ready for start up
2026/08/17 02:41:51 [notice] 1#1: nginx/1.31.3
2026/08/17 02:41:51 [notice] 1#1: start worker processes
172.17.0.1 - - [17/Aug/2026:02:43:02 +0000] "GET / HTTP/1.1" 200 896 "-" "curl/8.18.0" "-"
```

The HTTP `200` entry confirms that NGINX handled the request successfully.

My browser also requested `/favicon.ico`, which produced a `404`. That does **not** mean the NGINX container failed; the default page simply did not include that file.

---

## 12. Work Inside a Running Container

To start an interactive shell inside the running NGINX container:

```bash
docker exec -it my-nginx sh
```

### What the command means

| Part | Meaning |
|---|---|
| `docker exec` | Starts an additional process inside an already-running container. |
| `-i` | Keeps standard input open. |
| `-t` | Allocates a pseudo-terminal. |
| `my-nginx` | Name of the target container. |
| `sh` | Shell process to start inside the container. |

Because `nginx:alpine` is a minimal Alpine-based image, `sh` is a safer assumption than `bash`.

My session looked like this:

```text
skalyanapu@docker-ubuntu:~$ docker exec -it my-nginx sh
/ # exit
skalyanapu@docker-ubuntu:~$
```

The prompt changing to `/ #` showed that I was inside the container. Running `exit` terminated only that interactive shell; it did **not** stop the NGINX container.

> [!IMPORTANT]
> `docker exec` does not create a new container. It starts another process inside an existing running container.

---

## 13. Container Lifecycle Commands

### Stop a container gracefully

```bash
docker stop my-nginx
```

Observed output:

```text
my-nginx
```

`docker stop` asks the container's main process to terminate gracefully and waits before escalating if necessary.

### Start a stopped container

```bash
docker start my-nginx
```

This starts the **same existing container** again.

### Restart a container

```bash
docker restart my-nginx
```

Conceptually, this performs a stop followed by a start on the existing container.

### Inspect a container

```bash
docker inspect my-nginx
```

`docker inspect` returns detailed JSON describing the container, including information such as:

- container ID and name;
- image reference;
- command and entry point;
- environment variables;
- networking configuration;
- port mappings;
- mounts;
- state and timestamps; and
- restart policy.

Useful targeted examples:

```bash
# Container IP address
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' my-nginx

# Current container state
docker inspect -f '{{.State.Status}}' my-nginx
```

---

## 14. What Happens When a Container Is Removed?

### Remove a stopped container

```bash
docker rm my-nginx
```

Normally, Docker expects a container to be stopped before ordinary removal.

### What if the container is still running?

This command:

```bash
docker rm my-nginx
```

will refuse to remove a running container.

To force removal:

```bash
docker rm -f my-nginx
```

I used the force form in the lab and Docker returned the container name:

```text
my-nginx
```

> [!WARNING]
> `docker rm -f` is destructive. It forcefully terminates a running container and removes the container object. Use a normal `docker stop` followed by `docker rm` when you want a cleaner shutdown.

### Stop, then remove

A safer explicit sequence is:

```bash
docker stop my-nginx
docker rm my-nginx
```

### What is removed and what remains?

Removing a container removes the container instance, but the image remains locally unless you remove the image separately.

```text
nginx:alpine image
      |
      +--> my-nginx container   <-- docker rm removes this
      |
      +--> another container

nginx:alpine image              <-- still remains locally
```

To remove an unused image separately:

```bash
docker image rm nginx:alpine
```

> [!CAUTION]
> Data written only to a container's writable layer is disposable with that container. Persistent application data should normally be stored in a volume or bind mount.

---

## 15. Useful Docker Commands

| Task | Command |
|---|---|
| Show Docker version | `docker --version` |
| Show detailed client/server versions | `docker version` |
| Show Docker system information | `docker info` |
| List running containers | `docker ps` |
| List all containers | `docker ps -a` |
| List local images | `docker image ls` |
| Pull an image | `docker pull IMAGE:TAG` |
| Run a container | `docker run IMAGE:TAG` |
| Run in background | `docker run -d IMAGE:TAG` |
| Publish a port | `docker run -p HOST:CONTAINER IMAGE:TAG` |
| View logs | `docker logs CONTAINER` |
| Follow logs live | `docker logs -f CONTAINER` |
| Open a shell | `docker exec -it CONTAINER sh` |
| Stop a container | `docker stop CONTAINER` |
| Start a container | `docker start CONTAINER` |
| Restart a container | `docker restart CONTAINER` |
| Inspect details | `docker inspect CONTAINER` |
| Remove a stopped container | `docker rm CONTAINER` |
| Force-remove a container | `docker rm -f CONTAINER` |
| Remove an image | `docker image rm IMAGE:TAG` |

---

## 16. What I Validated in This Lab

This session went beyond installing packages. I validated the complete basic Docker workflow from the terminal:

- [x] Confirmed Ubuntu release and CPU architecture.
- [x] Checked for conflicting Docker/container packages.
- [x] Added Docker's APT key and repository.
- [x] Installed Docker Engine, CLI, containerd, Buildx, and Compose v2.
- [x] Confirmed that the Docker daemon was `active (running)`.
- [x] Verified Docker Engine `29.7.2` on `linux/arm64`.
- [x] Ran `hello-world` successfully.
- [x] Added my user to the `docker` group and validated Docker without `sudo`.
- [x] Pulled `nginx:alpine` from the registry.
- [x] Started an NGINX container in detached mode.
- [x] Published host port `8080` to container port `80`.
- [x] Verified NGINX with `curl http://localhost:8080`.
- [x] Confirmed the running container with `docker ps`.
- [x] Reviewed NGINX logs with `docker logs`.
- [x] Opened an interactive shell inside the container with `docker exec`.
- [x] Tested container stop/restart/removal operations.

> [!IMPORTANT]
> At the end of the lab, Docker was installed, the daemon was running, normal-user access worked, and an NGINX container successfully served HTTP traffic through Docker port mapping.

---

## Final Mental Model

```text
Dockerfile / Registry Image
          |
          v
     Docker Image
     (read-only)
          |
       docker run
          |
          v
    Docker Container
    (running instance)
          |
          +--> logs
          +--> exec
          +--> ports
          +--> stop/start/restart
          +--> remove
```

Once this image/container relationship is clear, the rest of Docker becomes much easier to reason about.

---

## Next Steps

After this foundation, the next useful topics are:

1. building a custom image with a `Dockerfile`;
2. container environment variables;
3. volumes and bind mounts;
4. Docker networks;
5. Docker Compose;
6. image tagging and pushing to a registry;
7. container health checks; and
8. basic Docker troubleshooting and cleanup.

---

**Lab style:** hands-on, terminal-tested, and documented from actual command output.
