# git-selfhelp

Personal Git cheat sheet

## Contents
- [Cloning and Changing Remotes](#cloning-and-changing-remotes)
    - [Cloning Only](#cloning-only)
    - [Clone and Track Updates](#clone-and-track-updates)
- [Handling Merge Conflicts](#handling-merge-conflicts)
    - [Unable to Git Pull as Local Repo Commits have Diverged from Remote](#unable-to-git-pull-as-local-repo-commits-have-diverged-from-remote)

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

## Handling Merge Conflicts

### <u>Unable to Git Pull as Local Repo Commits have Diverged from Remote</u>

<details>
<summary><i>Click to expand</i></summary>

- Fetch changes made to the upstream since the last `git fetch`, merge to local branch, and apply existing local commits to the top of the updated local branch (aka "rebasing the local branch on top of the upstream branch after fetching"). 

    ```
    git pull --rebase
    ```

</details>