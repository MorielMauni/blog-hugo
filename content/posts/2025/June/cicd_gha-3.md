+++
date = '2025-07-08T20:16:52Z'
draft = false
title = 'DevOps Study app: GitHub Actions- Back-end Part 2'
author = 'Moriel Mauni'
tags = [" "]
categories = [" "]
description = " "
+++

We going to continue were we left and push the last adds, and on GitHub we going to see a new pull request that will run with an error:
```
Run marocchino/sticky-pull-request-comment@v2
Error: Resource not accessible by integration
```

Which mean it tries to write to the repository but don't have the permission for it.

Repository settings --> Actions --> General --> 
	--> Actions permissions -->  Allow all actions and reusable workflows.
	--> ## Workflow permissions --> Read and write permissions.

Now if we go to Actions --> The last job --> re-run jobs --> re-run failed jobs.

![GitHub-3](/images/cicd-testbackend.jpg)
![GitHub-4](/images/cicd-testbackend2.jpg)


Let's "Squash and merge" it.

### Developer dependencies

Let's switch to main and pull.
```bash
git switch main && git pull
```

Now if we will create a new image with:
```
TAG=05 && docker build -t backend:$TAG .
```

We can see the new image is 95 MB when the last one was 56 MB. 
Why have this is a bigger image?  because we added some dependencies.

If we will look in /src/backend/pyproject.toml we can see there are some dependencies we don't use any more:
```toml
dependencies = [
  "fastapi>=0.115.13",
  "httpx>=0.28.1",
  "pytest>=8.4.1", # We don't use anymore
  "pytest-asyncio>=1.0.0", # We don't use anymore
  "pytest-cov>=6.2.1", # We don't use anymore
  "ruff>=0.12.1", # We don't use anymore
  "uvicorn>=0.34.3",
]
```

We can add the dependencies as this:
```bash
uv add pytest pytest-asyncio pytest-cov ruff --dev
```

And the pyproject.toml will look like this:
```toml
[project]
name = "study-tracker-backend"
version = "0.0.0"
description = "Backend API for tracking study time for DevOps certifications"
readme = "README.md"
authors = [{ name = "Moriel Mauni", email = "morielmauni@gmail.com" }]
requires-python = ">=3.13"
dependencies = ["fastapi>=0.115.13", "httpx>=0.28.1", "uvicorn>=0.34.3"]

[project.scripts]
study-tracker-api = "backend.main:main"

[tool.pytest.ini_options]
asyncio_default_fixture_loop_scope = "function"

[tool.hatch.build.targets.wheel]
packages = ["src/backend"]

[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"

[dependency-groups]
dev = [
    "pytest>=8.4.1",
    "pytest-asyncio>=1.0.0",
    "pytest-cov>=6.2.1",
    "ruff>=0.12.2",
]
```

Now we need to edit the Dockerfile this line:
On creating the virtual environment:
```dockerfile
uv sync --locked --no-editable --no-dev
```

If we create a new image, we can see that the image is back to 56 MB. 

Let's:
```bash
docker compose up --build
```

### Scan with Trivy

We want Trivy to scan out image as part of the steps in /.github/workflows/backend-test.yaml

First: 
We need to build the docker image:
```yaml
- name: Build Docker image
  run: docker build -t backend-app:latest .
```

Scan with Trivy:
```yaml
- name: Run Trivy vulnerability scanner
  uses: aquasecurity/trivy-action@0.30.0
  with:
    image-ref: "backend-app:latest"
    format: "table"
    exit-code: "0"
    # exit-code: '1' # Fail the workflow if vulnerabilities are found.
    ignore-unfixed: true
    vuln-type: "os,library"
    severity: "CRITICAL,HIGH"
```

We going to commit, push, squash and merge.
