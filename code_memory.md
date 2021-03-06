---
description: >-
  We present the Git tool and the Github web-based service. We then apply by
  saving on Github the script written during the previous chapter.
---

# The code memory

{% hint style="info" %}
Information for trainers:

We present git on a simplified example by including the notion of branches which is a good development practice. We also link it to github because github offers many useful features for code development, the first of which is sharing. The proposed exercise is the transfer of the script from the previous session to each learner's github account. Then, for the following sessions, the teacher will recall the versioning in git/gihub to save each modification.
{% endhint %}

## Introduction

### The problem

Many of us think far too early that the current version of our report is the final version. If this statement is true for the current version, it quickly becomes obsolete with time with each change made. This becomes a problem when we can no longer identify the latest version  \(see an example with the [comic strip "FINAL.doc"](http://phdcomics.com/comics/archive.php?comicid=1531)\). 

![](.gitbook/assets/image%20%28125%29.png)

The same applies to computer codes \(script files\). And sorting files by creation date or size does not help to identify the latest version, or more accurately, the one that works. Example:

```text
$ ls
R_script_03-04-2018.R        R_script_final_modified2.R
R_script_17-06-2018.R        R_script_final.R
R_script_18-03-2018.R        R_script_OK.R
R_script_final_2018-09-20.R  R_script.R
R_script_final_final.R       R_script_really_final.R
```

### The solution: use a version control tool

Using a method to record successive \(or even parallel\) versions becomes vital for reproducible research. This is one of the 10 basic rules according to [Sandve _et al._](https://doi.org/10.1371/journal.pcbi.1003285): "Rule 4: Version Control All Custom Scripts".

![Version control all custom scripts](.gitbook/assets/image%20%2845%29.png)

### What? When? Why? Git & Github!

We present a version control tool: **git**. And his sidekick who allows the saving and sharing of codes through the net: **github**. 

The usefulness of these 2 solutions in science is well established. 

_"When it comes to reproducible science, Git is code for success ... and the key to its popularity is the online repository and social network, GitHub"_. J. Perkel, [Nature Index, 2018](https://www.natureindex.com/news-blog/when-it-comes-to-reproducible-science-git-is-code-for-success)

“_Most researchers are primarily collaborating with themselves,”_ \[Tracy\] Teal explains_. “So, we teach it from the perspective of being helpful to a ‘future you’."_

All we have to do is try them on!

In this session, we will present you the Git tool and the Github web-based service. Next, we will apply these two things to our own project by creating a Github repository and saving the code we built at the previous session.

## The Git tool

### Where find git?

Git is installed by default in a linux terminal \(the default terminal of Ubuntu / Mac OS or in the Ubuntu terminal you previously installed for Windows\). You can type `git --help` for help or `git --version` to get the version. It is also a good way to check that everything is installed correctly.

{% hint style="info" %}
For Windows users, you can also use this tool if you do not want to use the Ubuntu terminal: [https://git-scm.com/](https://git-scm.com/)
{% endhint %}

### Initialise Git

The first Git usage require 3 minimal steps: create a specific folder, define your identity, and initialise.

```bash
# Create a new folder
$ cd /path/to/the/new/folder
$ mkdir gitFolder
$ cd gitFolder

# Define your identity
$ git config --global user.name "Firstname Name"
$ git config --global user.email "me@mail.com"

# Initialise git
$ git init
```

### Using Git

We present some of the Git commands that we used for code versioning.

#### Has Git detected any changes? `git status`

```text
$ git statut

On branch master

No commits yet

nothing to commit (create/copy files and use "git add" to track)
```

Here, there is no change detected because we haven't do anything.

So as a first change, we create a new file, named `test.txt` \(either with an online editor as nano or directly with echo and the redirection command\):

```bash
$ echo "My first file on git" > test.txt
```

To see the contain of test.txt, use the `cat`bash command:

```bash
$ cat test.txt
My first file on git
```

​Has Git detected any changes? `git status`

```bash
$ git status
branch master

No commits yet

Untracked files:
(use "git add file..." to include in what will be committed)

    test.txt

nothing added to commit but untracked files present (use "git add" to track)
```

Yes it does! And Git also explains that we need do track the changes.

Finally, Git allows you to take a picture at a given time. Let's take the example of the family photo at the Sunday meal to illustrate the rest.

#### Inform git to track files? `git add`

With `git add` , you will specify "who" will be in the picture. For example, you only want to see the  children. So you're going to tell all the kids to come to the picture \(`git add`\). It is the same principle for files. You specify which ones you want to version. Here, you want to version the file `test.txt` \(look the last line\):

```bash
$ git add test.txt
$ git statut
On branch master

No commits yet

Changes to be committed:
  (use "git rm --cached file..." to unstage)

        new file:   test.txt
```

#### Inform git to keep changes? `git commit`

Now that the picture is taken, you have to put a caption on it. We use `git commit` for this. The `-m` option allows you to add a quick explanation of changes. 

```bash
$ git commit -m "My first commit"
[master (root-commit) b4a7d66] My first commit
1 file changed, 1 insertion(+)
create mode 100644 test.txt
```

You can check if commit pass:

```bash
$ git statut
On branch master
nothing to commit, working tree clean
```

Now, change the contain of the file under editor: add "Text modifications" and check the status:

```bash
$ git statut
On branch master
Changes not staged for commit:
	(use "git add file..." to update what will be committed)
	(use "git checkout -- file..." to discard changes in working directory)

				modified:   test.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

Git detects changes. 

#### How to see the differences? `git diff`

Git is able to identify all the differences between two files and highlight them.

```diff
$ git diff
The file will have its original line endings in your working directory.
diff --git a/test.txt b/test.txt
index cd1ad34..32a6825 100644
--- a/test.txt
+++ b/test.txt
@@ -1 +1,3 @@
-My first file on git
+My first file on git
+
+Text modifications
```

#### The changes cycle

Save the changes with a 3-steps cycle: i\) `git add myfile`  ii\) `git commit -m mycomment` iii\) `git status`

```bash
$ git add test.txt
$ git commit -m "My second commit"
[master (root-commit) b4a7d66] My first commit
1 file changed, 1 insertion(+)
create mode 100644 test.txt

$ git statut
On branch master
nothing to commit, working tree clean
```

#### How to list the commits history? `git log`

\(type "q" to quit\)

```bash
$ git log
commit bbea074d0b2f6335f99e2d54c6a7092adb08f337 (HEAD -> master)
Author: thomasdenecker thomas.denecker@gmail.com
Date:   Tue Nov 13 15:15:59 2018 +0100

    My second commit

commit b4a7d66afb8066bced7a0d6113ab26a519b1b6be
Author: thomasdenecker thomas.denecker@gmail.com
Date:   Tue Nov 13 14:49:29 2018 +0100

    My first commit
```

### Branches

Sometimes we want to test a small \(or large\) idea without changing the current project. We simply make a "copy" \(`branch`\) of the initial project where we can make the modifications. Thus, this has no impact on the main project.

#### How to check the actual working branch? `git branch`

```text
git branch
* master
```

The star indicates the current branch. Here it is the main branch of a project, its default name is `master`.

#### How to create a new branch? `git branch <newBranchName>`

Next, we create a new branch named "modification":

```text
$ git branch modification
```

But we have not yet move to these new branch.

#### How to move toward a branch? `git checkout <branchName>`

We move and check the moving \(look at the star\): 

```text
$ git checkout modification
Switched to branch 'modification'

$ git branch
  master
* modification
```

We change something in the "modification" branch \(_eg._ we add "Modification in branch"  at the end of the `test.txt`file\):

```text
$ cat test.txt
My first file on git

Text modifications

Modification in branch
```

#### How to see the differences between the main branch and the working branch? `git diff master`

```bash
$ git diff master
warning: LF will be replaced by CRLF in test.txt.
The file will have its original line endings in your working directory.
diff --git a/test.txt b/test.txt
index 32a6825..ea7aaba 100644
--- a/test.txt
+++ b/test.txt
@@ -1,3 +1,6 @@
My first file on git

Text modifications
+
+Modification in branch
+
```

We keep the changes \(add, commit\) and check the history \(log ; type "q" to quit\):

```text
$ git add test.txt

$ git commit -m "Change in branch"
[modification c334c8b] Change in branch
	1 file changed, 3 insertions(+)

$ git log
commit c334c8b34368e9be690b9884d9ba18e8c64407c1 (HEAD -> modification)
Author: thomasdenecker thomas.denecker@gmail.com
Date:   Tue Nov 13 16:01:08 2018 +0100

    Change in branch

commit bbea074d0b2f6335f99e2d54c6a7092adb08f337 (master)
Author: thomasdenecker thomas.denecker@gmail.com
Date:   Tue Nov 13 15:15:59 2018 +0100

    My second commit

commit b4a7d66afb8066bced7a0d6113ab26a519b1b6be
Author: thomasdenecker thomas.denecker@gmail.com
Date:   Tue Nov 13 14:49:29 2018 +0100

    My first commit
```

As everything is going well, now we want to transfer the change to the main branch. 

#### How to transfer changes from one branch to the main branch?  `git merge <branchName>`

We join the master branch \(`checkout`\), and `merge` the "modification" branch. If we no longer need the branch, we can delete it \(`branch` with option `-d`\):

```text
$ git checkout master
Switched to branch 'master'

$ git merge modification
Updating bbea074..c334c8b
Fast-forward
test.txt | 3 +++
1 file changed, 3 insertions(+)

$ git branch -d modification
Deleted branch modification (was c334c8b).
```

We check the changes are included in the master branch:

```text
$ git statut
On branch master
nothing to commit, working tree clean

$ cat test.txt
My first file on git

Text modifications

Modification in branch
```

We now how to manage and control our different versions of code files. One next step will be to share our code with others through the net. 

## GitHub

We want to use a remote repository to upload our code files. A remote repository has the advantage of allowing access to our files from any workstation that has net access. This is for you but also for all the people you want to involve. For this objective, we choose "GitHub", a Microsoft web-based hosting service for version control using Git. 

![The GitHub logotype](.gitbook/assets/image%20%28162%29.png)

### GitHub initialization : account & repository

Like all traditional computer resources, a few steps before using GitHub for your project are required: 

* first if you don't have a GitHub account yet, initialize one \(the private gihtub account is unlimited for academics\). Follow the GitHub instruction to initialize your GitHub account here: [https://github.com/](https://github.com/)
* and next create a new repository **on the GitHub space**

For the rest of the presentation, we will use the GitHub account named "thomasdenecker" and the "DemoGit" directory. To access this remote directory, GitHub offers an URL that is composed as follows:

```text
https://github.com/thomasdenecker/DemoGit.git
```

### Associate the local and the remote repositories

We have to associate our local git repository to our new remote repository on GitHub. From our local git repository, we change the "origin" point \(`remote`\) and next we drop all files from the local repository to the remote repository \(`push`\).

```text
$ git remote add origin https://github.com/thomasdenecker/DemoGit.git

$ git push -u origin master
Counting objects: 9, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (4/4), done.
Writing objects: 100% (9/9), 784 bytes | 261.00 KiB/s, done.
Total 9 (delta 0), reused 0 (delta 0)
remote:
remote: Create a pull request for 'master' on GitHub by visiting:
remote:      https://github.com/thomasdenecker/DemoGit/pull/new/master
remote:
To https://github.com/thomasdenecker/DemoGit.git
 * [new branch]      master -> master
Branch 'master' set up to track remote branch 'master' from 'origin'.
```

### The changes cycle with a remote repository

With a remote repository, the cycle to manage changes becomes: "i\) `git add myfile`, ii\) `git commit -m "mycomment"`, and iii\) **`git push origin master`**". Here, an example of cycle of change with creating a new file \(with the bash`cat` command\):

```text
$ cat test.txt
My first file on git

Text modifications

Modification in branch

Changes for Github

$ git add test.txt

$ git commit -m "My commit"

$ git push origin master
Counting objects: 3, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 302 bytes | 302.00 KiB/s, done.
Total 3 (delta 1), reused 0 (delta 0)
remote: Resolving deltas: 100% (1/1), completed with 1 local object.
To https://github.com/thomasdenecker/DemoGit.git
   c334c8b..aefd13a  master -> master
```

![Development cycle with GitHub](.gitbook/assets/fair_github_devcycle.png)

If we reuse the analogy with the photograph of the family meal, it is possible to associate the stages of the development cycle in this way:

* choose the people on the final photograph: add the files to be versioned \(`git add <files>`\)
* once the photograph is printed, reminder the date and the names of the people: add comments \(`git commit -m "comment"`\)
* paste the photograph into the photo album: save all this in the repository \(`git push origin master`\)

### Working with collaborators

GitHub offers many functionalities to work with collaborators. We select a few of them and present you.

#### How to add a collaborator? =&gt; in the "Settings" tab

![](.gitbook/assets/image%20%2892%29.png)

#### **H**ow to catch the changes made by collaborators? `git pull origin master`

**\(**eg. your collaborator has created a README.md file that you want to retrieve\)

```text
$ git pull origin master
remote: Enumerating objects: 4, done.
remote: Counting objects: 100% (4/4), done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (3/3), done.
From https://github.com/thomasdenecker/DemoGit
 * branch            master     -> FETCH_HEAD
   aefd13a..c17f8f4  master     -> origin/master
Updating aefd13a..c17f8f4
Fast-forward
 README.md | 1 +
 1 file changed, 1 insertion(+)
 create mode 100644 README.md
```

#### How to describe a problem? or to propose a code change?

Sometimes when using the code of a GitHub project, you detect a problem. The thing to do is to inform the developers team. Use the GiHub tab "Issue" to describe the problem you are face on. Here is a screen capture of this "Issue" tab:

![](.gitbook/assets/image%20%28129%29.png)

If you have explored the code and you have a proposition of change, you may use the "Pull requests" tab to inform the team of your suggestion:

![](.gitbook/assets/image%20%28178%29.png)

Perhaps the suggestion will not be retained by the team of developers because of a different vision but, with such fair-play actions, the codes should become better and better. And this is good!

#### Manage your project: planning & wiki

Github may help you to manage your project, defining tasks and temporal plan,  follow it progress, etc. These helps stand in the "Projects" tab:

![](.gitbook/assets/image%20%28171%29.png)

The result of your planning is organized in a three part-desk: tasks done, tasks in progress, and tasks to do:

![](.gitbook/assets/image%20%28181%29.png)

The tab "Wiki" offers you to create a wiki associated to your project:

![](.gitbook/assets/image%20%28179%29.png)

And after a click on "Create the first page" and few changes you will have a wiki page:

![](.gitbook/assets/image%20%28143%29.png)

#### How to retrieve another project?  `git clone`

You can copy all the codes accessible from GitHub thanks to their associated URL \(clone\):

```text
$ git clone https://github.com/thomasdenecker/bPeaks-application.git
Cloning into 'bPeaks-application'...
remote: Enumerating objects: 21, done.
remote: Counting objects: 100% (21/21), done.
remote: Compressing objects: 100% (19/19), done.
remote: Total 616 (delta 10), reused 4 (delta 2), pack-reused 595
Receiving objects: 100% (616/616), 116.58 MiB | 4.08 MiB/s, done.
Resolving deltas: 100% (199/199), done.
Checking out files: 100% (210/210), done.
```

This command "git clone" will create a local repository \(eg. named "bPeaks-application"\) where you can find and execute the code of the copied project.

## Good practices

### What do you put in a Git repository?

Files with which git can calculate the difference between two versions. Most often it concerns text files of **reasonable size**:

* an R script ✅
* a Python script ✅
* a PDF file ❌
* a fastq file ❌

### Not so simple...

![https://xkcd.com/1597/](.gitbook/assets/image%20%28172%29.png)

Talking "git" is not so simple but once conquered, it is impossible to do without it!

## Applying Git/Github to the FAIR\_bioinfo project

In the previous session "It's not magic", we created a script, _i.e_. code. A best practise for repoducibility is to save its different versions into a version control tool. We choose Git & Github and we now follow these steps: i\) create a GiHub repository for this project \("Fair\_project"\), ii\) connect to git & github, iii\) select the files to save and share, iv\) save.

### Create the GitHub repository 

On our Github account, create a new repository:

![Screen capture : create a new repository on the GitHub page](.gitbook/assets/image%20%28154%29.png)

### Connect git & github

Open a terminal and go \(with `cd` bash command\) to the repository `FAIR_project` we created during the previous "It's not magic" session:

```text
tdenecker@DESKTOP-B8ON598:~$ cd Fair_project/
tdenecker@DESKTOP-B8ON598:~/Fair_project$
```

Connect Git & Github with the local repository, following the Github proposition \(in the URL, change the "thomasdenecker" mention by your github login\):

```text
echo "# Fair_project" >> README.md
git init
git add README.md
git commit -m "first commit"
git remote add origin https://github.com/thomasdenecker/Fair_project.git
git push -u origin master
```

### Save & share the script

Checkin the `git status` on the terminal displays a list of files or repositories that we could add to the github depot. Git call them "untracked files":

```text
tdenecker@DESKTOP-B8ON598:~/Fair_project$ git status
On branch master
Your branch is up to date with 'origin/master'.

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        Fair_script.sh
	      Project/
	      contitions.txt


nothing added to commit but untracked files present (use "git add" to track)
```

But we do not want the `Project` repository to be stored on github because it contains data that are too heavy and can be private. To prevent its save, we add a `.gitignore` file that will contain all files or repositories we want to exclude from github. Each line of the `.gitignore` file correspond to one file or repository to exclude. With the text editor you prefer \(`nano` in the example\) and place the `Project` repository into a `.gitingnore` file:

```text
tdenecker@DESKTOP-B8ON598:~/Fair_project$ nano .gitignore
tdenecker@DESKTOP-B8ON598:~/Fair_project$ cat .gitignore
Project/
```

With `git status` check the correctness of exclusions from the `.gitignore` file:

```text
tdenecker@DESKTOP-B8ON598:~/Fair_project$ git status
On branch master
Your branch is up to date with 'origin/master'.

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        .gitignore
        Fair_script.sh
	      contitions.txt


nothing added to commit but untracked files present (use "git add" to track)
```

The `Project` repository disappeared from the list of possible untracked files while the `.gitignore` file appears. 

### Save on github

We are ready to save on the github repository. We apply the git 3-steps cycle, add, commit, and save: 

```text
tdenecker@DESKTOP-B8ON598:~/Fair_project$ git add .
tdenecker@DESKTOP-B8ON598:~/Fair_project$ git commit -m "Init Fair_script.sh - architecture"
[master 2d2e31b] Init Fair_script.sh - architecture
 1 file changed, 11 insertions(+)
 create mode 100644 Fair_script.sh
tdenecker@DESKTOP-B8ON598:~/Fair_project$ git push origin master
Username for 'https://github.com': thomasdenecker
Password for 'https://thomasdenecker@github.com':
Counting objects: 3, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 392 bytes | 130.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0)
To https://github.com/thomasdenecker/Fair_project.git
   61310c5..2d2e31b  master -> master
```

{% hint style="danger" %}
Here we already had a repository to import to github. But, the best practice is rather to start by creating the git framework before creating the script that structures the project.
{% endhint %}

## Conclusion

In this chapter we have seen how to version our code files and how to share them with the developer community. We applied this versioning to the code from the previous chapter \(code for creating the project architecture and uploading input files\).

## It's up to you

{% hint style="success" %}
If not done during your reading, repeat the command lines of the last part of this session, "Applying Git/Github to the FAIR\_bioinfo project". For the next session, you should have your `FAIR_script.sh,` `conditions.txt` `README.md`, and `.gitignore` files saved in your Github depot.
{% endhint %}

## Resources

* [Version Control with Git](https://swcarpentry.github.io/git-novice/) : a Software Carpentry lesson
* [Git & GitHub](http://cupnet.net/git-github/) : the "Pierre Poulain" course \(in french\)
* [One paper](https://journals.plos.org/ploscompbiol/article?id=10.1371/journal.pcbi.1004947) : Ten Simple Rules for Taking Advantage of Git and GitHub
* [Video](https://youtu.be/hPfgekYUKgk) : Start with Git and Github in 30 minutes \(in french\)



