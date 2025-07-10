+++
date = '2025-07-10T21:19:20Z'
draft = false
title = 'DevOps Study app: Automate Realese creation'
author = 'Moriel Mauni'
tags = [" "]
categories = [" "]
description = " "
+++

### Automate Release creation

Using [release-please-action](https://github.com/google-github-actions/release-please-action) created by Google: 
Release Please automates CHANGELOG generation, the creation of GitHub releases, and version bumps for your projects. Release Please does so by parsing your git history, looking for [Conventional Commit messages](https://www.conventionalcommits.org/), and creating release PRs.


We switch to main and pull --> then let's create a new branch ```ci/add-release-please``` and let's make a file:
/.github/workflows/release-please.yaml

```yaml 
name: Release Please

on:
  push:
    branches:
      - main

jobs:
  release-please:
    runs-on: ubuntu-latest
    steps:
      - uses: googleapis/release-please-action@v4
        id: release
        with:
          # Will not trigger other workflows when using GITHUB_TOKEN
          # token: ${{ secrets.GITHUB_TOKEN }}
          token: "${{ secrets.DEVOPS_STUDY_APP }}"
          config-file: release-please-config.json
          manifest-file: .release-please-manifest.json

```

- name: Release Please.
- Trigger: push to main branch. 
	- Only on merge to main because we don't push to main.
- 1 job:
	- Run on Ubuntu
	- Loads from Google API the release-please-action.
	- id is release
	- Variables:
		- Need a token to access the GitHub API.
		- Create a Token to use:
			- Settings --> Developer Settings --> Personal access token --> Classic token --> [x]repository.
			- Go to the repository --> Settings --> Secrets and variables --> Actions --> New repository secret. **"${{ secrets.TOKEN_NAME }}"**.


devops-study-app/release-please-config.json 

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
    }
  }
}
```

- Points to src/backend.
	- release type is Python.
	- package name: same name as in pyproject.toml. 
	- component: it's the backend.
	- include tag, we got many tags.
	- create a change log and giving the path.
	- extra files:
		- pyproject.toml.
		- uv.lock.

devops-study-app/release-please-manifest.json
```
{
  "src/backend": "0.0.o"
}
```

- The starting point

Commit, push, squash and merge.

![github-release](/images/cicd-release1.jpg)

![github-release2](/images/cicd-release2.jpg)


