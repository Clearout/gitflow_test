# Gitflow

This is a guide on how to work using the Gitflow workflow.

Further reading and more examples may be found here:

* [Atlassian Gitflow](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow)
* [Original blogpost for Gitflow](https://nvie.com/posts/a-successful-git-branching-model/)

Using the Gitflow Git extension could be a helpful tool,\
however the usage and examples doesn't require this.

* [Gitflow Git extension](https://github.com/nvie/gitflow)

## <a name="contents"></a>Contents

* [Contents](#contents) and [Overview](#overview)
* [Feature branches](#feature-branches)
* [Hotfix branches](#hotfix-branches)
* [Release branches](#release-branches)
* [Git configuration](#git-configuration)

## <a name="overview"></a>Overview

In the Gitflow workflow there are two main branches, **develop** and **master**.\
The **master** branch tracks the official release history of the repository,\
while the **develop** branch tracks the development history of the repository.

Changes are made in either a feature branch, or a hotfix branch.\
A release branch is used to bring features into production.

All these branches have simple rules determining when they are created and\
where they may be merged into. This guide will detail the lifecycle and rules\
of each branch type, including step by step examples and some recommended git\
configuration.

## <a name="feature-branches"></a>Feature branches

### Usage

When making changes like adding a new feature or some non-critical bugfix,\
feature branches should be used.

>Create a feature branch from **develop**.\
>Make changes, commit and push them.\
>Remember to link the feature branch to the trello card for the task.\
>When the changes are complete merge **develop** into the feature branch\
>and fix any conflicts.\
>Create a pull request on github from the feature branch to **develop**.\
>When the pull request is approved it will be merged into **develop**.\
>Then the feature branch will be deleted.

### Rules

Feature branches may only be forked from the **develop** branch.\
Feature branches may only be merged to the **develop** branch.

### Example

```sh
# Go to the develop branch.
git checkout develop

# Make a feature branch and go into it.
git checkout -b feature/my_feature_branch

# Work on the changes, make commits and push them in this branch.
# Remember to link the feature branch to the trello card.
git add .
git commit -m "These are my changes"
git push -u origin feature/my_feature_branch

# When the changes are done, check if there are other new changes 
# in develop and pull the latest version. 
# Merge the latest version from develop into the feature
# branch and fix any conflicts.
git checkout develop
git pull --rebase
git checkout feature/my_feature_branch
git merge --no-ff develop

# Make a pull request on github. 
# Remember to add a link to the trello card and a
# description of the changes made in the pull request.
#
# After a code review the pull request may be approved, 
# and the feature branch will be merged into the develop branch.
# Then the feature branch will be deleted.
```

## <a name="hotfix-branches"></a>Hotfix branches

### Usage

Something in production is on fire and needs to be fixed now.\
This is a bugfix that can’t wait until the next release.\
This is when to use a hotfix branch.

> Create a hotfix branch from **master**.\
> Make the changes, commit and and push them.\
> Remember to link the trello card for the hotfix to the hotfix branch.\
> When the hotfix is complete make a pull request to **master**.\
> When the hotfix is approved it will be merged into both **master** and **develop**.\
> Then the hotfix branch will be deleted.

###  Rules

Hotfix branches may only be forked from **master**.\
Hotfix branches may only be merged to **master** and **develop**.

### Example

```sh
# Go to the master branch.
git checkout master

# Make a hotfix branch and go into it.
git checkout -b hotfix/my_hotfix_branch

# Work on the hotfix, make commits and push them in this branch.
# Remember to link the hotfix branch to the trello card.
git add .
git commit -m "This is my hotfix."
git push -u origin hotfix/my_hotfix_branch

# When the hotfix is done make a pull request on github. 
# Remember to add a link to the trello card and a 
# description of the changes made in the pull request.
# 
# After a code review the pull request may be approved, 
# and the hotfix branch will be merged into master.
# 
# Now the hotfix has to be merged into develop, 
# either by making a new pull request to develop or directly. 
# If there are possible conflicts these may be
# fixed in the same way as in the feature branch example.
#
# Here's how to merge the hotfix branch to the latest version of develop.
git checkout develop
git pull --rebase
git merge -no-ff hotfix/my_hotfix_branch
git push

# Then the hotfix branch will be deleted.
```

## <a name="release-branches"></a>Release branches

### Usage

When there are enough new features in the **develop** branch\
to justify a new version release branches are used.\
When a release branch is created this starts a new release cycle.\
No new features should be added to the release branch.

> Create a release branch from **develop**.\
> Fix any bugs found by testing in this branch.\
> If a bug fix is large or many are working in the release branch\
> it may be prudent to fork the release branch similarly to how\
> feature branches are used in **develop**.\
> This branch may also be continually merged into **develop**\
> to add any bug fixes there.\
> When the release is deemed ready for production it will be merged\
> into both **master** and **develop**.

### Rules

Release branches may only be forked from the **develop** branch.\
Release branches may only be merged to **master** and **develop**.\
When a release branch is created no new features should be added to it.\

### Example

```sh
# Go to the develop branch.
git checkout develop

# Make a release branch and go into it.
git checkout -b release/0.1.0

# When bugs are found in testing, either commit them here,
# or fork the release branch and merge the fixes back when done.
# 
# When the release branch is deemed ready for production 
# make a pull request and merge it into master.
#
# The release branch also has to be merged back into the latest version of develop.
git checkout develop
git pull --rebase
git merge --no-ff release/0.1.0
git push

# When the release branch has been merged to both master and develop,
# the release branch will be deleted.
```

## <a name="git-configuration"></a>Git configuration

To keep the history of the workflow properly,\
certain global settings should be set in the repository.

Note that these setting are global and will apply to all repositories,\
the `--global` option may be dropped to only\
set the config in the current repository.

All merges should be made with the no fast forward option,\
especially merging to **master**.\
To always enable this in all branches, use this command.

```sh
git config --global --add merge.ff false
```

If the no fast forward option is set,\
the `--no-ff` option may be dropped from merge commands in the examples.\
Github has this option set by default when merging a pull request.

Another useful setting is to always rebase on pull.\
This is to keep history cleaner with fewer merge commits.

```sh
git config --global pull.rebase true
```

With this setting the `--rebase` option may be dropped\
from the pull commands in the examples.

To set git to always only push the current branch, instead of all branches,\
this option may be set.\
This will also remove the need to set the remote for each new branch made.

```sh
git config --global push.default current
```

With this setting the `-u branch/my_branch_type` options\
may be dropped from the push commands in the examples.
