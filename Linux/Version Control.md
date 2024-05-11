1-
```shell
which git
```
- shows the binary location of git , no output means you dont have the file to install.

2- Sing in to git hub to have a repository.

3- Tap the 'new ssh key' from your github dashboard (you need the enter your system's public key in the dashboard) and give it a name. 

4- Clone with ssh

5- Identify yourself to git:
```shell
git config --global user.name 'gihub name'
git config --global user.email 'gihub email'
```


--------------------------------------------
 
- The Git command in Linux is used for version control, allowing users to track changes to files and collaborate with others on projects.

**git init**: Initializes a new Git repository in the current directory. This command creates a new `.git` directory that stores the repository's metadata and configuration.


**git clone <repository>**: Clones a remote Git repository to your local machine. This command creates a copy of the repository, including all files and commit history.


**git add <file>**: Adds a file or directory to the staging area. Files in the staging area are included in the next commit.


**git commit -m "<message>"**: Commits the changes in the staging area to the repository. The `-m` flag is used to provide a commit message describing the changes.


**git status**: Displays the status of the working directory and staging area. This command shows which files are modified, which files are staged, and which files are untracked.


**git diff**: Shows the differences between the working directory and the staging area. This command can be used to review changes before committing them.


**git log**: Displays the commit history of the repository. This command shows a list of commits, including the commit message, author, and timestamp.


**git push**: Pushes local commits to a remote repository. This command uploads local changes to the remote server, keeping the repository in sync.


**git pull**: Fetches changes from a remote repository and merges them into the current branch. This command is used to update your local repository with changes from the remote server.


**git branch**: Lists all branches in the repository. This command shows the current branch and highlights the branch you are currently on.


**git checkout -b<branch>**: Switches to the specified branch. This command allows you to move between branches in the repository.


**git merge <branch>**: Merges the specified branch into the current branch. This command combines the changes from the specified branch into the current branch.


**git remote**: Manages connections to remote repositories. This command shows the remote repositories configured for the current repository.

-  When you initialize GitLab on your local machine, you may be asked to add an SSH key for authentication purposes. This is because GitLab uses SSH keys to securely authenticate and authorize your access to Git repositories hosted on GitLab.
  ![[Pasted image 20240427093046.png]]

- You can do the merge request from the git's UI.

To delete a branch in Git, you can use the following command (**must be on its parent branch to delete it** )
In Git, a parent branch typically refers to the branch **from which a new branch was created**.
```
git branch -d branch_name
```

Tip: If you merge the child branch into another branch, all changes on its parent branch will be merged too.

**Applying changes on a branch, merge, deleting the branch (in case you don't have the access to push directly on a branch, which will be our parent branch here):**

1- Fetch the changes to be up to date
2- Create your branch from right parent branch

![[Pasted image 20240427145244.png]]

3- push the changes
4- Create a merge request to the parent branch, merge.
5- Delete the previous branch

--------
`git cherry-pick` is a Git command used to apply the changes introduced by a specific commit onto the current branch. It allows you to take a commit from one branch and apply it onto another branch, effectively copying the changes introduced by that commit.

Here's how `git cherry-pick` works:

1. Identify the commit you want to cherry-pick: First, you need to identify the commit you want to apply to the current branch. You can find the commit hash by using `git log` or any other Git history visualization tool.
    
2. Switch to the target branch: Make sure you are on the branch where you want to apply the changes.
    
3. Cherry-pick the commit: Use the `git cherry-pick` command followed by the commit hash of the commit you want to apply. Git will attempt to apply the changes introduced by that commit onto the current branch.
    
4. Resolve conflicts (if any): If there are any conflicts between the changes introduced by the cherry-picked commit and the current state of the branch, Git will pause the cherry-pick process and prompt you to resolve the conflicts manually. Once conflicts are resolved, you can continue the cherry-pick process by using `git cherry-pick --continue`.
    
5. Complete the cherry-pick: Once there are no more conflicts, Git will successfully apply the changes from the cherry-picked commit onto the current branch.
    

Here's the basic syntax for cherry-picking a commit:

bash

`git cherry-pick <commit-hash>`

Alternatively, you can cherry-pick a range of commits using commit hashes or commit ranges.

Cherry-picking can be useful in scenarios such as:

- Applying bug fixes from one branch to another.
- Reapplying changes that were accidentally reverted.
- Extracting specific changes from a feature branch to apply them to another branch.

--------------------
Here's a comparison of `git fetch` and `git pull`:

1. `git fetch`:
    
    - `git fetch` only retrieves changes from the remote repository and updates the remote tracking branches in your local repository. It does not merge or rebase the retrieved changes into your current branch.
    - It is a safe operation that does not affect your working directory or local changes.
    - After running `git fetch`, you can inspect the changes using commands like `git log origin/<branch>` to view the commits on the remote branch.
2. `git pull`:
    
    - `git pull` is a combination of two operations: `git fetch` followed by `git merge` or `git rebase`. By default, it fetches changes from the remote repository and merges them into the current branch.
    - If you have local changes in your working directory, `git pull` will attempt to merge the remote changes with your local changes. If there are conflicts, you will need to resolve them.
    - `git pull` updates both the remote tracking branches and the current branch in your local repository.

--------------------------------
There are different **tags** for each **branch** shown below:
**The last commit on each branch has these tags.**

![[Pasted image 20240427124902.png]]

Purple: The branch is on remote server
Yellow: The branch is our current branch
Green: The branch is available locally (we can checkout on it)
---------------------------------

In case that merge request form branch A to B faces conflicts, merge the B to Branch A in your IDE to resolve conflicts:

![[Pasted image 20240427145954.png]]

Then the merge request will be flawless.


---------------------------------------------------
Tip: Merge request merges branches not commits. So if you previously have a merge request, you can apply commits to that branch before it merges to the target branch.


--------------------------------------

View the changes in each commit:

![[Pasted image 20240429141021.png]]

----------------------------------------

Fetching and then checking out on a specific branch, equals pulling the changes in that branch.

-----------------------------------------
Obsidian configuration:

- git init

creates a new local git repository (the local .git folder which the tracking files will be stored in. We can reconize the files in this folder using **git status** and add files to it using **git add**)

- git remote add origin https://github.com/MaryamAsgarinezhad/obsidian.git

adds a new remote repository named "origin" to your local Git repository. This allows you to push your local changes to the remote repository and pull changes from it.

--------------------------------------------------------------------

Add new files to .git file to be tracked:

![[Pasted image 20240503192415.png]]

- error explanatiom :You haven't made any commits yet, so there are no changes recorded in Git.
- Git has detected some files in your working directory that it hasn't started tracking yet. These files are listed under "Untracked files".

```shell
git add .
git commit -m 'plugins added'

now push
```
-------------------------------------------------------------------

Git authentication using ssh key:

![[Pasted image 20240503193021.png]]
----------------------------

Release definition: a release, which is an instance of the application running on the cluster. Each release has a unique name and can be managed independently.

-------------------------------

**GitOps** is a set of practices that use Git as a "single source of truth" for declaratively managing infrastructure and application configurations. It leverages the version control capabilities of Git to automate and streamline the operational aspects of software development, deployment, and infrastructure management.

------------------------
Add a new file to the .git folder:
![[Pasted image 20240504163545.png]]

-----------------------------
Online gitlab IDE for edition and commiting:

![[Pasted image 20240504164105.png]]

---------------------
This command shows the history of your commits, along with the tag of the image pushed:

![[Pasted image 20240504164709.png]]

------------------------------
pull: fetch + update
![[Pasted image 20240504164840.png]]

------------------

**After merging the child branch with parent, the child branch will be deleted on the repository and you have it locally, so delete it locally and create a new branch for later merge requests.**

- fetch, check out to the latest commit of the parent branch,create the new branch, commit, merge request.
