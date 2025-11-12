##### Initializing / Cloning a repository
**git init** - creates a new `.git` subdirectory under the current directory
```sh
git init
```

**git clone**
- clones a full copy of nearly all the data a server has; every version of every file is pulled down
- essentially creates a `.git` directory, pulls down all data for that repository, and checks out a working copy of the latest version
```sh
git clone [url] [optionalName]
```

##### Recording Changes
Each file in the working directory can be in one of two states: **tracked** or **untracked**. Tracked files are files that were in the last snapshot i.e. Git already knows about it and is tracking it. Untracked files are the files that Git doesn't know about and so you have to introduce them first (by adding them to the staging area).

When you first clone a repository, all the files will be tracked and unmodified because you haven't made any changes yet. 

![[git-lifecycle.png]]

To check the status of your changes, use **git status**. This tells you if there are any untracked / modified files, and if you've diverged from the main branch.
```sh
git status
```

##### Tracking files and staging changes
**git add** is a multipurpose command:
- it adds untracked files to let Git know about them
- it stages modified files to the staging area in its current state, preparing it to go into the next snapshot (commit)
- it marks merge-conflicted files as resolved
```sh
git add [file | directory]
```

##### Ignoring Files
Create a `.gitignore` to prevent Git from automatically adding files / showing you files as untracked. This is recommended for automatically generated log files, or files produced by the build system.
```
*.[oa]  // ignore files ending in .o or .a
*~      // ignore files that end in a tilde
!lib.a  // ignore .a files but NOT lib.a
/sample // ignore the sample file in the current directory, but not test/sample
build/  // ignore the build directory
```

##### Viewing staged and unstaged changes
**git diff** can compare what's in your working directory with what's in your staging area. By running `git diff`, it can tell you the changes you've made that you haven't staged yet. Unlike **git status**, it doesn't just tell you what files you haven't staged, it tells you the changes you haven't staged.
```sh
git diff # show changes that are still unstaged (compare it with staged changes if any, or latest snapshot)
```

It can also compare what's currently staged with what's in the latest snapshot.
```sh
git diff --staged # show changes that are staged (compare it with latest snapshot)
```

##### Committing changes
**git commit** takes all your staged changes and puts it into the latest snapshot.
```sh
git commit -m "[commit message]"
```

##### Removing files
To remove a file from Git (let Git not know about it anymore), use the **git rm** command. This both removes the file from your working directory and stages it to be committed.
```sh
git rm [file | directory]
```

If you've made changes to the file you wanted to delete and have already staged it, Git won't allow you to remove it with **git rm**. You have to force it with the `-f` flag.
```sh
git rm [file | directory] -f
```

However, if you want to just stop Git from tracking a file without deleting it, use the `--cached` option. This might be useful if you forgot to add something to your .gitignore file.
```
git rm --cached [file | directory]
```

##### Renaming files
If you want to rename a file, use the **git mv** command. It's equivalent to renaming it  manually (`mv`), removing the old file from Git and staging it (`git rm`), and staging the new file (`git add`)
```sh
git mv [file] [new_file]
```

##### Viewing commit history
To view the commit history, use the **git log** command. Without any flags, the command lists down the commits in reverse chronological order ie the most recent commits show first.
```sh
git log
git log -p # show the actual changes
```

You can also limit the log output in a variety of ways.
```sh
git log --author="James Esg" # search for commits made by a certain author
git log --grep="delete" # search for commits with 'delete' in the message
```

##### Viewing what changes haven't been merged
You can also use **git log** to see the commits that are on a latter branch, but not on the former branch.
```sh
git log --no-merges branch1..branch2
```

##### Undoing Things
If you make a mistake and commit too early -- either you forgot to include files or you just want to change the commit message, use **git commit --amend**
```sh
git commit --amend
```

If you've made no changes since your last commit, the snapshot will look exactly the same, and all that will be changed is the commit message. The second commit will then replace the first commit.

##### Unstaging a staged file
To unstage a staged file, use **git reset**
```sh
git reset [file | directory]
```

##### Unmodifying a file
To remove the changes u made to a file / directory, use **git restore**
```sh
git restore [file | directory]
```

##### Working with remotes
Remotes are remote versions of the project hosted somewhere else.
```sh
git remote -v # show all remotes and their URLs
git remote add pb https://github.com/paulboone/git # add a new pb remote
```

To fetch data (branches and new commits) from a remote, use **git fetch**. It's important to note that it only pulls data to your local repository, but doesn't merge it with any of your work.
```sh
git fetch pb # fetch from the remote pb, now all the branches there are accessible from your local
```

To fetch data from a remote branch your branch is tracking (`git clone` automatically sets your local main branch to track the remote  main branch) and automatically merge it with your work, use **git pull**
```sh
git pull
```

Then to push your local work to the remote upstream, use **git push**
```sh
git push origin master
```

##### Tagging
VCs use tags to tag specific points in history as being important. They're commonly used to mark release points. Use the **git tag** to list all available tags.
```sh
git tag 
```

There are 2 kinds of tags you can create: **lightweight** and **annotated**. Lightweight tags are just pointers to a specific commit--like branches that don't change. Annotated tags contain more info such as the tagger name, email, date, and have a tagging message.

To create an annotated tag:
```sh
git tag -a v1.4 -m "my version 1.4"
git tag [tag-name] [commit-checksum]
```

You can then view the info of a specific tag, showing the tag message, tagger name, and the changes of that specific snapshot.
```sh
git show v1.4
```

If you want to create a lightweight tag instead, don't specify the `-am` flags. Running `git show` with lightweight tags just shows the changes of the commit.
```sh
git tag v1.4
```

By default, `git push` doesn't push tags up to the remote server. You have to explicitly share a tag either one by one or all at once.
```sh
git push origin [tag-name]
git push origin --tags # push all tags to the remote
```

You also can't checkout a tag. If you want to recreate the snapshot in your current working directory, you have to create a branch from the tag.
```sh
git checkout -b [branch-name] [tag-name]
```

