+++
date = '2025-07-12T21:38:39Z'
draft = false
title = 'DevOps study app: GitHub Container Image'
author = 'Moriel Mauni'
tags = [" "]
categories = [" "]
description = " "
+++


### Building and pushing images

We switch to main and pull --> then let's create a new branch ```feat/add-docker-image-pushing```.

/.github/workflows/docker-build-push.yaml
```yaml 
name: Build and Push Docker Images
on:
  push:
    tags:
      - "backend*"
env:
  REGISTRY: ghcr.io
  BACKEND_IMAGE_NAME: ${{ github.repository_owner | toLower }}/study-app-api
jobs:
  build-and-push-backend:
    name: Build and Push Backend
    runs-on: ubuntu-latest
    # Only run for backend tags
    if: contains(github.ref, 'backend')
    permissions:
      contents: read
      packages: write
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Log in to the Container registry
        uses: docker/login-action@v3
        with:
          registry: ${{ env.REGISTRY }}
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}

      - name: Extract tag name
        id: tag
        run: echo "TAG=${GITHUB_REF#refs/tags/}" >> $GITHUB_OUTPUT

      - name: Build and push Backend Docker image
        uses: docker/build-push-action@v5
        with:
          context: ./src/backend
          push: true
          tags: |
            ${{ env.REGISTRY }}/${{ env.BACKEND_IMAGE_NAME }}:${{ steps.tag.outputs.TAG }}
            ${{ env.REGISTRY }}/${{ env.BACKEND_IMAGE_NAME }}:latest
    outputs:
      tag: ${{ steps.tag.outputs.TAG }}
      app: backend
```
#### Troubleshoot
I got this error: 
```
/usr/bin/docker buildx build --iidfile /home/runner/work/_temp/docker-actions-toolkit-YswvLo/build-iidfile-7f217b5f64.txt --tag ghcr.io/MorielMauni/study-app-api:backend-v1.0.0 --tag ghcr.io/MorielMauni/study-app-api:latest --metadata-file /home/runner/work/_temp/docker-actions-toolkit-YswvLo/build-metadata-cc96015c9c.json --push ./src/backend

ERROR: failed to build: invalid tag "ghcr.io/MorielMauni/study-app-api:backend-v1.0.0": repository name must be lowercase

Error: buildx failed with: ERROR: failed to build: invalid tag "ghcr.io/MorielMauni/study-app-api:backend-v1.0.0": repository name must be lowercase
```

Because my GitHub username is in Camel-Case and the GHCR must be only lower case.

I got a fix to the code posted there that do a workaround to make the username lowercase (first i tried to pipe 'toLower' but it didn't worked).

Workaround:
```yaml 
name: Build and Push Docker Images

on:
  push:
    tags:
      - "backend*"

env:
  REGISTRY: ghcr.io

jobs:
  build-and-push-backend:
    name: Build and Push Backend
    runs-on: ubuntu-latest
    if: contains(github.ref, 'backend')
    permissions:
      contents: read
      packages: write
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Log in to the Container registry
        uses: docker/login-action@v3
        with:
          registry: ${{ env.REGISTRY }}
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}

      - name: Set lowercase repo owner and extract tag
        id: vars
        run: |
          echo "REPO_OWNER_LC=${GITHUB_REPOSITORY_OWNER,,}" >> $GITHUB_ENV
          echo "TAG=${GITHUB_REF#refs/tags/}" >> $GITHUB_OUTPUT

      - name: Build and push Backend Docker image
        uses: docker/build-push-action@v5
        with:
          context: ./src/backend
          push: true
          tags: |
            ${{ env.REGISTRY }}/${{ env.REPO_OWNER_LC }}/study-app-api:${{ steps.vars.outputs.TAG }}
            ${{ env.REGISTRY }}/${{ env.REPO_OWNER_LC }}/study-app-api:latest
    outputs:
      tag: ${{ steps.vars.outputs.TAG }}
      app: backend
```

- When a new tag is pushed "backend*" it's triggering for the pipeline.
- environment variable:
	- GitHub Container registry.
	- Image name: Based on the owner of the repository and study-app-api.
- 1 job:
	- name: build and push backend.
	- image: Ubuntu latest.
	- If the github.ref contains 'backend' this next part going to be triggered:
		- Permissions:
			- read contents.
			- write packages.
		- steps:
			- checking the repository.
			- login in the container registry with:
				- registry.
				- username.
				- password: GitHub secret.
			- extracting the tag name and the output is going to a variable $GITHUB_OUTPUT
			- Build and push the /src/backend directory to Docker image.
		- outputs:
			- tag: 
			- app: the backend app.


![package](/images/cicd-docker.jpg)
