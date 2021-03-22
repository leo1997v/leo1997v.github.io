---
layout:     post
title:      Git Commands
subtitle:   Some basic Git commands
date:       2021-03-18
author:     Leo
header-img: img/post-bg-os-metro.jpg
catalog: true
tags:
    - Git
---

>List of some basic Git commands for reference. 


# Create a new Repository

	echo "# projectName" >> README.md
	git init
	git add README.md
	git commit -m "first commit"
	git remote add origin git@github.com:leo1997v/projectName.git
	git push -u origin master

Push an existing repository

	git remote add origin git@github.com:leo1997v/test.git
	git push -u origin master


# Common Operations

#### Create a local repository（initialize）
	Create in current directory:
	git init
	
	Create a new repository directory:
	git init [project-name]
	
	Clone a remote project:
	git clone [url]
	
#### Add files to staging

	Add all files changed:
 	git add .

	Add one file:
	git add [file name]

#### Configure

	Set user information:
	git config [--global] user.name "[name]"
	git config [--global] user.email "[email address]"
	
	
#### Commit
	Commit changes in staging area to local repository
	git commit -m "msg"
	
	# Commit specified file to local repository
	$ git commit [file1] [file2] ... -m [message]
	
	# Commit any files you've added or changed since then
	$ git commit -a
	
	# Show all differences when committing
	$ git commit -v
	
	# Replace the last commit with a new commit
	# If there's no change on the codes, rewrite the commit message
	$ git commit --amend -m [message]
	
	# Re do the last commit, including new changes in specified files
	$ git commit --amend [file1] [file2] ...
	
#### Remote synchronization

	# fetch all changes in remote repository
	$ git fetch [remote]
	
	# List all currently configured remote repositories
	$ git remote -v
	
	# show information of certain remote repository
	$ git remote show [remote]
	
	# add a new remote repository and name it
	$ git remote add [shortname] [url]
	
	# fetch the changes in remote repository and merge it to certain local branch
	$ git pull [remote] [branch]
	
	# upload certain local branch to remote repository
	$ git push [remote] [branch]
	
	# Forcibly push the current branch to the remote repository, 
    # even if there is a conflict.
	$ git push [remote] --force
	
	# push all branches to remote repository
	$ git push [remote] --all


	
#### Branch

	# List all local branches
	$ git branch
	
	# List all remote branches
	$ git branch -r
	
	# List all local and remote branches
	$ git branch -a
	
	# Create a new branch, but still in current branch
	$ git branch [branch-name]
	
	# Create a new branch and switch to it
	$ git checkout -b [branch]
	
	# create a new branch，指向指定commit
	$ git branch [branch] [commit]
	
	
	# Switch from one branch to another
	$ git checkout [branch-name]
	
	# switch to last branch
	$ git checkout -
	
    # create a new branch to track specified remote branch
	$ git branch --track [branch] [remote-branch]
	
    # create track between current branch with specified remote branch
	$ git branch --set-upstream [branch] [remote-branch]
	
	# merge specified branch into current one
	$ git merge [branch]
	
	# choose a commit and merge it to current branch
	$ git cherry-pick [commit]
	
	# delete a branch
	$ git branch -d [branch-name]
	
	# delete remote branch
	$ git push origin --delete [branch-name]
	$ git branch -dr [remote/branch]
	
#### Tags

	add a tag to current commit
	git tag -a v1.0 -m 'xxx' 
	
	add a tag to specified commit
	git tag v1.0 [commit]
	
	check tags
	git tag
	
	delete a tag
	git tag -d V1.0
	
	delete a remote tag
	git push origin :refs/tags/[tagName]
	
	push
	git push origin --tags
	
	fetch
	git fetch origin tag V1.0

	create a new branch, pointing to a tag
	git checkout -b [branch] [tag]

#### Check Information

	# Show all files with changes, which haven't been added to staging
	$ git status
	
	# show version history of current branch 
	$ git log
	
	# show history of commit, and the changes in files
	$ git log --stat
	
	# search commit history according to keyword
	$ git log -S [keyword]
	
	# show all changes after specified commit, a commit for each line
	$ git log [tag] HEAD --pretty=format:%s
	 
	# show all changes after specified commit, whose 
    # description must conform to searching conditions
	$ git log [tag] HEAD --grep feature
	
	# show version history of specified file, including renaming operations
	$ git log --follow [file]
	$ git whatchanged [file]
	
	# show each diff of specified file 
	$ git log -p [file]
	
	# show the last 5 commits
	$ git log -5 --pretty --oneline
	
	# show all users who have committed, sorted by committing times
	$ git shortlog -sn
	
	# show who and when change the specified file
	$ git blame [file]
	
	# check differences between staging and working area
	$ git diff
	
	# check differences between staging and repository after the last commit
	$ git diff --cached [file]
	
	# show differences between working area and current branch after the last commit
	$ git diff HEAD
	
	# show differences between two commits
	$ git diff [first-branch]...[second-branch]
	
	# show lines of codes written today
	$ git diff --shortstat "@{0 day ago}"
	
	# show information of specified commit and the differences from the last commit of it
	$ git show [commit]
	
	# show the files changed on specified commit
	$ git show --name-only [commit]
	
	# show the content of specified file on specified commit
	$ git show [commit]:[filename]
	
	# show recent commit on current commit
	$ git reflog
	
#### Undo
	
	# reset specified file from staging to workspace
	$ git checkout [file]
	
	# reset specified file to staging and workspace on specified commit
	$ git checkout [commit] [file]
	
	# reset all files in staging to workspace
	$ git checkout .
	
	# reset specified file in staging, making it 
    # as same as the last commit, but doesn't change workspace
	$ git reset [file]
	
	# reset staging and workspace to the last commit
	$ git reset --hard
	
	# set current branch pointer to certain commit, 
    # reset staging but keep workspace unchanged
	$ git reset [commit]
	
	# reset HEAD of current branch to specified commit, # and reset staging and workspace to specified commit
	$ git reset --hard [commit]
	
	# reset current HEAD to specified commit，keep staging and workspace unchanged
	$ git reset --keep [commit]
	
	# roll back specified commit and cover the last one with it
	$ git revert [commit]
	
	# remove the changes haven't been committed temporary, pop it later
	$ git stash
	$ git stash pop
	
#### Others

	# generate a deliverable RAR 
	$ git archives