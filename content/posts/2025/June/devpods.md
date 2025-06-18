+++
date = '2025-06-18T19:57:39Z'
draft = false
title = 'Developer enviornment'
author = 'Moriel Mauni'
tags = ["devpod", "container", "docker"]
categories = ["devpod", "container", "docker"]
description = "About Dev-containers and working enviorments"
+++

### Intro 

In the world of development, Docker often comes up, and the classic “It worked for me” discussions follow. We all know the power of Docker containers in testing, deploying code, and packaging applications.

### What are DevPods?

DevPods allow each developer environment to run in a **separate container**, specified through the `devcontainer.json` file. The containers are created **locally using Docker**.

One of the best features? DevPods come with a **beautiful GUI** that simplifies the process, and there's even a solid **VS Code extension** to manage them. But, as DevOps professionals, we tend to lean on the **CLI** for automation. Here’s why:

- Automating DevPod creation becomes much easier.
    
- We can quickly copy, delete, or change configurations (like swapping IDEs).
    
- **GUI is for the weak** – we thrive on efficiency with the CLI.

[DevPods: getting-started](https://devpod.sh/docs/getting-started)

### Install 

```bash 
curl -L -o devpod "https://github.com/loft-sh/devpod/releases/latest/download/devpod-linux-amd64" && sudo install -c -m 0755 devpod /usr/local/bin && rm -f devpod
```

If you use Arch (BTW), there is a simpler option:
```bash 
yay -S devpod-bin

```

let's verify the install with:
```bash 
devpod-cli --version

```

Also, I will make an alias for devpod-cli in my ~/.bashrc:
```bash 
alias devpod-cli='devpod'
```

Another great thing with DevPod, that you can get you environment into GitOps apps so you can work on it on every machine when you clone it.

### First use configurations

We got the list of providers for DePod:
```bash 
devpod provider list-available
```

We will get this output:
```
List of available providers from loft:
	aws
	azure
	civo
	digitalocean
```
There is no Docker? no problem, let's add it as a provider:
```bash 
devpod provider add docker
```
then we can see it in the providers list:
```
     NAME  | VERSION | DEFAULT | INITIALIZED |   DESCRIPTION     
  ---------+---------+---------+-------------+-------------------
    docker | v0.0.1  | true    | true        | DevPod on Docker  
  
```

Let's see the IDEs we can pick with:
```bash 
devpod ide list
```
The output:
```
         NAME       | DEFAULT  
  ------------------+----------
    clion           | false    
    codium          | false    
    cursor          | false    
    dataspell       | false    
    fleet           | false    
    goland          | false    
    intellij        | false    
    jupyternotebook | false    
    none            | false    
    openvscode      | false    
    phpstorm        | false    
    positron        | false    
    pycharm         | false    
    rider           | false    
    rstudio         | false    
    rubymine        | false    
    rustrover       | false    
    vscode          | false    
    vscode-insiders | false    
    webstorm        | false    
    zed             | false  
```

To pick a provider we going to use:
```bash 
devpod ise use <ide-name>
```

After picking an ide, the false going to change to true next to it.

### .devcontainer.json 

We going to configure the Dev container with devcontainer.json file:

Image lists: https://github.com/devcontainers/images/tree/main/src
```json 
{
  "image": "mcr.microsoft.com/devcontainers/base:ubuntu",
}
```

After setting the image, we can also add features and there is no limits.

[Features docs](https://containers.dev/features)

Can look like this:
```json 
{
  "image": "mcr.microsoft.com/devcontainers/base:ubuntu",
  "features": {
    "ghcr.io/devcontainers-extra/features/neovim-homebrew:1": {},
    "ghcr.io/rio/features/chezmoi": {}
  }
}
```

### Starting the devcontainer

To create the DevPod we run: (in the devcontainer directory)
```bash 
devpod up .
```

#### Useful flags 

If wanted there are a lot of flags you can use as well:
```bash 
--dotfiles https://github.com/MorielMauni/dotfiles.git
--ide none/vscode/openvscode/jupyternotebook
--recreate 
--debug 
```

#### Connect 

When it's successfully done, run and connect to the pod:
```bash 
devpod ssh
```

Pick the devpod you created (same name as the directory you worked on).
The ide of pick will be open, if you picked none just the terminal going to be connected to the devpod like a ssh connection.
