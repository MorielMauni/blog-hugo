+++
date = '2025-06-29T21:01:25Z'
draft = false
title = 'DevOps study app: GitHub Action'
author = 'Moriel Mauni'
tags = [" "]
categories = [" "]
description = " "
+++

### CI/CD 

#### CI
Continuous Integration --> is our code ready to deploy? --> linting, testing, and scanning.

#### CD
Continuous Deployment --> Deploy code as safely, quickly, and easily as possible --> GitOps.

### Tools:
1. Jenkins: using groovy.
2. GitLab: using YAML.
3. Azure DevOps: using YAML.
### Idea behind the tool

There is an event --> Trigger a script.  

### GitHub Actions
[GitHub Actions](https://docs.github.com/en/actions/get-started/quickstart) is a continuous integration and continuous delivery (CI/CD) platform that allows you to automate your build, test, and deployment pipeline. You can create workflows that build and test every pull request to your repository, or deploy merged pull requests to production.

You can configure a GitHub Actions **workflow** to be triggered when an **event** occurs in your repository, such as a pull request being opened or an issue being created. Your workflow contains one or more **jobs** which can run in sequential order or in parallel. Each job will run inside its own virtual machine **runner**, or inside a container, and has one or more **steps** that either run a script that you define or run an **action**, which is a reusable extension that can simplify your workflow.


### Creating docker-compose.yaml

Let's install docker-compose:
```
mise use docker-compose
```

/docker-compose.yaml
```yaml 
services:
  backend:
    build:
      context: ./src/backend
      dockerfile: Dockerfile
    ports:
      - "22112:22112"
    volumes:
      - ./src/backend/data/:/app/data
    environment:
      - DATA_DIR=/app/data
    user: "1000:1000"

  frontend:
    build:
      context: ./src/frontend
      dockerfile: Dockerfile
    ports:
      - "22111:22111"
    environment:
      - API_URL=http://backend:22112
    depends_on:
      - backend
    user: "1000:1000"
```

- Defining 2 containers: backend and frontend.
- **Build**: tells Docker how to build the image:
	- **context**: The folder where Docker looks for the files to build the image.
	- **dockerfile**: use the dockerfile that's inside the folder in the context.
	- **ports**: exposes ports.
	- **volumes**: mounts a local folder ```./src/backend/data``` to ```/app/data``` inside the container.
	- **environment**: sets an environment variable in the container.
	- **user**: runs the container as UID and GID 1000 (user app we made).

### Git repository configuration

In the git repository:
--> Settings.
	--> [x] Automatically delete head branches.
--> Rules:
	-->  Rulesets:
		--> Add a new  rulesets.
			- Ruleset name: Main
			- Enforcement status: Active.
			- Bypass: if we still want to commit to main.
			- Target branches: default branch.
			- Rules:
				- [x] Require a pull request before merging
					- [ x] Request pull request review from Copilot

**NOW** no one can push to main, only with pull requests. 


### Linting Python with Ruff

[Ruff](https://github.com/astral-sh/ruff): An extremely fast Python linter and code formatter, written in Rust.

Install ruff:
```bash 
mise use ruff
```

And with UV is needed:
/src/backend
```bash 
uv add ruff
```

don't forget to: ```uv sync```

Let's run:
```bash 
ruff check
```

```bash
❯ ruff check
All checks passed!
```

We going to add this to the .pre-commit-config.yaml:
```yaml 
  - repo: https://github.com/astral-sh/ruff-pre-commit
    # Ruff version.
    rev: v0.12.1 # Check for your version with 'ruff --version'
    hooks:
      # Run the linter.
      - id: ruff
      # Run the formatter.
      - id: ruff-format
```

- ruff: check syntax.
- ruff-format: check formatting. 

### Creating a new branch

Creating a new branch and switching to him:
```bash 
git switch -c ci/add-ruff-linting
```

Now if we commit with an error, ruff will prevent it.
Example: made an error just to show, on the commit ruff can see the syntax error:
```bash
src/backend/src/backend/main.py:3:9: SyntaxError: Simple statements must be separated by newlines or semicolons
  |
1 | from fastapi import FastAPI, HTTPException, Query
2 | from fastapi.middleware.cors import CORSMiddleware
3 | immport uvicorn
  |         ^
4 | import logging
5 | from typing import List, Optional
  |

Found 1 error.

ruff format..............................................................Failed
- hook id: ruff-format
- exit code: 2

error: Failed to parse src/backend/src/backend/main.py:3:9: Simple statements must be separated by newlines or semicolons
```

Now, when I fixed the "error", the commit going through: 
```bash
ci(ruff): adding ruff to the ci

ruff (legacy alias)..................................(no files to check)Skipped
ruff format..........................................(no files to check)Skipped
commitizen check.........................................................Passed
ruff (legacy alias)..................................(no files to check)Skipped
ruff format..........................................(no files to check)Skipped

[ci/add-ruff-linting bf98c65] ci(ruff): adding ruff to the ci
 2 files changed, 10 insertions(+)
```

### GitHub Action

#### Creating the action:

First, we going to create a directory:
```
mkdir -p .github/workflows
```

And create backend-tests.yaml file --> a GitHub Action workflow file.
/.github/workflows/backend-tests.yaml
```yaml 
name: Backend Tests

on:
  pull_request:

jobs:
  check_paths:
    runs-on: ubuntu-latest
    outputs:
      should_run: ${{ steps.filter.outputs.backend }}
    steps:
      - uses: actions/checkout@v4
      - uses: dorny/paths-filter@v3.0.2
        id: filter
        with:
          filters: |
            backend:
              - 'src/backend/**'
              - '.github/workflows/backend-tests.yaml'

  test:
    name: Test Backend
    needs: check_paths
    if: ${{ needs.check_paths.outputs.should_run == 'true' }}
    runs-on: ubuntu-latest
    defaults:
      run:
        working-directory: ./src/backend

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Install UV
        uses: astral-sh/setup-uv@v5

      - name: Set up Python
        run: uv python install

      - name: Install dependencies
        run: |
          uv sync --locked --dev

      - name: Lint with Ruff
        run: |
          uv run ruff check --output-format=github --target-version=py313 src tests
          uv run ruff format --diff --check --target-version=py313 src tests
```

[GitHub Action Marketplace](https://github.com/marketplace):
	[Paths Changes Filter](https://github.com/marketplace/actions/paths-changes-filter).
	[Checkout V4](https://github.com/marketplace/actions/checkout).
	[astral-sh/setup-uv](https://github.com/marketplace/actions/astral-sh-setup-uv).


- **name**: just the name.
- **on**:  The trigger
	- **pull_request**: 
- **jobs**: every job made out of steps.
	- **name of the job**:
		- **runs-on**: what kind of container we want to use (ubuntu-latest most of times). 
		- **steps**:
		- **outputs**:
			- **should_run**: 

- **needs.check_paths.outputs.should_run == 'true'**: checking if "check_path" outputs "should_run" is true, everything going to be activate.
- **defaults**: make a default to run on everything
	- **run**:
		- **working-directory**: path/to/dir.
	
Now we pushed a new pull request.

We get this error in Test-backend:
```bash
Run uv run ruff check --output-format=github --target-version=py313 src tests
Error: tests:1:1: E902 No such file or directory (os error 2)
Error: Process completed with exit code 1.
```

![CI-1](/images/cicd-git-1.jpg)

We need to create a ```tests``` directory under ```/src/backend```. and ```touch tests/temp``` a file inside.
Now the CI will work good.

![CI-2](/images/cicd-git-2.jpg)

## I will continue more as I go :-) .
