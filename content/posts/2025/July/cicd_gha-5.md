+++
date = '2025-07-13T21:44:06Z'
draft = false
title = 'DevOps Study app: GitHub Container Image- Part 2'
author = 'Moriel Mauni'
tags = [" "]
categories = [" "]
description = " "
+++

### Front-end + Back-end

We switch to main and pull --> then let's create a new branch ```feat/add-docker-image-frontend```.

#### Add a new package:

./release-please-config.json:

```json 
{
  "packages": {
    "src/backend": {
      "release-type": "python",
      "package-name": "study-tracker-backend",
      "component": "backend",
      "include-component-in-tag": true,
      "changelog-path": "CHANGELOG.md",
      "extra-files": [
        {
          "type": "toml",
          "path": "uv.lock",
          "jsonpath": "$.package[?(@.name.value=='study-tracker-backend')].version"
        }
      ]
    },
    "src/frontend": {
      "release-type": "python",
      "package-name": "study-tracker-frontend",
      "component": "frontend",
      "include-component-in-tag": true,
      "changelog-path": "CHANGELOG.md",
      "extra-files": [
        {
          "type": "toml",
          "path": "uv.lock",
          "jsonpath": "$.package[?(@.name.value=='study-tracker-frontend')].version"
        }
      ]
    }
  }
}
```

#### Edit the manifest

/.release-please-manifest.json
```json 
{
  "src/backend": "0.1.0",
  "src/frontend": "0.0.0"
}
```

#### Update the docker-build-push

./.github/workflows/docker-build-push.yaml
```yaml 
name: Build and Push Docker Images

on:
  push:
    tags:
      - "backend*"
      - "frontend*"

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

  build-and-push-frontend:
    name: Build and Push Frontend
    runs-on: ubuntu-latest
    if: contains(github.ref, 'frontend')
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

      - name: Build and push Frontend Docker image
        uses: docker/build-push-action@v5
        with:
          context: ./src/frontend
          push: true
          tags: |
            ${{ env.REGISTRY }}/${{ env.REPO_OWNER_LC }}/study-app-web:${{ steps.vars.outputs.TAG }}
            ${{ env.REGISTRY }}/${{ env.REPO_OWNER_LC }}/study-app-web:latest
    outputs:
      tag: ${{ steps.vars.outputs.TAG }}
      app: frontend
```

#### Checking everything is good

Let's ```cd src/frontend``` and then ```git status``` and  ```uv lock```. 
Now we going now ```uv sync``` and ```git status```. 

I added a comment on /src/frontend/main.py just to trigger the CI.

Let's add, commit, push, squash and merge.

![cicd-front](/images/cicd-frontend.jpg)
