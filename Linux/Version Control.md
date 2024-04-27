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

**Applying changes on a branch, merge, deleting the branch:**

1- Fetch the changes to be up to date
2- Create your branch from right parent branch
3- push the changes
4- Create a merge request to the parent branch, merge.
5- Delete the previous branch


