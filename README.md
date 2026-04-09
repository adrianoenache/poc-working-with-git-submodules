# Proof of concept (POC) of working with Git submodules

## Get started

> Important! This repository use git submodule.

### Clone

```bash
git clone --recurse-submodules git@github.com:adrianoenache/poc-working-with-git-submodules.git <optional-parameter-to-name-the-folder>
```

If you clone the repository without the parameters **--recurse-submodules** run the commands to **int**, **sync** and **update** the git submodules in the repository.

```bash
git submodule init && git submodule sync && git submodule update
```

### Submodule update

This command will retrieve the latest commits from the branch configured in the **.gitmodules** file, assuming you have already run commands `git submodule int` and `git submodule sync`.

```bash
git submodule update --remote
```

> Commit the changes in the main repository and push it to origin.

### Lock on commit TAG

```bash
cd submodules-git/ui-kit-packages/

git fetch --all --prune

remote: Enumerating objects: 10, done.
remote: Counting objects: 100% (10/10), done.
remote: Compressing objects: 100% (4/4), done.
remote: Total 6 (delta 2), reused 5 (delta 2), pack-reused 0 (from 0)
Unpacking objects: 100% (6/6), 1.33 KiB | 123.00 KiB/s, done.
From github.com:adrianoenache/poc-working-with-git-submodules-packages-for-distribution
   b3b2be0..fb571b5  develop      -> origin/develop
   08ee378..c2f7fa5  main         -> origin/main
 * [new tag]         v0.0.1-alpha -> v0.0.1-alpha

cd ..
cd ..

git status

On branch main
Your branch is up to date with 'origin/main'.

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   README.md
        modified:   submodules-git/ui-kit-packages

git commit -m "Lock the git submodule on the tag v0.0.1-alpha"

[main c6c281b] Lock the git submodule on the tag v0.0.1-alpha
 2 files changed, 7 insertions(+), 1 deletion(-)
```

## Git submodules

- [Proof of concept (POC) of working with Git submodules - Compiled CSS](https://github.com/adrianoenache/poc-working-with-git-submodules-compiled-css)

## References

- [Git Tools Submodules - git-scm](https://git-scm.com/book/en/v2/Git-Tools-Submodules)
- [Git clone - git-scm](https://git-scm.com/docs/git-clone)
- [Git sparse-checkout - git-scm](https://git-scm.com/docs/git-sparse-checkout)
- [Git submodules - Atlassian](https://www.atlassian.com/br/git/tutorials/git-submodule)
- [Git submodules - W3schools](https://www.w3schools.com/git/git_submodules.asp)
- [Working with submodules](https://github.blog/open-source/git/working-with-submodules/)
- [How to Use Git Submodules – Explained With Examples](https://www.freecodecamp.org/news/how-to-use-git-submodules/)
- [Using Git submodules with GitLab CI/CD](https://docs.gitlab.com/ci/runners/git_submodules/)
- [How to Specify a Branch or Tag When Adding a Git Submodule: Explaining `git submodule add -b` and Commit Tracking](https://www.w3tutorials.net/blog/how-can-i-specify-a-branch-tag-when-adding-a-git-submodule/)
- [Mastering Git Submodule Tag Commands for Efficient Workflows](https://gitscripts.com/git-submodule-tag)
- [Git sparse-checkout](https://git-scm.com/docs/git-sparse-checkout)
- [Git Clone Specific Folder: A Quick Guide](https://gitscripts.com/git-clone-specific-folder)
- [Bring your monorepo down to size with sparse-checkout](https://github.blog/open-source/git/bring-your-monorepo-down-to-size-with-sparse-checkout/)
