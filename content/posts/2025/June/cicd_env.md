+++
date = '2025-06-22T22:10:18Z'
draft = false
title = 'DevOps study app'
author = 'Moriel Mauni'
tags = [" "]
categories = [" "]
description = " "
+++

My GitHub repository for the project: https://github.com/MorielMauni/devops-study-app 

## Setting up the DevPod

### Make a GitHub repository

I will name main as **devops-study-app** and this will be the root directory as well. 
### .devcontainer/devcontainer.json

- Building a Dockerfile and after the creation going to run "scripts/setup". 
- We install the "Docker in Docker" feature.
- Open ports 22111, 22112

```
{
  "build": {
    "context": "..",
    "dockerfile": "Dockerfile"
  },
  "postCreateCommand": "scripts/setup",
  "features": {
    "ghcr.io/devcontainers/features/docker-in-docker:2": {}
  },
  "forwardPorts": [
    "22111",
    "22112"
  ]
}
```

### .devcontainer/Dockerfile

- Base image of Ubuntu
- installing mise
```
FROM mcr.microsoft.com/devcontainers/base:ubuntu-24.04

COPY --from=jdxcode/mise /usr/local/bin/mise /usr/local/bin/

RUN echo 'eval "$(mise activate bash)"' >> /home/vscode/.bashrc && \
  echo 'eval "$(mise activate zsh)"' >> /home/vscode/.zshrc
```

### scripts/setup
```
mkdir scripts
nvim scripts/setup
```

```
echo "This is a placeholder for the script to run"
```

```
chmod +x setup
```

Run in the root directory : 
```
devpod up . 
```

### Installing with mise
```
mise use python@3.13
```

We now have a mise.toml file and we can add it this:
```
[tools]
python = "3.13"
uv = "latest"

[settings]
python.uv_venv_auto = true
idiomatic_version_file_enable_tools = ["python"]
experimental = true

[env]
APP_REPO = '<GitHub-repository-name>'
GITOPS_REPO = '<GitOps-repository-name>'
```

In this we:
mise settings:
- Adding uv (Python package) to the container.
- python.uv_venv_auto = true : when running uv inside the project it detect the virtual environment and activate it.
-  Work with:
	- idiomatic_version_file_enable_tools = ["python"]
	- experimental = true


#### Trust mise & install using the setup script

In scripts/setup, edit the file to contain this: 
```
#!/bin/bash

/usr/local/bin/mise trust /workspaces/"$DEVPOD_WORKSPACE_ID"/mise.toml && /usr/local/bin/mise install
```

now run:
```
./scripts/setup
```

Now all the apps, settings and environments are up with the changes we made.

### Install Commitizan and pre-commit

**Commitizan**: A tool that's help to create commit messages. [Commitizan](https://commitizen-tools.github.io/commitizen/)  helps to write good commits. 
**pre-commit**: A framework for managing and maintaining multi-language pre-commit hooks. [pre-commit](https://pre-commit.com/) .

Add this to mise.toml
```
[hooks]
enter = "bash ./scripts/setup_project"
```

- triggered when entering to the project

scripts/setup_project
```
#!/bin/bash

if ! command -v cz >/dev/null && [ "$DEVPOD" = "true" ]; then
  git config --global push.autoSetupRemote true
  git config --global --add safe.directory /workspaces/$DEVPOD_WORKSPACE_ID
  pip install --user pipx
  pipx install commitizen
  pre-commit install
  pre-commit install --hook-type commit-msg
fi
```

-  Checks if Commitizan is  not installed and we inside a DevPod, it runs:
	-  Setup git configs 
	- Install pipx
	- Install Commitizan
	- Install pre-commit hooks

#### Insallation

```
mise use pre-commit
```

settings up the pre-commit hook:

.pre-commit-config.yaml
```
repos:
  - repo: https://github.com/commitizen-tools/commitizen
    rev: v1.17.0
    hooks:
      - id: commitizen
        stages: [commit-msg]
```

Now if we exit the DevPod and ssh back in, it going to install all the configuration we did:
```bash 
$ devpod ssh
Collecting pipx
  Downloading pipx-1.7.1-py3-none-any.whl.metadata (18 kB)
Collecting argcomplete>=1.9.4 (from pipx)
  Downloading argcomplete-3.6.2-py3-none-any.whl.metadata (16 kB)
Collecting packaging>=20 (from pipx)
  Downloading packaging-25.0-py3-none-any.whl.metadata (3.3 kB)
Collecting platformdirs>=2.1 (from pipx)
  Downloading platformdirs-4.3.8-py3-none-any.whl.metadata (12 kB)
Collecting userpath!=1.9,>=1.6 (from pipx)
  Downloading userpath-1.9.2-py3-none-any.whl.metadata (3.0 kB)
Collecting click (from userpath!=1.9,>=1.6->pipx)
  Downloading click-8.2.1-py3-none-any.whl.metadata (2.5 kB)
Downloading pipx-1.7.1-py3-none-any.whl (78 kB)
Downloading argcomplete-3.6.2-py3-none-any.whl (43 kB)
Downloading packaging-25.0-py3-none-any.whl (66 kB)
Downloading platformdirs-4.3.8-py3-none-any.whl (18 kB)
Downloading userpath-1.9.2-py3-none-any.whl (9.1 kB)
Downloading click-8.2.1-py3-none-any.whl (102 kB)
Installing collected packages: platformdirs, packaging, click, argcomplete, userpath, pipx
Successfully installed argcomplete-3.6.2 click-8.2.1 packaging-25.0 pipx-1.7.1 platformdirs-4.3.8 userpath-1.9.2

[notice] A new release of pip is available: 24.3.1 -> 25.1.1
[notice] To update, run: pip install --upgrade pip
  installed package commitizen 4.8.3, installed using Python 3.13.5
  These apps are now globally available
    - cz
    - git-cz
done! ✨ 🌟 ✨
pre-commit installed at .git/hooks/pre-commit
pre-commit installed at .git/hooks/commit-msg
```

### First commit

now when we 
```
git commit -m "first commit"
```

Commitizen will activate and check the commit if it's valid:
```bash 
git commit -m "first commit"                                                            
[INFO] Initializing environment for https://github.com/commitizen-tools/commitizen.       
[INFO] Installing environment for https://github.com/commitizen-tools/commitizen.         
[INFO] Once installed this environment will be reused.                                    
[INFO] This may take a few minutes...                                                     
commitizen check.........................................................Failed           
- hook id: commitizen                                                                     
- exit code: 14                                                                  
commit validation: failed!                                                                
please enter a commit message in the commitizen format.                                   
commit: first commit                                                             

pattern: (build|ci|docs|feat|fix|perf|refactor|style|test|chore|revert|bump)(\(\S+\))?:\s.
*   
```

So, we can use cz to commit a message:
```
cz commit
```

Follow the QA and then it will commit it.

``` bash
cz commit                                                                               
? Select the type of change you are committing feat: A new feature. Correlates with MINOR 
in SemVer                                                                                 
? What is the scope of this change? (class or file name): (press [enter] to skip)         
 Adding the files, first commit                                                           
? Write a short and imperative summary of the code changes: (lower case and no period)    
 Adding the first files, first commit                                                     
? Provide additional contextual information about the code changes: (press [enter] to skip

? Is this a BREAKING CHANGE? Correlates with MAJOR in SemVer No                           
? Footer. Information about Breaking Changes and reference issues that this commit closes:


feat(Adding-the-files,-first-commit): Adding the first files, first commit

commitizen check.........................................................Passed

[master (root-commit) c38633d] feat(Adding-the-files,-first-commit): Adding the first file
s, first commit
 7 files changed, 53 insertions(+)
 create mode 100644 .devcontainer/.devcontainer.json
 create mode 100644 .devcontainer/Dockerfile
 create mode 100644 .devcontainer/devcontainer.json
 create mode 100644 .pre-commit-config.yaml
 create mode 100644 mise.toml
 create mode 100755 scripts/setup
 create mode 100755 scripts/setup_project

Commit successful!
```

## This is part 1/5, I will update as I got.
