> **branching** - diverging from the main line of development to continue to do work without messing with that main line

#### In a nutshell
When you make a commit, Git stores that commit as a pointer to a snapshot of the content. Each commit stores a reference to its parent (the commit that came before it), much like a linked list in reverse. This is why in the current snapshot you're in, you can only "see" the nodes / snapshots that came before the current one. 

Internally, a commit stores a pointer as well to the root project tree that contains the file names of the files and their corresponding blob hash. This is so that it can recreate what the project looked like when you took the snapshot.

![[commit-internal.png]]

If you make a commit again, that new commit stores a reference to the commit that came before it. Thus, a **branch** is just a lightweight movable pointer to one of these commits. If you create a new branch, it simply creates a new pointer for you to move around. It's like a variable that has a value of a commit hash you can go back to anytime just by referencing the branch name.
```sh
git branch testing
```

Branches are stored as files in the `.git/refs/heads` directory. Each branch is just a file with 40 bytes representing the hash of the commit that it's currently pointing to.

##### HEAD
HEAD is a special pointer that Git uses to keep track of what branch you're currently on. Normally, HEAD points to a branch name, which in turn points to the latest commit in that branch. When HEAD is pointing to a branch, it's considered **attached**. However, if you check out a commit, tag, or any reference that isn't a branch, it becomes **detached**.

#### Merging
A typical workflow is you create a branch, make some changes, then merge it back to the mainline for production deployment. And you can do this with **git merge**.
```sh
git checkout -b hotfix # create new branch for hotfix

# make changes and commit them to the new branch...

git checkout main
git merge hotfix # merge the changes back into the mainline
```

**some caveats**:
- **fast-forward merges** are done only if the commit pointed to by the branch you're on is a direct ancestor of the commit you're merging in; they're called fast-forward merges as Git simply moves the pointer forward
- if the branch you're on *isn't* a direct ancestor of the branch you're merging in, Git does a **merge commit** in which a new commit is made that has 2 parents, as a result of the 3-way merge
![[merge-commit.png]]

#### Branch Management
To see a list the list of branches on your local, including ones your local knows about from the remote, use **git branch**
```sh
git branch
```

To see which branches have already been merged into the current branch, use the `--merged` flag, and the `--no-merged` flag for the opposite. It's safe to delete branches you've already merged
```sh
git branch --merged
```

To set manually set up a remote tracking branch for the current branch:
```sh
git branch -u origin/serverfix
```

#### Branching Workflows
**Long running branches**
With long-running branches, you'll have several branches that are always open and you use for different stages of the development cycle. Typically, you'll have a `main` or `master` branch that is stable -- possibly only code that has been or will be released. They then have other parallel branches like `development` to test new features for stability.

The idea is the branches will be at different levels of stability, with `main` being the most stable. Once, `development` is stable enough, it can be merged into `main`, or the branch above it.

#### Rebasing
Rebasing is another way to integrate changes from one branch into another. Merging is the easier way to integrate changes, it performs a 3-way merge between the two latest branch snapshots. **Rebasing** is another way of integrating changes that works by taking the patch of the change in the feature branch, and reapplying it on top of the main branch. This way, you can choose to perform a fast-forward merge instead of a merge commit.

```sh
git checkout feature-branch
git rebase main
```

**rebasing vs merging**:
- while merging is like tying a knot to connect two ends of a rope together, rebasing is like straightening out the rope
- rebasing is preferred if your team prefers a clean, linear history to trace commits or to see **the story of how your project was made**
- merging is preferred to show a **record of what actually happened** at the cost of possibly messy merge commits, which might not be the case with rebasing (it lies about what actually transpired)

**when you shouldn't rebase**
- do not rebase commits that exist outside your repository
	- ie if you push commits to a shared branch and others pull them down and base their work on them, and then you rewrite those commits with rebasing and push them up again, your collaborators will have to re-merge their work and things will get messy

##### Squashing 
You can also have a linear history when merging if you squash the commits in the feature branch with the `--squash` flag with **git merge**. What this does is it encapsulates all of the commits (and all of the changes) that were made in the feature branch in just one commit, thereby allowing it to just have one parent (the tip of main during the time it's merged). The downside of this is that the more granular commit history is lost, but it does make it easier for you to follow the gradual changes as you don't have to follow the commits made in a branch one by one, you can just look at the squashed merge commit made on main.
```sh
git merge feature-branch --squash
```

