1-
```shell
which git
```
- shows the binary location of git , no output means you don't have the file to install.

2- Sing in to git hub to have a repository.

3- Tap the 'new ssh key' from your github dashboard (you need the **enter your system's public key** in the dashboard) and give it a name. 

4- Clone project with ssh
- **Change your directory to the path of the cloned project to make changes and use git commands.**
- In the .git file of that project, which is hidden, you have specified which remote repository to write and read from.

5- Identify yourself to git:
```shell
git config --global user.name 'gihub name'
git config --global user.email 'gihub email'
```
- This configs will be saved in the '**gitconfig**' file. (/etc/gitconfig)

6- Create changes to a file on git. It will be saved locally not on your repository. So:
```shell
git statu
```
- Shows you the changes since the last commit
```shell
git diff 'name of the file'
```
- shows you what has changed in the file
```shell
git add 'name of the file'
```
- staged, ready to commit
```shell
git commit -m 'message'
```
- commited
```shell
git push origin 'target branch'
```
- pushed

8- See your commands history:
```shell
history
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


**git branch**: Lists all branches in the repository. This command shows the current branch and highlights the branch you are currently on. ==It also creates a new branch from the current branch if it does not exist.==


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
![[Pasted image 20241229183441.png]]

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

-------------------------------------

**Reversing the commit on the same branch:**

1- Fetch
2- Reset the branch to the commit you want (**change the origin branch**)

![[Pasted image 20240513111003.png]]

- Specify the desired commit from the commit tree:
- ![[Pasted image 20240513140629.png]]

3- Delete the previous commit

4- To see the last commit on your local, roll bach the uncommited changes.
![[Pasted image 20240513141059.png]]

---------------------------------------------------

**==To resolve conflicts, merge the branch containing new changes into the conflicting branch.==**

------------

multiple people are using a server to develop a code . we have multiple ssh keys to pull and push git. how to handle it

**1. Use a Per-User SSH Key Configuration**

```shell
ssh-keygen -t rsa -b 4096 -C zareiminooo@gmail.com
```
![[Pasted image 20241223170333.png]]

Then users pub and private keys are generated.

### **2. Configure `~/.ssh/config`**

Each user can configure their own `~/.ssh/config` file to manage multiple identities. Here's an example configuration:

```plaintext
# Default GitHub identity
Host github.com
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_rsa

# Additional identities for different projects
Host project1-github
  HostName github.com
  User git
  IdentityFile ~/.ssh/project1_key

Host project2-gitlab
  HostName gitlab.com
  User git
  IdentityFile ~/.ssh/project2_key

```

Your SSH `config` file has two conflicting `Host git.mci.dev` entries. To use multiple identities for the same host, you need to distinguish them using ==**Host aliases**==. Here's how to fix and use the configuration effectively:

```plaintext

Host git-sorush
    HostName git.mci.dev
    User git
    AddKeysToAgent yes
    IdentityFile ~/.ssh/sorush


Host git-maryam
    HostName git.mci.dev
    User git
    AddKeysToAgent yes
    IdentityFile ~/.ssh/maryam

```

**Clone a Repository with Sorush's Key**: Use the alias `git-sorush` when cloning:

```bash
git clone git@git-sorush:your-repo.git
```
---------

Setting the `name` and `email` in Git is important because these values are used to identify you as the author of commits.

``` bash
git config user.name "Your Work Name"
git config user.email "your.work@example.com"  //repository level
```

```bash
git config --global user.name "Your Name"
git config --global user.email "your_email@example.com"  //global level
```

this will change the config of the git.

Git uses a hierarchy to determine which configuration to apply:

1. **Repository-Level** (`.git/config`): Overrides global and system settings.
2. **Global-Level** (`~/.gitconfig`): Overrides system settings.
3. **System-Level** (`/etc/gitconfig`): Base-level configuration.


If you want to use **both Sorush and Maryam identities** in the same repository, you can achieve this by switching between identities as needed. Here’s how to configure and use them effectively:

```bash
git commit --author="Maryam <maryam@example.com>" -m "Commit message"
```

You can also control which SSH key is used for pushing by using your `~/.ssh/config` setup. For example:

```bash
git clone git@git-sorush:your-repo.git
```

==Tip==: Use alias for remote git actions (like pull, push, clone,...) and use --global for local git actions (commit, ...)

==Tip==: Since you created a new ssh key for server machine git authentication, you need to add it to your git server account.

-----------

**==You need to initialize git for a new directory which you want to turn it into a repository (this is not a cloned project, since cloned projects are already a repository==**

```bash
`git init`

//setup git configuration
git config user.name "Your Name"
git config user.email "your.email@example.com"

//add all files to the repository
git add .

//commit changes
```

### **1. Create a GitHub Repository**

Go to [GitHub](https://github.com/) and create a new repository:

1. Go to the **Repositories** tab on your GitHub profile and click **New**.
2. Enter a repository name (e.g., `tts`), add a description if desired, and make the repository public or private.
3. Do **not** initialize the repository with a README or `.gitignore` (since you already have a project).
4. Click **Create repository**.

### **2. ==Link== Your Local Repository to GitHub**

Copy the URL for your newly created GitHub repository. Then, in your terminal, add this as a remote repository:

```bash
git remote add origin https://github.com/yourusername/tts.git
```

now you can push the changes.

The error fatal: not a git repository (or any of the parent directories): .git means that the current directory (~/maryam) is not recognized as a Git repository.

-----------

return to the last commits on you local (used when merging to remote faces lots of conflicts) :

```
git reset --hard
```

Tip: if you delete "a" branch using (git branch -D branch_name) it will delete it from your local, then you can fetch all changes (git fetch --all) and switch to remote "a" branch.