+++
date = '2025-10-27T20:07:11Z'
draft = false
title = 'Ansible but in Windows'
author = 'Moriel Mauni'
tags = [" "]
categories = [" "]
description = " "
+++

### Controller

Ansible needs **Linux** as a controller (Control Node).

When Linux server wants to "talk" with Windows server it use WinRM (Remote connect to another server).

**WinRM**: management protocol used by Windows to remotely communicate with another server.

### Requirements

#### Linux

- Ansible installed: 2.9 above [Install Docs](https://docs.ansible.com/ansible/latest/installation_guide/installation_distros.html)
- Python3
- pywinrm

#### Windows

- PowerShell 3.0 above
- .NET 4.0 and above
- WinRM Listener should be active

#### Ansible on WSL

==NTA Project==

Switch to root

```
sudo su
```

Update + install Ansible

```
apt-get update && apt-get install ansible -y
```

Install an Ansible package

```
sudo apt install ansible-lint
```

Install pythin-winrm

```
sudo apt-get install python3-winrm
```

#### Install Chocolatey

==Most of the times it's already installed==
On the Control Machine (UNIX/WSL)

```
ansible-galaxy collection install chocolatey.chocolatey
```

When using the `win_chocolatey` Ansible module, it detects absence of Chocolatey and installs it automatically before managing packages.

[YouTube: Ansible for Windows](https://www.youtube.com/watch?v=73pgFXLYrfk&embeds_referring_euri=https%3A%2F%2Fwww.perplexity.ai%2F&embeds_referring_origin=https%3A%2F%2Fwww.perplexity.ai&source_ve_path=MjM4NTE)
