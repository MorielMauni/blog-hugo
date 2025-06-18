+++
date = '2025-06-17T00:10:20Z'
draft = false
title = 'Making the blog'
author = 'Moriel Mauni'
tags = ["code", "blog", "hugo", "guide"]
categories = ["code"]
description = "How to make a website using Hugo framework"
+++

### The Setup:

I'm working with DevPods, it's making my main setup clean, I don't need to install on it all the packages I use in all the projects. Also, the DevPod is connected to GitHub repository.

Every DevPod need a .devcontainer.json file:

```json
{
  "image": "mcr.microsoft.com/devcontainers/base:debian",
  "forwardPorts": [1313]
}
```

The "forwardPorts: [1313]" is to open the Devpod to be open to post 1313 (which is the port hugo uses) so I could see website to check on it.

### Making a new website

Making a new website is easy as this:

```bash
hugo new website <site-name>
```

Then, a new directory will be created:

```bash
cd <site-name>
```

### Hugo directory tree:

```
└── <site-name>
    ├── archetypes # Define data about content
    │   └── default.md
    ├── content # content of the website
    ├── data # DataBase for the website
    ├── hugo.toml # AKA config.toml The main file, setting file
    ├── layouts # for headers and footers, etc..
    ├── static # Static elements: images, css, etc..
    └── themes # Pre-build themes
```

There is 2 ways to create a website with hugo:

1. Simple website: editing only the content and theme directories.
2. Complex website: editing archetypes, data, content, and theme directories.

### Themes

Hugo have a official gallery for themes: [Hugo gallery themes](htpps://themes.gohugo.io). It's providing all the GitHub repositories of the themes and most important the documentation.

All the theme sits in one directory which is theme:

```bash
cd themes
```

Then, we going to clone the repository of the theme we want:
```bash 
git clone htpps://github.com/UserName/GitRepo.git
```

**For your information**: read the documentation, some repositories requires you to use it as Git submodule.

To activate the theme we need to edit the hugo.toml file and add this line:
```toml
theme = 'name-of-theme-dir'
```

Now, for the first time, we can see our website with: 
```bash 
hugo server
```

To view the website go to [http://localhost:1313](http://localhost:1313).

### Create new content 

To create a new content web page, we will run the command:
```bash 
hugo new <file-name>.md 
```

It will create a file which is a Mark Down file with toml prefixes at the static
```toml 
+++
title =  'title'
date: '2025-06-14T22:46:19Z'
draft: true
+++
```

**For your information**: the "draft" section is if the web page going to be live (false) or only to your eyes only (true).

### Archetypes:

```
+++
date = '{{ .Date }}'
draft = true
title = '{{ replace .File.ContentBaseName "-" " " | title }}'
+++
```

This is the default of the meta-data, we can edit this so every time we will get what we want.

We can create a new file in archetypes directories with the name of a directory we can give it a custom archetype. 

For example: 
archetypes / dir1.md
```
+++
date = '{{ .Date }}'
draft = true
title = '{{ replace .File.ContentBaseName "-" " " | title }}'
author = 'Moriel Mauni'
+++
```

All the xxx.md files that will create under the dir1 going to have this meta-data.

### Taxonomies

how to group content together in affiance way.  tags and categories. 

in the meta-data
```
tags = ["tag1", "tag2"]
catagories = ["cat1", "cat2"]
```

We can create custom Taxonomies in the config.toml file
one = "many"
```
[taxonomies]
	tag = "tag"
	category = "catagory"
	mood = "moods"
```


