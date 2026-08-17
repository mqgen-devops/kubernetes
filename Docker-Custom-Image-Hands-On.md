# 🐳 Build Your First Custom Docker Image — Hands-On NGINX Lab

> [!IMPORTANT]
> **Hands-on status:** I built this image from my Ubuntu terminal, started the container, verified it with `docker ps`, and tested the web page with `curl`.

This lab documents my first custom Docker image build using the official **NGINX Alpine** image as the base. Instead of only pulling and running an existing image, I created my own `index.html`, added it to an image with a `Dockerfile`, built the image, ran a container, and verified the result from the terminal.

---

## 🎯 What I Built

A small static web page running inside an NGINX container.

```text
Local machine
    |
    |  http://localhost:8080
    v
Docker container
    |
    |  port 80
    v
NGINX + custom index.html
```

### ✅ What I verified from the terminal

- The custom image built successfully as `my-first-web:v1`.
- The image appeared in `docker image ls`.
- The container started as `my-first-web-container`.
- Port `8080` on the host mapped to port `80` in the container.
- `curl http://localhost:8080` returned my custom HTML page.

---

## 🟦 Step 1 — Create the Project Directory

I started from my home directory and created a directory for the Docker project.

```bash
pwd
mkdir my-docker-image
cd my-docker-image/
```

### Terminal output

```console
skalyanapu@docker-ubuntu:~$ pwd
/home/skalyanapu
skalyanapu@docker-ubuntu:~$ mkdir my-docker-image
skalyanapu@docker-ubuntu:~$ cd my-docker-image/
skalyanapu@docker-ubuntu:~/my-docker-image$
```

At this point, all files needed for the image will be created inside `~/my-docker-image`.

---

## 🟦 Step 2 — Create the Web Page

I created a simple `index.html` file directly from the terminal.

```bash
cat > index.html <<'EOF'
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My First Docker Image</title>
</head>
<body>
    <h1>Hello from Docker!</h1>
    <p>This page is running from my first custom Docker image.</p>
</body>
</html>
EOF
```

I verified the file before building the image:

```bash
cat index.html
```

### Terminal verification

```console
skalyanapu@docker-ubuntu:~/my-docker-image$ cat index.html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My First Docker Image</title>
</head>
<body>
    <h1>Hello from Docker!</h1>
    <p>This page is running from my first custom Docker image.</p>
</body>
</html>
```

---

## 🟦 Step 3 — Create the Dockerfile

A `Dockerfile` normally has **no file extension**. It contains the instructions Docker uses to build the image.

```bash
cat > Dockerfile <<'EOF'
FROM nginx:alpine

COPY index.html /usr/share/nginx/html/index.html

EXPOSE 80
EOF
```

Verify it:

```bash
cat Dockerfile
```

### Dockerfile

```dockerfile
FROM nginx:alpine

COPY index.html /usr/share/nginx/html/index.html

EXPOSE 80
```

### What each instruction means

| Instruction | Purpose |
|---|---|
| `FROM nginx:alpine` | Uses the official lightweight NGINX Alpine image as the base image. |
| `COPY index.html /usr/share/nginx/html/index.html` | Copies my local web page into NGINX's default web root inside the image. |
| `EXPOSE 80` | Documents that the containerized application listens on TCP port `80`. |

> [!NOTE]
> `EXPOSE 80` does **not** publish port 80 to the host by itself. The actual host-to-container mapping is created later with `docker run -p 8080:80`.

---

## 🟦 Step 4 — Build the Docker Image

I built the image with:

```bash
docker build -t my-first-web:v1 .
```

### Command breakdown

```text
docker build -t my-first-web:v1 .
│      │     │  │            │  └─ Build context: current directory
│      │     │  │            └──── Image tag: v1
│      │     │  └───────────────── Image/repository name: my-first-web
│      │     └──────────────────── Tag option
│      └────────────────────────── Build an image
└──────────────────────────────── Docker CLI
```

### What does `-t` mean?

`-t` means **tag**. It gives the image a readable name and optional tag during the build.

```text
my-first-web:v1
│            │
│            └─ tag
└────────────── repository/image name
```

### What is `v1`?

`v1` is the tag I chose for this build. I am using it as a version label meaning **version 1**.

Docker tags are references used to distinguish image versions, for example:

```text
my-first-web:v1
my-first-web:v2
my-first-web:latest
```

### Why is the final `.` important?

The final dot means:

```text
Use the current directory as the Docker build context.
```

Because I ran the command from `~/my-docker-image`, Docker could access the files needed by the build:

```text
my-docker-image/
├── Dockerfile
└── index.html
```

> [!NOTE]
> During the build, Docker uses `nginx:alpine` as the base image. If that base image is not already available locally, Docker can retrieve it as part of the build process.

### 🟢 Actual build result

The build completed successfully:

<details>
<summary><strong>Show terminal build output</strong></summary>

```console
skalyanapu@docker-ubuntu:~/my-docker-image$ docker build -t my-first-web:v1 .
[+] Building 0.6s (7/7) FINISHED docker:default
 => [internal] load build definition from Dockerfile 0.0s
 => => transferring dockerfile: 116B 0.0s
 => [internal] load metadata for docker.io/library/nginx:alpine 0.0s
 => [internal] load .dockerignore 0.0s
 => => transferring context: 2B 0.0s
 => [internal] load build context 0.0s
 => => transferring context: 352B 0.0s
 => [1/2] FROM docker.io/library/nginx:alpine@sha256:4a73073bd557c65b759505da037898b61f1be6cbcc3c2c3aeac22d2a470c1752 0.1s
 => => resolve docker.io/library/nginx:alpine@sha256:4a73073bd557c65b759505da037898b61f1be6cbcc3c2c3aeac22d2a470c1752 0.0s
 => [2/2] COPY index.html /usr/share/nginx/html/index.html 0.0s
 => exporting to image 0.2s
 => => exporting layers 0.1s
 => => exporting manifest sha256:2891fde3b0329a810daed7126b3150d87e7f308950bb3df027cc0e78133bad01 0.0s
 => => exporting config sha256:94485ac4f49c4be7f8d7b207e9817f3e102e961367f3c9762cbf867258747171 0.0s
 => => exporting attestation manifest sha256:5bf010707cadbfdbe884253f0876ba8d3708272d7cc78892f7c70ae26aac4b9a 0.0s
 => => exporting manifest list sha256:57b27cde3b5b89ffb0136d75a8eca4a17e0e932adc04343467f0c6cf24d0f8bb 0.0s
 => => naming to docker.io/library/my-first-web:v1 0.0s
 => => unpacking to docker.io/library/my-first-web:v1
```

</details>

The key confirmation is:

```text
[+] Building ... FINISHED
...
naming to docker.io/library/my-first-web:v1
```

---

## 🟦 Step 5 — Verify the Image

After the build, I checked the images stored locally:

```bash
docker image ls
```

### 🟢 Terminal verification

```console
skalyanapu@docker-ubuntu:~/my-docker-image$ docker image ls
IMAGE                ID             DISK USAGE   CONTENT SIZE   EXTRA
hello-world:latest   5dd0d3e6e255       22.6kB         10.3kB    U
my-first-web:v1      57b27cde3b5b         92MB           26MB
nginx:alpine         4a73073bd557       92.8MB         26.9MB
```

My custom image is present:

```text
my-first-web:v1
```

---

## 🟦 Step 6 — Run a Container from the Custom Image

I started a container in detached mode:

```bash
docker run -d \
  --name my-first-web-container \
  -p 8080:80 \
  my-first-web:v1
```

### Command breakdown

| Option | Meaning |
|---|---|
| `docker run` | Creates and starts a new container from an image. |
| `-d` | Runs the container in detached/background mode. |
| `--name my-first-web-container` | Assigns a readable name to the container. |
| `-p 8080:80` | Maps host port `8080` to container port `80`. |
| `my-first-web:v1` | Specifies the image used to create the container. |

Port mapping:

```text
Host                    Container
localhost:8080   ─────►  port 80 / NGINX
```

### 🟢 Terminal result

```console
skalyanapu@docker-ubuntu:~/my-docker-image$ docker run -d \
  --name my-first-web-container \
  -p 8080:80 \
  my-first-web:v1
f04c4bc6b34782fe8a299a92cba4e111e7363876220774d89271a627dd03f41c
```

Docker returned the container ID, confirming that the container was created and started.

---

## 🟦 Step 7 — Verify the Running Container

I checked the running containers:

```bash
docker ps
```

### 🟢 Terminal verification

```console
skalyanapu@docker-ubuntu:~/my-docker-image$ docker ps
CONTAINER ID   IMAGE             COMMAND                  CREATED          STATUS          PORTS                                     NAMES
f04c4bc6b347   my-first-web:v1   "/docker-entrypoint.…"   20 seconds ago   Up 19 seconds   0.0.0.0:8080->80/tcp, [::]:8080->80/tcp   my-first-web-container
```

The important fields are:

```text
IMAGE    : my-first-web:v1
STATUS   : Up
PORTS    : 8080 -> 80/tcp
NAME     : my-first-web-container
```

---

## 🟦 Step 8 — Test the Website

Finally, I tested the running container directly from the Ubuntu terminal:

```bash
curl http://localhost:8080
```

### 🟢 Actual response

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My First Docker Image</title>
</head>
<body>
    <h1>Hello from Docker!</h1>
    <p>This page is running from my first custom Docker image.</p>
</body>
</html>
```

This confirmed the complete path was working:

```text
index.html
    ↓
Docker image
    ↓
NGINX container
    ↓
host port 8080
    ↓
curl response ✅
```

---

## 🟦 Step 9 — Understanding Docker Image Tags

Docker image references commonly follow this structure:

```text
[REGISTRY/]NAMESPACE/REPOSITORY[:TAG]
```

Examples:

```text
nginx:alpine
ubuntu:24.04
my-first-web:v1
myusername/my-first-web:v1
```

For my image:

```text
my-first-web:v1
```

- `my-first-web` is the repository/image name.
- `v1` is the tag.

To create another tag that points to the same image:

```bash
docker tag my-first-web:v1 my-first-web:latest
```

Then verify the tags with:

```bash
docker image ls
```

A typical result would look like:

```text
REPOSITORY       TAG       IMAGE ID
my-first-web     v1        abc123
my-first-web     latest    abc123
```

> [!TIP]
> A Docker tag is a **reference to an image**. Creating another tag does not necessarily duplicate all of the image data.

> [!NOTE]
> The `docker tag ... latest` command above is included as the next tagging step from my notes. My captured terminal output verifies the build/run/test flow; it does not include a terminal result proving that this additional tag command was executed.

---

## 🧪 Practical Verification Summary

| Check | Command | Result |
|---|---|---|
| Build custom image | `docker build -t my-first-web:v1 .` | ✅ Passed |
| Confirm image exists | `docker image ls` | ✅ Passed |
| Start container | `docker run -d --name ... -p 8080:80 ...` | ✅ Passed |
| Confirm container is running | `docker ps` | ✅ Passed |
| Test NGINX page | `curl http://localhost:8080` | ✅ Passed |

---

## 🧠 Key Takeaways from This Lab

1. A `Dockerfile` describes how a custom image should be built.
2. `FROM nginx:alpine` gives the image an existing NGINX base layer.
3. `COPY` places my application content inside the image.
4. `docker build -t name:tag .` builds and names the image.
5. The final `.` supplies the current directory as the build context.
6. `docker run -p HOST:CONTAINER` publishes the container service through a host port.
7. `docker ps` verifies that the container is actually running.
8. `curl` gives a simple end-to-end test of the application.
9. Docker tags such as `v1` and `latest` are references used to identify image versions.

---

## 📁 Final Project Structure

```text
my-docker-image/
├── Dockerfile
└── index.html
```

---

## ✅ Final Result

I successfully went from a local HTML file to a custom Docker image and a running NGINX container:

```text
Create files
   ↓
Write Dockerfile
   ↓
Build my-first-web:v1
   ↓
Run my-first-web-container
   ↓
Map 8080:80
   ↓
Verify with docker ps
   ↓
Test with curl
   ↓
Hello from Docker! 🐳
```

---

### GitHub Markdown formatting used in this guide

This file uses GitHub-friendly formatting for readability:

- `bash` fenced blocks for shell commands
- `console` blocks for captured terminal output
- `dockerfile` blocks for Dockerfile syntax highlighting
- `html` blocks for webpage content
- GitHub callouts such as **NOTE**, **TIP**, and **IMPORTANT**
- Tables for command explanations and test results
- Collapsible `<details>` sections for long terminal output
