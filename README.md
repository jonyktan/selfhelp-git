# git-selfhelp

Personal Git cheat sheet


## Contents
- [Cloning and Changing Remotes](#cloning-and-changing-remotes)
    - [Cloning Only](#cloning-only)
    - [Clone and Track Updates](#clone-and-track-updates)
- [Co-Development](#co-development)
- [Managing Changes](#managing-changes)
    - [Squashing Commits in General](#squashing-commits-in-general)
    - [Squashing Commits During Merge](#squashing-commits-during-merge)
- [Handling Merge Conflicts](#handling-merge-conflicts)
    - [Unable to Git Pull as Local Repo Commits have Diverged from Remote](#unable-to-git-pull-as-local-repo-commits-have-diverged-from-remote)
    - ["Combining" Branches](#combining-branches)
- [Handling Line Endings](#handling-line-endings)
- [Submodules](#submodules)
- [Cleaning Up](#cleaning-up)



## Setting Up

Setting up git credentials on new machine. 

<details>
<summary><i>Click to expand</i></summary>

_Reference: GitHub Docs for SSH keys_

1. [Check for existing SSH key on machine](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/checking-for-existing-ssh-keys)

1. [Generate new SSH key if none available](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)

1. [Add SSH key to GitHub account](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account)

1. Use SSH URL for `git clone` (e.g. `ssh://git@github.com/username/repo.git`). Edit in `.git/config` if previously cloned using `https` or `git` URL. [See reference here](https://stackoverflow.com/a/7773605). 

</details>


## Cloning and Changing Remotes

### <u>Cloning Only</u>

<details>
<summary><i>Click to expand</i></summary>

### Objective

To quickly clone a public repo into a private repo. <i>Caveat: clone of public repo on local machine is not usable for development.</i>

### Steps

1. Clone the public repo onto local machine. This includes all branches and commit history, but without any link back to the Github repo, i.e. you will not able to `git fetch` again.

    ```
    git clone --bare <public repo url> <local folder name>
    ```

1. Create private repo on Github.

1. Enter local folder.

    ```
    cd <local folder name>
    ```

1. Push the local version of the contents of public repo to the private repo. 

    ```
    git push --mirror <private repo url>
    ```

1. Delete local folder. 

    ```
    cd ..
    ```

    ```
    rm -rf <local folder name>
    ```

1. Clone private repo.

    ```
    git clone <private repo url> <local folder name>
    ```

1. To setup the new local folder to track changes in the public repo, see steps 7 onward in the [next section](#clone-and-track-updates) to create a tracking remote.

</details>


### <u>Clone and Track Updates</u>

<details>
<summary><i>Click to expand</i></summary>

### Objective

To clone a public repo into a private repo and <u>track changes of the public repo</u>.

### Steps

1. Clone the public repo onto local machine.

    ```text
    git clone <public repo url> <local folder name>
    ```

1. Create private repo on Github.

1. Enter local folder.

    ```text
    cd <local folder name>
    ```

1. Change remote for `origin` for local folder to private repo. `origin` is the default pull/push Git repo for local folder.

    ```text
    git remote set-url origin <private repo url>
    ```

1. Check that the origin remote has been changed.

    ```text
    git remote -v
    ```
        
        > origin <private repo url> (fetch)
        > origin <private repo url> (push)
        

    Alternatively, check that the remote "origin" is set to private repo url in the git config file.

    ```text
    cat .git/config
    ```

1. Push cloned files to `main` branch on private repo. This also sets the upstream for `main` to the `origin` remote. 

    ```text
    git push origin main
    ```

1. Create branch to track public repo.

    ```text
    git branch <tracking branch name>
    ```

1. Create new remote to track public repo.

    ```text
    git remote add <new remote name> <public repo url>
    ```

1. Check that the new tracking remote has been created.

    ```text
    git remote -v
    ```

1. Fetch the branch info from the new tracking remote (aka the public repo).

    ```text
    git fetch <new remote name>
    ```

1. Change the upstream for the new tracking branch to the new tracking remote.

    ```text
    git branch <tracking branch name> --set-upstream-to <tracking remote name>/<branch on public repo>
    ```

1. Check that the main development branch is tracking the private repo and the tracking branch is tracking the public repo.

    ```text
    cat .git/config
    ```

</details>


## Co-Development

<details>

<summary><i>Click to expand</i></summary>

### Managing Branches

Create new feature on new working branch.

1. Create new working branch from existing branch (e.g. 'development').

    ```
    git checkout -b <new branch name> <existing branch name>
    ```

1. Push the new branch to Github (or remote). 

    ```
    git push --set-upstream <remote name> <new branch name>
    ```

</details>


## Managing Changes

<details>

<summary><i>Click to expand</i></summary>

### <u>Squashing Commits in General</u>

1. View commit history.

    ```
    git log --oneline
    ```

1. Squash *x* past commits.

    ```
    git rebase -i HEAD~<x>
    ```

    1. An editor will open. 
    1. Use `pick` or `p` to retain specific commits. Use `squash` or `s` to combine specific commits. 
    1. Once done, save the file and close.
    1. Another editor will open with the new commit messages for further editing if required. 
    1. Once done, save the file and close. 

### <u>Squashing Commits During Merge</U>

To clean up commits on one branch (e.g. dev branch) when merging it into another (e.g. main branch). 

1. Switch to main branch.

    ```
    git switch <main branch>
    ```

1. Use `--squash` flag when merging incoming branch (e.g. dev branch).

    ```
    git merge --squash <dev branch>
    ```

1. Commit the changed files with a single commit message. 

1. Push to recipient branch (e.g. main branch), create Pull Request on GitHub, and merge Pull Request. 

### <u>Undoing Commits</U>

1. Check past commits.

    ```
    git reflog
    ```

1. Reset current branch to desired commit (`HEAD@{x}`).

    ```
    git reset --hard HEAD@{x}
    ```

    For Windows:

    ```
    git reset --hard "HEAD@{x}"
    ```

</details>


## Handling Merge Conflicts

<details>
<summary><i>Click to expand</i></summary>

### <u>Unable to Git Pull as Local Repo Commits have Diverged from Remote</u>

- Fetch changes made to the upstream since the last `git fetch`, merge to local branch, and apply existing local commits to the top of the updated local branch (aka "rebasing the local branch on top of the upstream branch after fetching"). (<i>[Explainer](https://gitolite.com/git-pull--rebase)</i>)

    ```
    git pull --rebase
    ```

### <u>"Combining" Branches</u>

- Merge commits from current branch on top of commits on another branch (stays on current branch). 

    ```
    git rebase <another branch>
    ```

- Check that the commit history on current branch is updated.

    ```
    git log
    ```

- Push the changes to the upstream.

    ```
    git push <remote name> --force-with-lease
    ```

</details>


## Handling Line Endings

<details>
<summary><i>Click to expand</i></summary>

### <u>Set global default line endings for Linux</u>

```
git config --global core.autocrlf input
```

### <u>Set global default line endings for Windows</u>

```
git config --global core.autocrlf true
```

</details>


## Submodules

<details>
<summary><i>Click to expand</i></summary>

### <u>Add submodule to Repo</u>

1. Clone submodule into repo.

    ```
    git submodule add <submodule URL>
    ```

1. Commit and push changes to origin (new submodule folder and new .gitmodules file).

### <u>Clone Repo with Submodules</u>

1. Clone the repo.

1. Initialise submodule.
    
    ```
    git submodule init
    ```

1. Fetch data from submodule project.

    ```
    git submodule update
    ```

</details>


## Cleaning Up

<details>
<summary><i>Click to expand</i></summary>

### Delete Branch

```
git branch -d <old branch>
```

- Use `-D` to force delete.

</details>