# Git Command Line Basics

## About git

Git is a **version control system**.
It was initially written by Linus Travalds (the original author of Linux) and first released in 2005.

Accoring to wikipedia, The 2015 Stack Overflow developer survey reported that 69.3% of developers use git.

## Installing git

### Windows

On windows, you can download and install *Git for Windows*, or use *chocolaty* to install it (personally, I prefer to use chocolaty for installing anything it has a script for).

If presented with install options, I recommend selecting the following: "Use Git and optional Unix tools from the Windows Command Prompt",
"Checkout as-is, commit Unix-Style line ending",
"Use MinTTY", and
"Enable file system cashing".

*Git for Windows* comes with a git bash shell that is SIGNFICANTLY more useful than the windows command line.  Think of it as the windows command prompt with support for various unix commands like *ls*, and *cp*, as well as supporting *colored text* and *tab completion*.  I recommend using the git bash shell for your git commands rather than command shell or powershell, or whatever.

I recommend also installing a decent merge tool, like p4merge or meld (both of which can be downloaded and installed manually, or by simply using chocolaty).



### Mac
On mac, git comes with xcode.

## Thinking in Git

In order to use git effectivly, you must first understand the workflow that files move through within git.

### Files on your computer
When you create, delete, or edit files on your computer (in a respository folder) the only copy of those changes is the files themselves.  In git lingo, this is often refered to as your **Working Directory**.

### Staged files (still on your local computer)
When you "git add" files, you haven't yet "saved" them in any way, you have merely flagged them to be saved.  This is called "staging" them.  Local changes that have been added are considered to be **staged** for commit.  Git lingo sometimes calls the staging area the **index**.  So, files are "added to the index".

### Local Repository (still on your computer)
When you "git commit" files, you create a permenant checkpoint.  This is like securing your rope while rock climbing, it saves your current snapshot with a name that can be used to revert back to at any point you want.

Its important to note, that commited files are still only on your local computer, so if your hard drive crashes, you can still lose everything.

### Remote Repository (not on your computer)
You can syncronzie your local repository history with a remote repository.  Git lingo calls the remote repository "**origin**".

You use "git push" to syncronize changes from your local system to the origin repository.

You use "git pull" to synchronize changes from origin down to your local copy.

Technically, having a remote repository is *optional*.  Meaning, you can have a completely local and perfectly functional git repository.  This scenario makes it hard to share code with other developers, and it isn't safe in terms of hardware failure, but it does track code changes and allow you to branch and merge and do all the other things git does.

Also, its worth noting that a local repository is not permenantly connected to origin.  You can "git set" your repository to point at a different origin whenever you want. 


### The basic workflow of git is as follows:
1. Make changes to your **Working Directory**.
2. "**Add**" those changes to the **index** (a.k.a. "staging area").
3. "**Commit**" a set of changes (or "change set") to your local repository
4. "**Push**" your changes to a remote repository (a.k.a. "**origin**") so that others have access to them. 

## Working with your local changes
Git doesnt care what you use to modify the files in your working directory.  In other words, you can use whatever you want to add/remove/edit files locally.

### Discovering what has changed

	git status

You can use "git status" to see what files have changed, and what has been staged for commit.


	git diff

You can use "git diff" to see line by line changes in every file that has changed.


	git difftool

If the command line diff notation is unreadable for you, you can use a gui difftool.
	
Git supports several diff viewers, here's some more information on [setting up a custom difftool](https://stackoverflow.com/questions/6412516/configuring-diff-tool-with-gitconfig).  I personally prefer using *p4merge* as my difftool and mergetool.

### Reverting unwanted changes

	git reset --hard
You can reset your entire Working Directory back to the most recent commit on the current branch (which is called the "HEAD" of the branch) using "git reset --hard".

	git checkout -- filename.txt
You can reset a **specific file** back to whatever it was at the HEAD of the current branch using "git checkout -- filename.txt".  This leave everything else in your Working Directory just like it is, and reverts that single file back to whatever it was.

	git checkout master -- filename.txt
Its also possible to grab a specific file from another branch (in this case "master") and use that copy to overwrite whatever you have in that file in your working directory. 
	


## Staging files
	git add filename.txt
	
you add files to the staging area using "git add", but doing so is somewhat tedious, so ...

	git add --all
Its common to want to add all of changes in an entire directory (and its subfolders).  to do that, go to that directory and "git add --all"

	git add --all :/
Its also common to want to add all of the changes in the entire Working Directory, without having to move to the base folder of your repository.  This can be done using **:/** which is shorthand for "the root of my Working Directory for this repository".  Similar to **~** being shorthand for a user's home folder, or **.** being an alias for the current folder, or **..** an alias for the parent of the current folder.
  
*NOTE:* when you git add --all, it will (thankfully) NOT add things you have told it to ignore using the .gitignore file.  More on git ignore [here](https://help.github.com/articles/ignoring-files/).

	git status
	git add --all :/

generally I use "git status" to make sure I havent changed something inadvertantly, and then "git all --all :/".

Very seldom do I add individual files or folders one by one.  However, it is good to at least be aware of what you CAN do because that increases your understanding of what is really going on.

## Committing locally
	git commit

The simplest way to commit your staged changes is simply "git commit". However, this will open up a **vi** editor asking you to document the changes you are committing.  If you come from a Windows background, like me, the vi editor is like kryptonite.  You can avoid being prompted with vi by specifying a message as part of your commit, like so...

	git commit -m 'message text'
	
I personally use a jira ticket number as the entire text of my commit message.  This is convenient, and (in my opinion) a wonderful way to do it.  However, I will admit that a lot of people think that's a cop out, because you cant guarantee that the ticket system you use today will still be available years down the road when people have question about your code.  If you have to write detailed commit messages, the vi editor option is going to be easier than the -m option.  

	git config --global core.editor "nano" 

If you hate vi but have to provide detailed commit messages ... use the above command to swap out the default text editor used by git, more discussion on that [here](https://stackoverflow.com/questions/2596805/how-do-i-make-git-use-the-editor-of-my-choice-for-commits).

## Syncronizing with a remote "origin"
*NOTE*:  "origin" was a terrible name for the remote server.
Because, while it is often the place where you got your files, that isn't always the case.  In fact, the natural concept of "origin" as being the place where the files origianlly came from is completely distorted, and can only cause confusion.  This is because at any time you can "set" your origin repository to point wherever you want, or to no place at all.  Worse still, you can create a local repository with no "origin", and not even add an origin until you decide to publish your files somewhere.  

So, it is important to know that "origin" doesnt mean "original source of files".  It means "remote git server I am currently set to synchronize with".

	git clone /path/to/remote/repo
To initialize a local git repository by cloning a project on some git server, you use the git clone command.  When you do this, the remote server is automatically set up as your "origin".  And, the current snapshot of the repository is automatically "pulled".

	git pull
You can use git pull to download existing changes that are on the server, but haven't yet been applied to your local repository. 

	git push
you can use git push to take any changes that you have committed on the current branch and push them to the origin branch.   IF someone else has pushed changes to the origin, and your local copy is out of date, then you will be forced to pull (and possibly merge) before you will be allowed to push your changes.

	git remote add origin http://IP/path/to/repository
If you created your repository using "git init", and therefor have no "origin" and would like to point it at a remote server, you can do that with the "git remote add origin" command.

	git remote set-url origin https://github.com/username/repo
at any time, you can up and decide to point at a different server as your origin.  you can do that with the "git remote set-url origin" command.  More details on that [here](https://stackoverflow.com/questions/7259535/setting-up-a-git-remote-origin).


## Branching
In very small programming teams, you might get away with always working on the "master" branch.  However, using named branches is a more excelent way.  Git uses *diff based* branching (as opposed to full copy branching) which means that creating a branch has almost zero overhead.

	git checkout -b my-branch
To start a totally new branch named my-branch, you use the command above.  note the **-b**, which means "start a new branch" based on the current branch.

	git checkout anther-branch
To move from my-branch to another-branch you use git checkout without the -b switch.  Whether you create a new branch, or merely switch between existing branches, any uncommited changes to files in your Working Directory will move with you.  If you dont want that, consider commiting those changes, or stashing them if you arent ready to commit (stashing is discussed below).
 
	git checkout target-branch
	git merge source-branch
	
when you want to merge one branch into another, you start by checking out the target-branch (making it the current branch), and then merging the source-branch in to the current branch.  If there are conflicts between the two, it will tell you to fix them, and commit the results of your merge.

## Merge conflicts

Sooner or later, you are going to have a merge conflict.

	git mergetool
When you have a conflict, you can use "git mergetool" to open up a gui merge tool, and manually merge the changes.

As an alternative you can just edit the files yourself.  The message that tells you there were merge conflicts will tell you what files had issues.  If you open those files, you'll find sections blocked off with stuff like >>>>>>>>> and <<<<<<<<<.  You have to fix those areas, and save your changes. 

	git commit -m 'message about how much you hate manual merging'
After you have merged all the conflicting files, you simply commit your changes using "git commit".



## Stashing stuff
Git has a "stash", which is a temporary place where you can, well, stash stuff that is in your way that you arent ready to commit, but you arent ready to just throw away either.

	git stash
you use "git stash" to put all the uncommited changes in the Working Directory to the "stash".  Once you do that, you can check out another branch, or ... whatever you need to do.  The stash will just sit there minding its own business while you do your thing.

	git stash apply
When you want to apply the changeset that you have stashed, and put those changes into the current branch, you do so with "git stash apply".  Its important to know that doing this wont commit those chagnes, it just brings them back into the working directory.  Its also worth nothing you can apply changes that were stashed on one branch to a totally different branch if you want.   In fact, its not uncommon for me to go in start making changes, and realize I'm not on the branch I intended to work on, so I stash my changes, checkout the right branch, and apply my stash to the correct branch. 

	git stash drop

Sometimes you decide that you do want to abandon stuff you stashed.  you can do that using "git stash drop".  

The stash is capable of being a [very complex thing](https://git-scm.com/docs/git-stash).  But I've only ever used it in the simple way described above. 

# Putting it all together:  a ticket centered workflow
In a software engineering environment, there is generally a ticketing system such as jira, redmine, trac, asana, or mantis.
New user stories and bugs and other requested changes receive tickets with details about what needs to be done. Those tickets generaly have a unique ticket ID assigned by the system when they are created (except for asana, which hides that internal id).

A git repository generally has a "master" branch that is expected to have the most up-to-date, release-ready code. A changeset that has not been tested, peer reviewed, or otherwise evaulated for release-readiness is not allowed to be merged into the "master" branch.

In order to satisfy that requriement, developers start a new branch for each ticket they intend to work on.  for instance, before starting work on ticket DEV-123, I would do the following:

	git status
	git checkout master
	git pull
	git checkout -b 'DEV-123'

I did "git status" just ot make sure I had no residual change sitting around that I didnt realize was there.  Then, I make sure I'm on "master" (or whatever branch is my starting point for this new branch).   I pull to make sure I have the latest stuff on the main server, and then "checkout -b" to make a branch named after my ticket.

really you could name the branch whatever you want, but the ticket number seems descriptive, unique, and suscinct.  So I use that. 

Then, the developer makes whatever changes need to be made in order to satisfy the ticket, when we think we are done, we use git status, git diff, and git add to verify we intended to change the stuff we changed, and then stage it for commit, and commit it.

	git status
	git difftool
	git add --all :/
	git commit -m 'changes to satisfy DEV-123'
	git push
	
The reason I included 'DEV-123' in the commit message is that many ticket systems will autmatically tie commits to tickets, if you include the ticket id in the message.

A peer can now review my branch on another computer ...
	
	git stash
	git pull
	git checkout DEV-123
	git difftool master
	git checkout THIER-BRANCH
	git stash apply
	
We used "git stash" here to avoid ruining whatever was being worked on before the code review.  we switch back to whatever we were working on, and use "git stash apply" to resume where we left off.

When the branch is good enough to merge into master someone will do that...

	git status
	git checkout DEV-123
	git pull
	git checkout master
	git pull
	git merge DEV-123
	git push
	
We started off using "git status" to make sure we dont have little uncommitted changes.  we pull the latest version of the branch we want to merge, and the latest version of master.  Then we merge the other branch into the master branch, and push the result out to origin.


## Contributing to open source projects using pull requests...
I have very little experience with pull requests, but it is a very standard way of doing things on github.
Here's some info on [how to do that](https://help.github.com/articles/about-pull-requests/).

