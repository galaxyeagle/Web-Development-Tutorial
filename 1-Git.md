
# Understanding Git

<style>em{color: #04D9FF;}</style>

Throughout your learning journey, you will work on projects powered by Git. So its fundamentals should be clear.  

The [Git website](https://git-scm.com/) reads that Git is a free and open source distributed version control system. Git is a version control system (VCS) because it tracks file changes. This is crucial because when working on a software project, it’s important to know how to manage your changes effectively. Take a look at the image below :   
 
<img src="//earthdatascience.org/images/earth-analytics/git-version-control/git-add-commit.png" width="40%" height=auto style= "background-color: white; padding: 10px;" >

Graphite.dev explains this process as follows :  
<span style="color: orange;"> Git manages a directed acyclic graph of snapshots. Every commit represents a snapshot of your project’s files at a certain point in time. Before something becomes a commit, it first resides in something known as the staging area. The git add command moves changes from your working directory (the place where you edit files) into this staging area (an index, or record of what’s about to be committed). Then git commit converts whatever is staged into a new snapshot. Finally, git push transfers those snapshots from your local repository to a remote repository, making them visible to your team.</span>

Git is not the only Source Code Management (SCM) software in market. Subversion & Mercurial are other alternatives. Subversion maintains each revision as changeset. Hg uses both changeset and snapshot while Git purely uses snapshot to store the data. 

If you're working alone on your local system, remote repo isn't necessary. If you wish to collaborate on the project so that other people can contribute to your source code, then remote repo is indeed preferred (though not mandatory since Git VCS is "distributed" by design). The most popular remote repository is [Github](https://github.com/). Others are Gitlab, Bitbucket, Sourcehut & Atlassian. The bio of subreddit r/github calls it a "social coding" platform. As I'll highlight later, its quite like a social media platform for coders !

# Working with Git

Download git and git-bash softwares from their websites. Then go to the terminal and set your global git username and email:

```bash
git config --global user.name "Your Name" # set username
git config --global user.email "Your email id" # set email
git config --list # check if they've been set
```

Create a local folder. Git bash into it. Then run `git init`. This will initialize that folder as a git repo having a .git folder, which contains all the necessary metadata and version history for your project. Now you can use any code editor or IDE to create your sourcecode files in the folder. (I personally use Sublime for heavy-text & light-code repos like my github blog, while VSCode for code-heavy projects because it has excellent inbuilt plugins, debugger & recently Github Copilot integration. For Python projects, I use Anaconda). You can add files to the staging area using <em>`git add filename`</em> and commit them with <em>`git commit -m "Your commit message"`</em>. You can create branches to work on different features or fixes independently. Use <em>`git branch branch-name`</em> to create a new branch and <em>`git checkout branch-name`</em> to switch between branches. Merging branches is also possible with <em>`git merge branch-name`</em>. All changes you make are recorded in the local repository's history, which you can view using commands like git log. This allows you to revert to previous states of your project if needed. If you want to view the repository as it was at a specific commit, you can check out that commit using its hash.
bash: <em>`git checkout commit-hash`</em>. To view details about a specific commit, including changes made in that commit, use: <em>`git show commit-hash`</em>. After viewing a previous state, you can return to your latest commit on your current branch with: <em>`git checkout master`</em>. If you want to see what changed between two commits, you can use: <em>`git diff commit-hash-1 commit-hash-2`</em>.   

You can run all these git operations as commands in the Command Line/Git Bash/Terminals inbuilt in the editor or IDE. When I was a newbie I used Windows CMD, then I downloaded Git Bash because it showed the current branch as well. Later I learned about inbuilt terminals inside code editors/IDEs which is my preferred choice till date. However many developers prefer a GUI (also called a Git Client) for these git operations. Popular ones are Github Desktop, Sublime Merge, Fork, Sourcetree, Tower, SmartGit, GitKraken Desktop, etc. 

<span style="color: orange;"> 
NB. Don't confuse between Github Desktop and Github CLI softwares. GitHub Desktop is a GUI for interacting with Git, while GitHub CLI is a CLI specifically designed to interact with GitHub. To utilize all features (eg. visibility settings, licensing, README) available right when creating a repository on GitHub, it's recommended to first create the repository online using the Github webpage or Github CLI with gh repo create command. Once created, you can then clone it locally or push an existing local repository to it. 
</span>


# Working with Github

When many developers wish to merge their contributions to the same master codebase, "merge conflicts" can arise. A web platform like Github handles these merge conflicts like a pro. "Forking" a repository on github.com creates a personal remote copy of a repo under your GitHub account, allowing you to make changes without affecting the original project (known as the upstream repository). "Cloning" is the process of creating a local copy of a remote repo (either original or forked) on your machine, enabling offline work using the command: <em>`git clone repository-url`</em>. 
After making local changes to the repo, perform the usual git add-commit sequence we'd discussed. Then push the changes to the remote forked repo using the git push command. Then you can go to github.com and submit a "Pull Request (PR)" to propose those changes to the original project. The owner of the original project may accept those changes.  

# Working between Local & Remote copies of your Git Repo

If you are sharing your local Git repo on Github, you will have 2 copies of your repo - Local (in your PC) & Remote (in the Github server). You may wish to edit the local repo & want to reflect the changes in the remote repo also, or vice-versa. You may also want to work on a branch and merge changes with the master. For all such exercises, this article shines a light.

To add a remote repo to your local git project and name it say `origin`, use the <em>`git remote add origin <url>`</em> command. To check if its added, use <em>`git remote -v`</em> to list all remotes.  To remove the existing `remote`, 
To rename your remote project from `origin` to say `myProject` use <em>`git remote rename myProject origin`</em>
Once your Local & Remote Repos are setup and linked, you can continue your work as follows.

## <ins> Working on 'master' branch </ins>

### Pushing local changes to remote

Make local changes
<em>	  
	git add .  (stage)<br>
	git commit -m "changes"	(commit)<br>
	git push origin master (push)<br>
</em>

### Pulling remote changes to local

Make remote changes
<em>  
	git fetch origin  <br>
	git merge origin/master <br>
	Or simply: git pull origin master <br>
</em>

# <ins> Working on another branch (say 'develop') </ins>

## Pushing local changes to remote

<em>
	git branch develop   <br>
	git checkout develop <br>
</em>
	Make local changes  
<em>	
	git add .  <br>
	git commit -m "changes"  <br>
	git push origin develop  <br>
</em>

### Pulling remote changes to local

#### 1st time

Create a new branch 'develop' in github & edit it
<em>    
	git fetch  <br>
	git branch develop origin/develop <br>
</em>
		
#### After 1st time  

Make changes in 'develop' in github 
<em>
	git fetch  <br>
	git merge origin/develop <br>
	Or simply: git pull origin develop <br>
</em>
		
		
----------------------------------------------------------------------------------------  
After changes to 'develop' branch are made, & you want to merge it with master branch

On master branch: <em>git merge develop</em>

	
nb. There's a concept called "pull request" only in github where you can create a "pull request" in github while you're on the develop branch
    This will flag a notification on master branch in github with an option for merge. Command line git has no role in this affair.
	
## Deleting 'develop' branch after its merged with master & no longer needed 
  
To delete local copy : <em>`git branch -d develop`</em>  
To delete remote copy also : <em>git push origin --delete develop`</em>

----------------------------------------------------------------------------------------

Sometimes you work on both your `local` and `remote` repositories depending on your mood ! In such cases a gap is created between the two. In VSCode you can use the `sync` feature in the version control UI to `pull` and `push` simulatneously to close the gap. If the gap is too large, it may not work. In such cases, first pull the remote changes using <em>`git pull origin <branch_name> --allow-unrelated-histories`</em>. And then push them using <em>`git push origin <branch_name>`</em>


Alternatively to the first pull-then push concept, you can follow this strategy table:

| Strategy           | Command                                  | Result                                            |
|--------------------|------------------------------------------|---------------------------------------------------|
| Merge              | git config pull.rebase false             | Merge remote into local, creates merge commits    |
| Rebase             | git config pull.rebase true              | Replay local commits on top of remote             |
| Fast-forward only  | git config pull.ff only                  | Only allow fast-forward, otherwise abort          |


# Actual version control

When you're working on a project professionally, you'll want to go back to a past commit and create a new branch from there and work on it. Later you may either want to keep working on that brancha and delete the original one, or merge this new branch with the first one. Here's how you should do all of ths.

`git checkout commit-hash` (headless)
or
`git checkout commit-hash -b newbranch` (head now on new branch)

Or use the newer switch command instead of the checkout command

`git switch -c newbranch commit-hash`

Here is a comprehensive tutorial summarizing our entire discussion about Git workflows involving branching, resetting, squashing commits, and pushing, illustrated with commands and explanations:

---

## 1. Working with Branches and Commits

Suppose you have a branch **A** where you made a series of commits:

```
A: C1 --- C2 --- ... --- Ci --- ... --- Cn
```

You realize commits from `Ci+1` to `Cn` are unnecessary, so you want to start fresh from commit `Ci` and create a new branch **B** from there.

**How to create branch B from commit Ci:**

```bash
git switch -c B 
```
or (older syntax)
```bash
git checkout -b B 
```

- This creates a new branch `B` starting at commit `Ci`.
- You can now make new commits `Ci+1`, `Ci+2`, ... on branch B.
- Branch A remains unchanged unless you delete it.

---

## 2. Deleting Unused Branches

If you no longer need branch A after switching to B, it is good practice to delete it to keep your repo tidy:

- Switch to another branch (not A):
  ```bash
  git switch B
  ```
- Delete local branch A:
  ```bash
  git branch -d A   # safer, only deletes if merged
  ```
  or force delete if not merged:
  ```bash
  git branch -D A
  ```
- If branch A exists on remote and you want to delete it there:
  ```bash
  git push origin --delete A
  ```

*Note:* You can delete local and remote branches in any order, but always ensure you are not currently on the branch you want to delete.

---

## 3. Understanding `git reset`

**`git reset`** changes the current branch pointer and optionally affects the staging area and working directory.

- `git reset --soft `  
  Moves HEAD to ``, keeps all changes from later commits staged (index), working directory unchanged.  
  Use case: rewrite history by squashing or editing commits without losing code changes.

- `git reset --hard `  
  Moves HEAD to ``, resets staging area and working directory to match ``, discarding all changes after it.  
  Use case: discard unwanted commits and changes (use with caution).

Example to undo last commit but keep changes staged:

```bash
git reset --soft HEAD~1
```

---

## 4. Difference Between `git reset` and `git revert`

| Command            | Effect on History                   | Effect on Code Changes                | Safe for Shared Branches? |
|--------------------|-----------------------------------|-------------------------------------|---------------------------|
| `git reset`        | Rewrites history (removes commits) | Changes kept (`--soft`) or discarded (`--hard`) | No                        |
| `git revert`       | Adds new commit that undoes changes | Changes undone by new commit        | Yes                       |

- `git revert` is preferred on shared branches because it preserves history and avoids conflicts.
- `git reset` is powerful but can cause problems if used on branches others rely on.

---

## 5. Force Pushing After History Rewrite

After rewriting history locally (e.g., with `git reset --soft` and a new commit), your local branch will diverge from the remote. A normal `git push` will be rejected.

To update the remote branch to match your rewritten history:

```bash
git push --force
```

**Warning:** Force pushing overwrites remote history and can disrupt collaborators. Use carefully and communicate with your team.

---

## 6. Squashing Commits

If you made many commits on branch B and want to merge them into branch A as a **single squashed commit**, use:

### Method 1: `git merge --squash`

1. Switch to branch A:
   ```bash
   git switch A
   ```
2. Squash merge branch B:
   ```bash
   git merge --squash B
   ```
3. Commit the squashed changes:
   ```bash
   git commit -m "Squashed commit from branch B"
   ```
4. (Optional) Delete branch B if no longer needed:
   ```bash
   git branch -d B
   ```

**How it works:**  
All changes from B are combined into your working directory and staged as one big change. No merge commit is created automatically; you create a single new commit summarizing all changes.

---

### Method 2: Squashing last N commits using `git reset --soft`

If you want to squash the last N commits on your current branch into one commit:

```bash
git reset --soft HEAD~N
git commit -m "New squashed commit message"
```

This moves HEAD back N commits, keeps all changes staged, and lets you create a new single commit.

---

## 7. Summary of Important Commands

| Command                              | Purpose                                              |
|------------------------------------|------------------------------------------------------|
| `git switch -c B `        | Create and switch to new branch B from commit Ci     |
| `git branch -d A`                   | Delete local branch A (only if merged)                |
| `git branch -D A`                   | Force delete local branch A                            |
| `git push origin --delete A`       | Delete remote branch A                                 |
| `git reset --soft `         | Move HEAD to commit, keep changes staged              |
| `git reset --hard `         | Move HEAD to commit, discard all changes after it     |
| `git revert `               | Create a new commit that undoes changes from commit   |
| `git push --force`                  | Force push rewritten history to remote                |
| `git merge --squash B`              | Squash merge branch B into current branch             |
| `git commit -m "message"`           | Commit staged changes with message                     |

---

## 8. Visual Illustration

Initial state:

```
A: C1 --- C2 --- Ci --- C(i+1) --- ... --- Cn
```

After creating branch B from Ci:

```
A: C1 --- C2 --- Ci --- C(i+1) --- ... --- Cn
                 \
B:                Ci --- C(i+1)' --- C(i+2)' --- ...
```

After squash merging B into A:

```
A: C1 --- C2 --- Ci --- Cnew
```

Where `Cnew` is a single commit containing all changes from B.

---

## 9. Best Practices

- Use `git switch -c` to create branches from specific commits safely.
- Delete unused branches to keep your repo clean.
- Use `git reset --soft` for local history rewriting, but avoid on shared branches.
- Use `git revert` to undo commits safely on shared branches.
- Force push only after rewriting history and with team communication.
- Use squash merges to keep history clean and manageable.

---

# Contributing to a repo on Github

Refer to my [this](https://dev.to/raman_butta/step-by-step-guide-to-submitting-a-pull-request-on-github-5fok) tutorial.

# References

- [Benefits of Distributed Version Control Systems](https://about.gitlab.com/topics/version-control/benefits-distributed-version-control-system/)
- [Graphite.dev Guide](https://graphite.dev/guides/git-add-commit-push)


