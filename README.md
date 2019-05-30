# 0. Notes
This guide is intended to accompany a Git workshop, but it should also be helpful on its own, especially if you have some familiarity with Git. If you don't, check out [this guide](https://git-scm.com/book/en/v1/Getting-Started-Git-Basics).

You may also find the diagrams in `diagrams.odp` useful for visualizing workflows. See part 8 for a recommended workflow and details.

Finally, I've included my personal Git cheat sheet and notes in `git_commands.xlsx`. Note that I added some of these entries years ago, so they might not all be accurate.

# 1. Bash crash course
Command | Purpose | Example 1 | Example 2
--- | --- | --- | ---
pwd | Print working directory (where you are now) | `pwd` |
man | get manual page for a command | `man pwd` | `man cd`
cd | Change directory | `cd Desktop` | `cd /home/mike/Desktop`
ls | List directory contents | `ls` | `ls –a (to include hidden files)`
echo | Display text (text often redirected to a file) | `echo 'hello world!'` | `echo 'blah' > file.txt`
mkdir | Make directory | `mkdir dir1` | `mkdir dir1 dir2`
rmdir | Remove directory | `rmdir dir1` | `rmdir dir1 dir2`
touch | Make file | `touch file1.txt` | `touch file1.txt file2.csv`
rm | Remove file or directory (include `-r` if directory; beware of `-f`!) | `rm file1.txt` | `rm –r folder1`
cp | Copy files and directories (include `-r` if copying folder) | `cp source/f1.csv destination` | `cp -r source/dir1 dest`
mv | Move or rename files and directories | `mv f1.csv ~/data`  | `mv f1.csv f2.csv`
CTRL/CMD + C | Abort whatever is happening | |

# 2. Creating a repo on GitHub
 1. Click account icon.
 2. Name and describe your repo.
 3. Make it public.
 4. Give it a readme, a .gitignore file for the primary language you intend to use, and a license (always use a license).
 5. Click "create repository".
 6. Click "Clone or Download", then copy the repo's URL to the clipboard.

# 3. Local Git setup
Verify that you have Git and it's updated.

`git --version`

Tell Git your name and email, so they can be associated with your commits.

```
git config --global user.name 'bruce willis'
git config --global user.email 'bwillis@hotmail.com'
```

Configure line ending management. On \*nix (Linux, OS X, UNIX, etc.), use the following config setting to always convert CRLF to LF, and never convert back.

`git config --global core.autocrlf input`

On Windows, use this setting to convert CRLF to LF when writing to the object database (e.g. when committing), and to convert LF to CRLF when writing to the working directory (e.g. when checking out a commit), so that Windows can still do its Windows thing without causing trouble.

`git config --global core.autocrlf true`

# 4. Single-player Git: version control only

### Clone remote repo (make a local copy)
Copy URL of repo you just made. Go to home directory. Clone repo there.

```
cd
git clone <url here>
```

Enter repo. List all files, including hidden files. Look inside the `.git` directory, which is where Git will store all data for this repo.

```
cd git_demo
ls -a
ls .git
git status
```

### Learn the Git mantra: `add`, `commit`, repeat (apply `status` liberally).
Write some code. Add changes to the staging area. Make your first commit.

```
touch 01_mainframe_infiltrator.R
echo 'some code' > 01_mainframe_infiltrator.R
cat 01_mainframe_infiltrator.R
git status
git add 01_mainframe_infiltrator.R
git status
git commit -m 'finished CIA hacking module'
git status
git log
```

Overwrite original code and add another line (`>` overwrites; `>>` appends). Write a second file. Recursively add all changes in the working directory to the staging area.

```
echo 'better code' > 01_mainframe_infiltrator.R
echo 'more code' >> 01_mainframe_infiltrator.R
cat 01_mainframe_infiltrator.R
touch 02_gratuitous_bloatware.crp
git status
git add .
git status
git commit -m 'fixed bug that caused NASA to be hacked instead of CIA. whoops ^_^'
```

Remove a file (note that `git rm` is not the same as `rm`). Add more files. Add a directory. Tell Git to ignore the directory, as well as any file with a ".png" extension.

```
git rm 02_gratuitous_bloatware.crp
touch 02_fingerprint_faker.html
echo 'elite code' > 02_fingerprint_faker.html
touch sweet_logo.png
mkdir high_school_poetry
touch high_school_poetry/what_is_life.doc
touch .gitignore
echo 'high_school_poetry' > .gitignore
echo '*.png' >> .gitignore
git status
git add .
git commit
"faked richard nixon's fingerprints; got into the mainframe"
git log
```

Check differences between commits (two different ways)

```
git diff <previous commit ID> <current commit ID>
git diff HEAD~1 HEAD
```

Make another change. Stash it while you go back and look at a previous commit. Return to the tip of the master branch (the only branch you have at the moment). Retrieve your changes from the stash.

```
echo 'tentative changes' >> 01_mainframe_infiltrator.R
git stash
git checkout HEAD~3
git checkout master
git stash apply
```

Push all changes, including full commit history, to remote repo on GitHub

`git push origin master`

# 5. Multiplayer Git: version control + collaboration

### Fork a repo on Github

### Clone it locally

### Make some changes; add-commit-push

### Initiate a pull request on GitHub

### Accept a pull request on GitHub

### Fix a merge conflict

###

# 6. Branching

Get list of branches (just master at the moment).

`git branch`

Add a new development branch. Make a commit to it. Push that commit to the GitHub, which will create a new 'dev' branch on GitHub to mirror the one you just made.

```
git checkout -b dev
touch 03_new_feature.py
echo 'code' > 03_new_feature.py
git add .
git commit -m 'finished feature X; not yet tested'
git push origin dev
```

Merge development changes to the master branch and push to GitHub.

```
git checkout master
git merge dev
git push origin master
```

# 7. Aliases
Aliases are shorthand commands that stand in for frequently used longer commands.

Here's one that prints a clean, informative git log (`git clog`):

`git config --global alias.clog "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cD) %C(bold blue)<%an>%Creset' --abbrev-commit"`

To list all the aliases you've created, use `git config --get-regexp alias`.

# 8. Recommended workflow
Check out `diagrams.odp` to visualize workflows. Let me know if it appears to render incorrectly on your machine.

I recommend the fourth workflow (El Jefe), in which there is one main repository on GitHub that everyone forks, and all team members have the privilege to accept pull requests into the main repo (everyone is el jefe). This ensures that all changes receive a visual check on GitHub before they are incorporated into the main repo. You can also configure each of your local repos such that nobody can push directly to the main, nor pull directly from their fork. This foolproofs the workflow. Here's how to do it:

```
git remote rm origin
git remote rm upstream
git remote add upstream <url for the main repo>
git remote set-url --push upstream DISABLE
git remote add origin DISABLE
git remote set-url --push origin <url for your forked repo>
```

You can see which URLs your local repo is connected to with:

`git remote -v`

