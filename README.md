# git-selfhelp

Personal Git cheat sheet

Contents
- [Cloning and Changing Remotes](#cloning-and-changing-remotes)

## Cloning and Changing Remotes
<details open>
<summary><i>Click to expand</i></summary>

### Objective

To clone a public repo into a private repo and track changes of the public repo.

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