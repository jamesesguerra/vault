#### Key Terms
- **version control** - a system that records changes to a file or a set of tiles over time so that you can recall specific versions later
- **distributed version control systems** - a version control system wherein clients don't just check out the latest snapshot of the files: they fully mirror the repository, making every clone a fully backup of the data
- **repository** - a collection of *commits*; defines the *HEAD*; contains a set of *branches* and *tags*
- **the index (staging area)** - a place you can confirm changes one by one, before doing a commit
- **working tree** - any directory on your filesystem which has a *repository* associated with it
- **commit** - a *snapshot* of your working tree at some point in time; the state of the HEAD at the time your commit is made becomes that commit's parent
- **branch** - a name or reference for a commit
- **tag** - similar to a branch, is a name for a commit, except that it always names the same commit and can have its own description text
- **master / main** - the name of the branch that is considered as the mainline of development in most repositories
- **HEAD** - is used by your repository to define what is currently checked out
- **Git directory (.git)** - the place where Git stores the metadata and object database for your project

#### Snapshots vs Differences
One thing that sets Git apart from older VCS is that it uses snapshots to keep track of file changes. Older VCS used a list of file-based changes, so a check out of a specific version will refer to a set of files as their difference from a previous version. Meanwhile, Git just keeps track of what the files looked like at a specific point in time.

Snapshots are better because reconstructing a file at a specific point in history can involve applying a series of computationally-expensive deltas starting from the original version, whereas a Git snapshot is a self-contained state.

![[git-snapshots.png]]

#### The Three States
There are 3 states that your files can reside in:
- **committed** - the data is safely stored in your local database
- **modified** - you have changed the file but have not committed it to the database yet
- **staged** - you have marked a modified file in its current version to go into your next commit snapshot

![[git-states.png]]

#### Git Configuration
1. Setting up your identity
```sh
git config --global user.name "John Doe"
git config --global user.email "johndoe@example.com"
```

