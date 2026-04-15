# Proof of concept (POC) of working with Git submodules

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE) [![Node.js](https://img.shields.io/badge/node-%3E%3D24.14.1-brightgreen)](package.json)

This repository demonstrates how to work with Git submodules in a front-end project. The submodule `submodules-git/ui-kit-packages` is a UI Kit distribution package that provides compiled CSS, JS bundle, Web Components bundle and Stylus core config at `submodules-git/ui-kit-packages/`, consumed by the main project.

> **Important:** This repository uses git submodules. Always clone with `--recurse-submodules` or initialise the submodule manually after cloning — see [Get started](#get-started).

## Table of Contents

- [Prerequisites](#prerequisites)
- [Project structure](#project-structure)
- [Get started](#get-started)
  - [Add a submodule](#add-a-submodule)
  - [Clone](#clone)
  - [Check status](#check-status)
  - [Submodule update](#submodule-update)
  - [Lock on commit TAG](#lock-on-commit-tag)
  - [Remove a submodule](#remove-a-submodule)
- [Setup & Installation](#setup--installation)
- [Development](#development)
- [Pre-commit hooks](#pre-commit-hooks)
- [Troubleshooting](#troubleshooting)
  - [Submodule directory is empty after cloning](#submodule-directory-is-empty-after-cloning)
  - [Submodule is in detached HEAD state](#submodule-is-in-detached-head-state)
  - [Unexpected submodule changes in git status](#unexpected-submodule-changes-in-git-status)
- [Repositories](#repositories)
- [License](#license)
- [References](#references)

## Prerequisites

- Node.js >= 24.14.1
- npm >= 11.11.0
- Git (with submodule support)

> **Note:** The versions listed above are the minimum tested versions. Any higher version compatible with semver (i.e. no breaking changes) should also work.

## Project structure

| Directory | Purpose |
|---|---|
| `src/javascript/` | JavaScript source files (components, modules, functions) |
| `src/javascript/entry/` | Entry point file (`main.js`) — Webpack entry for the compiled bundle |
| `src/stylus/` | Stylus CSS source files (atomic design structure) |
| `src/stylus/entry/` | Entry point file (`main.styl`) — imports the full Stylus source tree |
| `distribution/` | Distribution build output (HTML + compiled JS + CSS) |
| `submodules-git/ui-kit-packages/` | UI Kit distribution package (git submodule) |
| `tools/` | Build helper scripts |

## Get started

### Add a submodule

This command adds a submodule tracking the `main` branch of the remote repository into the `submodules-git/ui-kit-packages` directory.

```bash
git submodule add -b main git@github.com:adrianoenache/poc-working-with-git-submodules-packages-for-distribution.git submodules-git/ui-kit-packages
```

After running the command, a `.gitmodules` file is created (or updated) in the root of the repository with the following content:

```ini
[submodule "submodules-git/ui-kit-packages"]
    path = submodules-git/ui-kit-packages
    url = git@github.com:adrianoenache/poc-working-with-git-submodules-packages-for-distribution.git
    branch = main
```

| Field | Value | Description |
|---|---|---|
| `path` | `submodules-git/ui-kit-packages` | Local directory where the submodule is checked out |
| `url` | `git@github.com:…` | Remote repository for the distribution package |
| `branch` | `main` | Branch tracked by `git submodule update --remote` |

Commit the changes to register the submodule in the repository.

```bash
git add .gitmodules submodules-git/ui-kit-packages
git commit -m "Add ui-kit-packages as a git submodule"
git push origin main
```

### Clone

```bash
git clone --recurse-submodules git@github.com:adrianoenache/poc-working-with-git-submodules.git <optional-parameter-to-name-the-folder>
```

If you clone the repository without the `--recurse-submodules` parameter:

```bash
git clone git@github.com:adrianoenache/poc-working-with-git-submodules.git
```

Run the following commands to **init**, **sync** and **update** the git submodules in the repository.

```bash
git submodule init && git submodule sync && git submodule update
```

### Check status

This command shows the current state of all submodules in the repository.

```bash
git submodule status
```

The prefix in each line indicates the current state of the submodule:

| Prefix | Meaning |
|---|---|
| ` ` (space) | Submodule is at the same commit as registered in the parent repository |
| `-` | Submodule is not initialized — run `git submodule init` |
| `+` | Submodule is at a different commit than the one registered in the parent repository |

### Submodule update

This command will retrieve the latest commits from the branch configured in the `.gitmodules` file (`main`), assuming you have already run `git submodule init` and `git submodule sync`.

```bash
git submodule update --remote
```

After updating, commit the new submodule pointer and push it to origin.

```bash
git add submodules-git/ui-kit-packages
git commit -m "Update ui-kit-packages to latest main"
git push origin main
```

> **Note:** These two commands behave differently:
>
> | Command | What it does |
> |---|---|
> | `git submodule update` | Updates to the commit **recorded in the parent repository** (the pointer stored in `.gitmodules`) |
> | `git submodule update --remote` | Fetches the **latest commit from the `main` branch** configured in `.gitmodules` |

### Lock on commit TAG

Navigate into the submodule directory, fetch all remote changes and check out the desired tag.

```bash
cd submodules-git/ui-kit-packages/

git fetch --all --prune

git checkout v0.0.1-alpha

cd ../..

git add submodules-git/ui-kit-packages

git commit -m "Lock the git submodule on the tag v0.0.1-alpha"

git push origin main
```

Expected output from `git fetch --all --prune`:

```
remote: Enumerating objects: 10, done.
remote: Counting objects: 100% (10/10), done.
remote: Compressing objects: 100% (4/4), done.
remote: Total 6 (delta 2), reused 5 (delta 2), pack-reused 0 (from 0)
Unpacking objects: 100% (6/6), 1.33 KiB | 123.00 KiB/s, done.
From github.com:adrianoenache/poc-working-with-git-submodules-packages-for-distribution
   b3b2be0..fb571b5  develop      -> origin/develop
   08ee378..c2f7fa5  main         -> origin/main
 * [new tag]         v0.0.1-alpha -> v0.0.1-alpha
```

Expected output from `git commit`:

```
[main c6c281b] Lock the git submodule on the tag v0.0.1-alpha
 2 files changed, 7 insertions(+), 1 deletion(-)
```

### Remove a submodule

Removing a submodule requires more than one step. Run the following commands from the root of the repository.

```bash
git submodule deinit submodules-git/ui-kit-packages

git rm submodules-git/ui-kit-packages

rm -rf .git/modules/submodules-git/ui-kit-packages

git commit -m "Remove submodule ui-kit-packages"

git push origin main
```

> The `git rm` command automatically removes the corresponding entry from `.gitmodules`. There is no need to edit that file manually.

## Setup & Installation

After cloning the repository and initializing the submodules, install all dependencies and start the project:

```bash
npm run setup
```

This command runs `npm install`, sets up Husky pre-commit hooks and **starts the JS + CSS watchers** — the terminal will remain blocked while the watchers are running. To install only, without starting the watchers:

```bash
npm i && npm run prepare
```

If you need a clean reinstall (e.g. to resolve dependency conflicts):

```bash
npm run npm-reinstall
```

This command removes `node_modules`, clears the npm cache, reinstalls all dependencies, runs `npm audit fix` and lists outdated dependencies (`npm outdated`). At the end, the JS + CSS watchers will start and **the terminal will remain blocked**.

## Development

For initial setup and clean reinstall, see [Setup & Installation](#setup--installation).

| Command | What it does |
|---|---|
| `npm run start` | Start JS + CSS watchers in parallel |
| `npm run js` | Compile JS only (production via Webpack) |
| `npm run js-watch` | Compile JS with file watcher |
| `npm run js-lint` | Run ESLint with auto-fix on JS source files |
| `npm run css` | Lint + compile CSS only (production) |
| `npm run css-lint` | Lint Stylus source files only |
| `npm run css-comp` | Compile Stylus to CSS only |
| `npm run css-watch` | Compile Stylus with file watcher |
| `npm run npm-reinstall` | Removes `node_modules`, clears the npm cache, reinstalls all dependencies, runs `npm audit fix`, lists outdated dependencies and starts the JS + CSS watchers |

## Pre-commit hooks

This project uses [Husky](https://typicode.github.io/husky/) to run checks automatically before every commit:

| Hook | Command | What it does |
|---|---|---|
| pre-commit | `npm run css-lint` | Lints all Stylus source files |
| pre-commit | `npm run js-lint` | Runs ESLint with auto-fix on JS source files |

Commits are blocked if any lint check fails.

> **Note:** The `prepare` script (which sets up Husky) is invoked automatically when running `npm run setup`. You do not need to run it separately.

> **Bypassing hooks (use with caution):** In exceptional circumstances you can skip the pre-commit hooks with `git commit --no-verify`. This should only be used as a last resort — bypassing lint checks may introduce code quality issues into the repository.

## Troubleshooting

### Submodule directory is empty after cloning

If you cloned without `--recurse-submodules`, the `submodules-git/ui-kit-packages/` folder will be empty. Run:

```bash
git submodule init && git submodule sync && git submodule update
```

### Submodule is in `detached HEAD` state

This is expected after `git submodule update`. The submodule is checked out at the commit pointer recorded in this repository, not at the tip of a branch. To work on the submodule and push changes, navigate into the directory and check out the appropriate branch:

```bash
cd submodules-git/ui-kit-packages
git checkout <branch>
```

### Unexpected submodule changes in `git status`

If `git status` shows `modified: submodules-git/ui-kit-packages (new commits)` without you having changed anything, your local submodule pointer is ahead of what is recorded in this repository. Either commit the updated pointer (if intentional) or reset it:

```bash
git submodule update --checkout
```

## Repositories

| Repository | Role |
|---|---|
| [poc-working-with-git-submodules](https://github.com/adrianoenache/poc-working-with-git-submodules) | This repository |
| [poc-working-with-git-submodules-packages-for-distribution](https://github.com/adrianoenache/poc-working-with-git-submodules-packages-for-distribution) | Git submodule consumed by this repository |
| [poc-working-with-git-submodules-compiled-css](https://github.com/adrianoenache/poc-working-with-git-submodules-compiled-css) | Generates and publishes the compiled CSS and Stylus core config |
| [poc-working-with-git-submodules-compiled-js](https://github.com/adrianoenache/poc-working-with-git-submodules-compiled-js) | Generates and publishes the compiled JS bundle |
| [poc-working-with-git-submodules-elena-progressive-web-components](https://github.com/adrianoenache/poc-working-with-git-submodules-elena-progressive-web-components) | Generates and publishes the compiled Web Components bundle |

## License

[MIT](LICENSE)

## References

- [Git Tools Submodules - git-scm](https://git-scm.com/book/en/v2/Git-Tools-Submodules)
- [Git clone - git-scm](https://git-scm.com/docs/git-clone)
- [Git submodules - Atlassian](https://www.atlassian.com/br/git/tutorials/git-submodule)
- [Git submodules - W3schools](https://www.w3schools.com/git/git_submodules.asp)
- [Working with submodules](https://github.blog/open-source/git/working-with-submodules/)
- [How to Use Git Submodules – Explained With Examples](https://www.freecodecamp.org/news/how-to-use-git-submodules/)
- [Using Git submodules with GitLab CI/CD](https://docs.gitlab.com/ci/runners/git_submodules/)
- [How to Specify a Branch or Tag When Adding a Git Submodule: Explaining `git submodule add -b` and Commit Tracking](https://www.w3tutorials.net/blog/how-can-i-specify-a-branch-tag-when-adding-a-git-submodule/)
- [Mastering Git Submodule Tag Commands for Efficient Workflows](https://gitscripts.com/git-submodule-tag)
