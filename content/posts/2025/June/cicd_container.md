+++
date = '2025-06-26T19:34:09Z'
draft = false
title = 'DevOps study app: Containers'
author = 'Moriel Mauni'
tags = [" "]
categories = [" "]
description = " "
+++

### Intro

We going to take the Python app we have in ```/src/``` and upload it to Docker to make it a Docker image.

### Example: 

We going to test everything with a demo project:
/src/
```bash 
uv init demo
```

### Containerized demo:

src/demo/Dockerfile:
```dockerfile 
FROM python:latest
WORKDIR /app
COPY . /app
CMD ["python", "main.py"]
```

- Taking the latest Python image that is in docker-hub.
- The working directory is /app
- We coping all the content in ```.``` which is  ```/src/demo``` into ```/app```. 
- Run the command ```python main.py```.

#### Build:

```bash 
docker build -t demo:01 .
```

Output:
```bash 
❯ docker build -t demo:01 .
[+] Building 54.3s (8/8) FINISHED                                          docker:default
 => [internal] load build definition from Dockerfile                                 0.1s
 => => transferring dockerfile: 107B                                                 0.0s
 => [internal] load metadata for docker.io/library/python:latest                     7.9s
 => [internal] load .dockerignore                                                    0.0s
 => => transferring context: 2B                                                      0.0s
 => [1/3] FROM docker.io/library/python:latest@sha256:5f69d22a88dd4cc4ee1576def19a  44.9s
 => => resolve docker.io/library/python:latest@sha256:5f69d22a88dd4cc4ee1576def19ae  0.0s
 => => sha256:0c01110621e0ec1eded421406c9f117f7ae5486c8f7b0a0d1a3 48.49MB / 48.49MB  8.3s
 => => sha256:b1b8a0660a31403a35d70b276c3c86b1200b8683e83cd77a92 64.40MB / 64.40MB  30.9s
 => => sha256:3b29f43b7fff1f92cbea7613c52afd7725af4fec4862126311438 6.32kB / 6.32kB  0.0s
 => => sha256:3b1eb73e993990490aa137c00e60ff4ca9d1715bafb8e888dbb 24.02MB / 24.02MB  5.9s
 => => sha256:5f69d22a88dd4cc4ee1576def19aef48c8faa1b566054c4429118 9.72kB / 9.72kB  0.0s
 => => sha256:1ae8a7a2503de66cd1398659363f8ea92db4a634bffab4dfb074a 2.32kB / 2.32kB  0.0s
 => => sha256:48b8862a18fa961ebfbac8484877dd4894e96ee88177d8c4 211.37MB / 211.37MB  37.5s
 => => extracting sha256:0c01110621e0ec1eded421406c9f117f7ae5486c8f7b0a0d1a37cc7bc9  1.3s
 => => sha256:20d0b4e6a2e6295e295119126082b4f39882f50278611e48580e 6.16MB / 6.16MB  10.1s
 => => extracting sha256:3b1eb73e993990490aa137c00e60ff4ca9d1715bafb8e888dbb0986275  0.4s
 => => sha256:77a6ac598bc154025b4b2e393a3ca959116e0d8af9c31659a8 27.39MB / 27.39MB  20.6s
 => => sha256:5cc4a19fbac0d0fb7423535182443188713730a05b7ab1104f211605 250B / 250B  26.3s
 => => extracting sha256:b1b8a0660a31403a35d70b276c3c86b1200b8683e83cd77a92ec987440  1.9s
 => => extracting sha256:48b8862a18fa961ebfbac8484877dd4894e96ee88177d8c4f1f54d9727  5.0s
 => => extracting sha256:20d0b4e6a2e6295e295119126082b4f39882f50278611e48580e8fbef7  0.2s
 => => extracting sha256:77a6ac598bc154025b4b2e393a3ca959116e0d8af9c31659a857ac05ab  0.6s
 => => extracting sha256:5cc4a19fbac0d0fb7423535182443188713730a05b7ab1104f21160554  0.0s
 => [internal] load build context                                                    0.1s
 => => transferring context: 527B                                                    0.0s
 => [2/3] WORKDIR /app                                                               1.1s
 => [3/3] COPY . /app                                                                0.1s
 => exporting to image                                                               0.1s
 => => exporting layers                                                              0.1s
 => => writing image sha256:80d267af8e21f5f8f01dc8fe411c48a3a4c1d1c1ef17875e38981ca  0.0s
 => => naming to docker.io/library/demo:01      
```

Now, we can see the docker images list with:
```bash 
docker image ls
```

Output:
```bash
❯ docker image ls
REPOSITORY   TAG       IMAGE ID       CREATED          SIZE
demo         01        80d267af8e21   54 seconds ago   1.02GB
```

To run the container:
```bash 
docker run demo:01
```

Output:
```bash
❯ docker run demo:01
Hello from demo!
This is a demo
```

We can get more information about the image with:
```bash 
docker image inspect <image-name>
```

```bash 
docker history <image-name>
```

#### Add layers: 

```dockerfile 
FROM python:latest
WORKDIR /app

# Add some silly layers
RUN touch file1.txt
RUN echo "hello" > file2.txt
RUN mkdir -p /app/test

COPY . /app

CMD ["python", "main.py"]
```

## Containerized the back-end

#### Dockerfile

Create a new Dockerfile under ```/src/backend```
```dockerfile 
FROM python:latest
COPY --from=ghcr.io/astral-sh/uv:latest /uv /uvx /bin/

WORKDIR /app

COPY . /app

RUN uv sync --locked --no-editable

CMD ["uv", "run", "study-tracker-api"]
```

- We are copying from ```ghcr.io/astral-sh/uv:latest``` into ```/bin```, ```/uv```, and ```/uvx```.
- Work directory is ```app```.
- Copying everything from ```/src/backend``` into ```app```.
- Terminal will run ```uv run study-tracker-api```.

#### Tag and build
We going to run 2 commends at once.
```bash 
TAG=00 && docker build -t backend:$TAG .
```

- Making a variable for the "TAG" so we can change it every build.
- The build command.
```bash
docker image ls
REPOSITORY   TAG       IMAGE ID       CREATED             SIZE
backend      00        3c963e717333   About an hour ago   1.09GB
```

#### Trivy

[Trivy](https://trivy.dev/latest/docs/): The all in one open source security scanner, find vulnerabilities (CVE) & miscommunications (IaC) across repositories, containers images, Kubernetes clusters, and more.

Install:
```bash 
mise use trivy
```

Use Trivy:
```bash 
TAG=00 && trivy image --format table --severity CRITICAL,HIGH backend:$TAG
```

- "--format table": the format of the data.
- "--severity CRITICAL, HIGH": what CVEs we want to see.
This is just a little bit, there are a lot more and it's like this most of the time.
```bash
Legend:                                                                         
- '-': Not scanned                                                              
- '0': Clean (no security findings detected)                                    
backend:00 (debian 12.11)                                                                                                                                        
Total: 178 (HIGH: 169, CRITICAL: 9)                                                                                                                              
│      Library       │ Vulnerability  │ Severity │    Status    │    Installed Version    │ Fixed Version │                            Title                             │                
├────────────────────┼────────────────┼──────────┼──────────────┼─────────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤                
│ icu-devtools       │ CVE-2025-5222  │ HIGH     │ affected     │ 72.1-3                  │               │ icu: Stack buffer overflow in the SRBRoot::addTag function   │                
│                    │                │          │              │                         │               │ https://avd.aquasec.com/nvd/cve-2025-5222                    │
├────────────────────┼────────────────┼──────────┤              ├─────────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ libaom3            │ CVE-2023-6879  │ CRITICAL │              │ 3.6.0-1+deb12u1         │               │ aom: heap-buffer-overflow on frame size change               │
│                    │                │          │              │                         │               │ https://avd.aquasec.com/nvd/cve-2023-6879                    │
│                    ├────────────────┼──────────┼──────────────┤                         ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2023-39616 │ HIGH     │ will_not_fix │                         │               │ AOMedia v3.0.0 to v3.5.0 was discovered to contain an        │
│                    │                │          │              │                         │               │ invalid read mem...                                          │
│                    │                │          │              │                         │               │ https://avd.aquasec.com/nvd/cve-2023-39616                   │
```


### Optimizing the image

##### Starting size: 1.09GB

Making the image we copying more specific: version, and slim variant.
/src/backend/Dockerfile
```dockerfile 
FROM python:3.13-slim
WORKDIR /app
COPY . /app
CMD ["python", "main.py"]
```

##### After version specific: 190MB

We can see that even the Trivy report is better:

#### Other tag options
- [alpineLinux](https://www.alpinelinux.org/).
- [chainguard](https://www.chainguard.dev/)- not fully free.

#####  **NOTE**: with alpine tag I got 115MB.

### Implement caching & multi-stage builds

/src/backend/Dockerfile:
```dockerfile 
FROM python:3.13-alpine AS builder


# Install uv
COPY --from=ghcr.io/astral-sh/uv:latest /uv /uvx /bin/

# Change the working directory to the `app` directory
WORKDIR /app

# Install dependencies in a cache layer. This speeds up build time
RUN --mount=type=cache,target=/root/.cache/uv \
  --mount=type=bind,source=uv.lock,target=uv.lock \
  --mount=type=bind,source=pyproject.toml,target=pyproject.toml \
  uv sync --locked --no-install-project --no-editable

# Copy the project into the intermediate image
COPY . /app

# Sync the project and install it, now that we have access to the source code
RUN --mount=type=cache,target=/root/.cache/uv \
  uv sync --locked --no-editable

FROM python:3.13-alpine

# Copy the environment, but not the source code
COPY --from=builder --chown=app:app /app/.venv /app/.venv

# Run the application
CMD ["/app/.venv/bin/study-tracker-api"]
```


- ``` ```:
- ```FROM python:3.13-alpine AS builder```: making the image as small as possible.
	-  ```AS builder```:  **multi-stage build**, which helps create smaller final images.
- ```--mount=type=cache,target=/root/.cache/uv \``` : Installing dependencies in a cached layer, and when it's update it don't need to pull all the dependencies again --> faster rebuild.
- ```--mount=type=bind,source=uv.lock,target=uv.lock \```: mounting files from local file system to the builder stage.
- ```--mount=type=bind,source=pyproject.toml,target=pyproject.toml \```: mounting files from local file system to the builder stage.
- ```uv sync --locked --no-install-project --no-editable```: install all the dependencies in the cache layer. 
- ```COPY --from=builder --chown=app:app /app/.venv /app/.venv```: 


##### Now the image is 56.4MB


### Securing the image

The securing going to be through creating a non-root user and using him instead of root. 

/src/backend/Dockerfile
```dockerfile 
FROM python:3.13-alpine AS builder

COPY --from=ghcr.io/astral-sh/uv:latest /uv /uvx /bin/

# Change the working directory to the `app` directory
WORKDIR /app

# Install dependencies in a cache layer. This speeds up build time
RUN --mount=type=cache,target=/root/.cache/uv \
  --mount=type=bind,source=uv.lock,target=uv.lock \
  --mount=type=bind,source=pyproject.toml,target=pyproject.toml \
  uv sync --locked --no-install-project --no-editable

# Copy the project into the intermediate image
COPY . /app

# Sync the project and install it, now that we have access to the source code
RUN --mount=type=cache,target=/root/.cache/uv \
  uv sync --locked --no-editable

FROM python:3.13-alpine

# Create a non-root user and group with specific IDs
RUN addgroup -S -g 1000 app && adduser -S -u 1000 -G app app

# Copy the environment, but not the source code
COPY --from=builder --chown=app:app /app/.venv /app/.venv

# Switch to the non-root user
USER app

# Expose the correct port
EXPOSE 22112

# Run the application
CMD ["/app/.venv/bin/study-tracker-api"]
```

- ```RUN addgroup -S -g 1000 app && adduser -S -u 1000 -G app app```: creating a non-root user. 
- ```USER app```: switching to the user we created above.
- ```EXPOSE 22112```: exposing the right port.

## Containerized the front-end

#### Dockerfile

Create a new Dockerfile under ```/src/backend```
```dockerfile 
FROM python:latest
COPY --from=ghcr.io/astral-sh/uv:latest /uv /uvx /bin/

WORKDIR /app

COPY . /app

RUN uv sync --locked --no-editable

CMD ["uv", "run", "study-tracker-fronend"]
```

#### Tag and build
We going to run 2 commends at once.
```bash 
TAG=00 && docker build -t frontend:$TAG .
```

##### Size: 1.07GB

Making the image we copying more specific: version, and slim variant.
/src/frontend/Dockerfile
```dockerfile 
FROM python:3.13-slim
WORKDIR /app
COPY . /app
CMD ["python", "main.py"]
```

##### Size: 128MB

### Implement caching & multi-stage builds

/src/frontend/Dockerfile:
```dockerfile 
FROM python:3.13-alpine AS builder


# Install uv
COPY --from=ghcr.io/astral-sh/uv:latest /uv /uvx /bin/

# Change the working directory to the `app` directory
WORKDIR /app

# Install dependencies in a cache layer. This speeds up build time
RUN --mount=type=cache,target=/root/.cache/uv \
  --mount=type=bind,source=uv.lock,target=uv.lock \
  --mount=type=bind,source=pyproject.toml,target=pyproject.toml \
  uv sync --locked --no-install-project --no-editable

# Copy the project into the intermediate image
COPY . /app

# Sync the project and install it, now that we have access to the source code
RUN --mount=type=cache,target=/root/.cache/uv \
  uv sync --locked --no-editable

FROM python:3.13-alpine

# Copy the environment, but not the source code
COPY --from=builder --chown=app:app /app/.venv /app/.venv

# Run the application
CMD ["/app/.venv/bin/study-tracker-frontend"]
```

##### Size: 49.2MB


### Securing the image

The securing going to be through creating a non-root user and using him instead of root. 

/src/frontend/Dockerfile
```
FROM python:3.13-alpine AS builder

COPY --from=ghcr.io/astral-sh/uv:latest /uv /uvx /bin/

# Change the working directory to the `app` directory
WORKDIR /app

# Install dependencies in a cache layer. This speeds up build time
RUN --mount=type=cache,target=/root/.cache/uv \
  --mount=type=bind,source=uv.lock,target=uv.lock \
  --mount=type=bind,source=pyproject.toml,target=pyproject.toml \
  uv sync --locked --no-install-project --no-editable

# Copy the project into the intermediate image
COPY . /app

# Sync the project and install it, now that we have access to the source code
RUN --mount=type=cache,target=/root/.cache/uv \
  uv sync --locked --no-editable

FROM python:3.13-alpine

# Create a non-root user and group with specific IDs
RUN addgroup -S -g 1000 app && adduser -S -u 1000 -G app app

# Copy the environment, but not the source code
COPY --from=builder --chown=app:app /app/.venv /app/.venv

# Switch to the non-root user
USER app

# Expose the correct port
EXPOSE 22111cd 

# Run the application
CMD ["/app/.venv/bin/study-tracker-frontend"]
```

