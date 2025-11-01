+++
date = '2025-11-01T19:23:27Z'
draft = false
title = 'Chocolatey in Ansible'
author = 'Moriel Mauni'
tags = [" "]
categories = [" "]
description = " "
+++

### Chocolatey

Just like 'apt', 'dnf', 'brew', 'pacman', and 'aur', Chocolatey is an open source Windows package manager.

In Chocolatey docs, they really know to give you the way to do things with Ansible himself.

#### Chocolatey package

"Chocolatey packages are known as nupkg files, which is a compiled NuSpec or a fancy zip file that knows about package metadata (including dependencies and versioning). These packages are enhanced NuGet packages; they have additional metadata that is specific to Chocolatey. Chocolatey is also compatible with vanilla NuGet packages. A Chocolatey package can contain embedded software and/or automation scripts."

### chocolatey.chocolatey.win_chocolatey

It is the module that is part of 'Galaxy collection' to configure Chocolatey with Ansible.

```bash
ansible-galaxy collection install chocolatey.chocolatey
```

- Most of the time, it's already installed.

#### Modules

```
win_chocolatey          Manage packages using chocolatey
win_chocolatey_config   Manage Chocolatey config settings
win_chocolatey_facts    Create a facts collection for Chocolatey
win_chocolatey_feature  Manage Chocolatey features
win_chocolatey_source   Manage Chocolatey sources
```

#### Use cases

Upgrade all packages with Chocolatey:

```yaml
- name: Upgrade installed packages
  win_chocolatey:
    name: all
    state: latest
```

Install version 6.6 of notepadplusplus:

```yaml
- name: Install notepadplusplus version 6.6
  win_chocolatey:
    name: notepadplusplus
    version: '6.6'
```

- Pick a package. pick a version, install it silently.

#### state

**state** (string): present/latest/absent/downgrade/reinstalled.
