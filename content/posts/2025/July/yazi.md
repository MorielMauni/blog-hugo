+++
date = '2025-07-20T20:33:36Z'
draft = false
title = 'Yazi: Blazing fest terminal file manager'
author = 'Moriel Mauni'
tags = [" "]
categories = [" "]
description = " "
+++

### Introduction

I use the terminal for everything, and the more I can do, the stronger I get.
As a DevOps, using the terminal for managing files is a must but sometimes you want to see the bigger picture.

### Yazi 

A fast terminal file manager written in Rust, based on async I/O.

### Installation

[Yazi installation docs](https://yazi-rs.github.io/docs/installation)

#### Arch:

```bash 
sudo pacman -S yazi ffmpeg 7zip jq poppler fd ripgrep fzf zoxide imagemagick
paru -S resvg
```

#### Brew

```bash
brew install yazi ffmpeg sevenzip jq poppler fd ripgrep fzf zoxide resvg imagemagick font-symbols-only-nerd-font
```

#### Debian

```bash
apt install ffmpeg 7zip jq poppler-utils fd-find ripgrep fzf zoxide imagemagick
```

#### mise 
Add this to ```mise.toml```:
```toml 
yazi = "latest"
fzf = "latest"
ripgrep = "latest"
zoxide = "latest"
fd = "latest"
7zip = "latest"
imagemagick = "latest"
paru = "latest"
ffmpeg = "latest"
```
**NOTE**: The ```imagemagick``` have long delay, like really long.

Now, if you type ```yazi``` you can use it.
I made an alias for ```y``` so it will be easy.

### .zshrc / .bashrc configuration

Make yazi to cd to the directory you inside what you close it ```q```
```bash 
function y() {
	local tmp="$(mktemp -t "yazi-cwd.XXXXXX")" cwd
	yazi "$@" --cwd-file="$tmp"
	IFS= read -r -d '' cwd < "$tmp"
	[ -n "$cwd" ] && [ "$cwd" != "$PWD" ] && builtin cd -- "$cwd"
	rm -f -- "$tmp"
}
```

Set a default  editor
```bash 
export EDITOR='nvim'
```

### yazi configuration 

The location of the configuration files:
```bash 
mkdir -p ~/.config/yazi && cd ~/.config/yazi
```


There are 3 configuration files:
```bash 
touch yazi.toml keymap.toml theme.toml
```

I just copied the configuration files from the [Default configuration](https://github.com/sxyazi/yazi/tree/shipped/yazi-config/preset).

To open NeoVim with yazi, add this to ```yazi.toml``` under the "opener" section:

Remove:
```toml 
{ run = '${EDITOR:-vi} "$@"', desc = "$EDITOR", block = true, for = "unix" },

```

Add:
```toml 
{ run = 'nvim "$@"', desc = "Neovim", block = true, for = "unix" },
```

#### Add a new theme

inside ~/.config/yazi
```bash 
mkdir flavors
```

The list of themes that Yazi provide us [yazi-flavors](https://github.com/yazi-rs/flavors).

I'm going to pick [catppuccin-macchiato](https://github.com/yazi-rs/flavors/tree/main/catppuccin-macchiato.yazi).
```bash 
ya pkg add yazi-rs/flavors:catppuccin-macchiato
```

Add this line (depends on the theme you picked) to ```theme.toml```:
```toml 
[flavor]
dark = "catppuccin-macchiato"
```


### Default key-maps 

- **gg**: go to top.
- **Shift + g**: go to bottom.
- **z**: zoxide.
- **Shift + z**: search with zoxide.
- **Shift + h**: backwards a directory.
- **O / Enter**: open a file.
- **Space**: select more then 1 file.
- **y**: yank / copy.
- **Shift + y**: un-yaml / in-copy.
- **p**: paste.
- **d**: delete a file.
- **a**: new file.
- **r**: rename a file.
- **TAB**: information about a file.
- **CTRL + a**: select all.
- **.**: toggle hidden files.
- **/**: search forward.
- **?**: search backwards.
- **c - c**: copy file path.
- **f**: filter files.
- **CTRL + S**: Search inside files.

