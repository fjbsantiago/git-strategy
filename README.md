# git-strategy
A summary of how to organize and manage code versioning in projects using git

Based on the approach proposed by Vincent Driessen: http://nvie.com/posts/a-successful-git-branching-model/ 

## Branches

Infinite life branches

- master
- development

Limited life branches

- Feature branches
- Release branches
- Hotfix branches


### master
Main branch where the source code of HEAD always reflects a production-ready state.
When a commit to master is done, it should be tagged with a release version.

### development
When it reaches a stable point and is ready to be released, all of the changes should be merged back into master.

### feature
Exists as long as the feature is in development, but will eventually be merged back into develop or discarded.

May branch off from:
- develop
Must merge back into:
- develop
Branch naming convention:
- anything except master, develop, release-*, or hotfix-*

Starting new work in feature:
```
$ git checkout -b myfeature develop
```

Incorporating changes into develop:

```
$ git checkout develop
Switched to branch 'develop'
$ git merge --no-ff myfeature
Updating ea1b82a..05e9557
(Summary of changes)
$ git branch -d myfeature
Deleted branch myfeature (was 05e9557).
$ git push origin develop
```

### release
Created when develop (almost) reflects the desired state of the new release and all features that are targeted for the release-to-be-built must be merged in to develop at this point in time.
Allows for minor bug fixes, last minute adjustments and preparing meta-data for a release (version number, build dates, etc.)

May branch off from:
- develop
Must merge back into:
- develop and master
Branch naming convention:
- release-*

Creating a release branch:

```
$ git checkout -b release-1.2 develop
Switched to a new branch "release-1.2"
$ ./bump-version.sh 1.2
Files modified successfully, version bumped to 1.2.
$ git commit -a -m "Bumped version number to 1.2"
[release-1.2 74d9424] Bumped version number to 1.2
1 files changed, 1 insertions(+), 1 deletions(-)
```

Finish a release branch:

```
# Merge to master
$ git checkout master
Switched to branch 'master'
$ git merge --no-ff release-1.2
Merge made by recursive.
(Summary of changes)
$ git tag -a 1.2

# Merge do development
$ git checkout develop
Switched to branch 'develop'
$ git merge --no-ff release-1.2
Merge made by recursive.
(Summary of changes) 
$ git branch -d release-1.2
Deleted branch release-1.2 (was ff452fe).
```

### hotfix
Meant to prepare for a new production release, but an unplanned one. They arise from the necessity to act immediately.

May branch off from:
- master
Must merge back into:
- develop and master
Branch naming convention:
- hotfix-*

Creating a hotfix branch

```
$ git checkout -b hotfix-1.2.1 master
Switched to a new branch "hotfix-1.2.1"
$ ./bump-version.sh 1.2.1
Files modified successfully, version bumped to 1.2.1.
$ git commit -a -m "Bumped version number to 1.2.1"
[hotfix-1.2.1 41e61bb] Bumped version number to 1.2.1
1 files changed, 1 insertions(+), 1 deletions(-)
```

Finishing a hotfix branch

```
$ git commit -m "Fixed severe production problem"
[hotfix-1.2.1 abbe5d6] Fixed severe production problem
5 files changed, 32 insertions(+), 17 deletions(-)
$ git checkout master
Switched to branch 'master'
$ git merge --no-ff hotfix-1.2.1
Merge made by recursive.
(Summary of changes)
$ git tag -a 1.2.1
$ git branch -d hotfix-1.2.1
Deleted branch hotfix-1.2.1 (was abbe5d6).
```
