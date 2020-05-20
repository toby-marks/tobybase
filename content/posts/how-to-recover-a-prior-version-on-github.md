+++ 
date = "2020-05-19T17:09:28-05:00"
lastmod = "2020-05-19"
title = "How to Restore Files From a Previous Version in Git/Github"
categories = ["Tools"]
tags = ["git", "github", "source control"]
+++
### Get the hash of the "good" version you want to recover

You first need to identify where things went wrong. Then get the commit identifier (also called the hash) for the version immediately prior to that. (Also make sure you're pulling from the appropriate branch; for simplicity's sake, this example assumes all changes are being committed to the master branch.)

```
# git log

commit 07c5f535743e3f70fcdf09c0d032580820ca38c9
Author: Toby Base <xxxxx@gmail.com>
Date:   Tue May 19 14:33:17 2020 -0500

    Created content/post/my-newest-article.md
    (Oh no! Accidentally deleted some files after the last commit!)

commit 85add47b28670abaf99b17e010edeafb2fbb3189
Author: Toby Base <xxxxx@gmail.com>
Date:   Fri Feb 7 21:04:39 2020 +0000

    Updated content/post/oracle-cloud-day-dallas.md

commit c3a520e136dc90e7982bc50d56a33aa3c3885f85
Author: Toby Base <xxxxx@gmail.com>
Date:   Fri Feb 7 21:02:29 2020 +0000

    Created static/uploads/BC9419D3-B290-4CF3-845C-5C008A19419C.jpeg
```

In my case, I know I deleted files in the commit on May 19. So I want to restore those files from the version right before that, the one with hash `85add47b28670abaf99b17e010edeafb2fbb3189`. 

### Flash back to the state of the repository at the desired commit

Do this only if you don't care about eliminating commit history or losing changes since the specified commit. 

```
# git reset --hard 85add47b28670abaf99b17e010edeafb2fbb3189
```

Other options for `git reset` include `--soft` and `--mixed`, which retain the changed files in your working directory, but treat the states of those files differently. A more thorough explanation of how `git reset` works [can be found here](https://www.atlassian.com/git/tutorials/undoing-changes/git-reset).

### Recover files while retaining the commit history

```
# git checkout -b new_branch 85add47b28670abaf99b17e010edeafb2fbb3189
``` 

The checkout command will revert any unchanged files and restore any deleted files as of the specified commit. New and changed files since that time are retained. We fork a new branch in order to avoid the "detached HEAD" state, discussed in the [above linked article](https://www.atlassian.com/git/tutorials/undoing-changes/git-reset), that could lead to problems on future commits.  

If you were working on the master branch and want to continue committing changes on that branch, simply commit your changes on the new branch, switch back to the master, and merge in your changes.

```
# git add -A
# git commit -m "Restoring files from previous commit"
# git checkout master
# git merge new_branch
```

Here `checkout master` switches us back to the master branch, and then we use `merge new_branch` to restore the lost files to the master.

### Another option just to recover the files

The simplest way to get your files back without dealing with the branching and merging is to checkout a prior version in a temporary directory, then copy the missing files from there back to your working directory. Then just add and commit those files back to source control.

```
# cd /tmp
# git clone https://github.com/toby-marks/tobybase.git
# cd tobybase
# git checkout 85add47b28670abaf99b17e010edeafb2fbb3189
# cp content/posts/*.md /path/to/tobybase/working/directory/content/posts/
# cd /path/to/tobybase/working/directory
# git add -A
# git commit -m "Restoring files lost after 85add47b28670abaf99b17e010edeafb2fbb3189"
```
