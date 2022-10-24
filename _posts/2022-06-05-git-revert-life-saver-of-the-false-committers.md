---
layout: post
title: "Git Revert: Life Saver of the False Committers"
description: "Git Revert: Life Saver of the False Committers"
img_path: /assets/img/posts/2022-06-05-git-revert-life-saver-of-the-false-committers
image:
  path: single-revert.png
categories: [DevOps, Git]
tags: [git, cloud]
date: 2022-06-05 16:00 -0400
---

## Git Revert: Life Saver of the False Committers

If you ever pushed a change that was not supposed to get merged to the remote repository(especially to the main branch), you were most likely in a position to roll back your changes. With the help of the [Git Reset command](/posts/git-reset-reset-your-changes-back-to-the-original-state), we can reset a branch to a commit id, then push it to the remote repository again. Hopefully, That will solve all our problems, will it? Unfortunately, It is not that easy. If the changes get merged to the remote repository, git reset will not work, and you have to revert those commits to roll back your changes. Git has the git revert command for this.

With git revert, you can revert [a single commit](#revert-single-commit) or [multiple commits](#revert-multiple-commits). In the end, Let’s see [what happens if we reset](#why-not-reset) our local branch and push it to the remote repository.

## Revert Single Commit

With Git revert, you can revert a single commit. Git will create a new commit with the revert message and revert the changes in the given commit. If there is a conflict, you have to resolve it, add it to track with Git add, then create a commit. Finally, you can push your revert to the remote repository.

**The Command:**
{% raw %}

```console
git revert <commit SHA id>
git push origin
```

{% endraw %}

> **Note:** You can only revert change commits. Merge commits (created when PR merges) cannot be reverted as they are not a change but a group of change commits. Check out the [Revert Multiple Commits](#revert-multiple-commits) section for options to revert merge commits.

![Single Revert](single-revert.png)
_Single Commit Revert_

## Revert Multiple Commits

> Coming soon...

## Why not Reset?

Git works with blob storage and takes the delta of the changes when you commit a change. The concept will make sense to you if you are familiar with the Linked List of Data Structures in programming. Git stores the content as blobs. In your very first commit, Git creates a blob, stores the content of your changes in it, and adds author information and timestamps. Git creates another blob in your following change, but the blob content includes only the changes made on top of the first commit (aka parent commit), then adds the first commit’s SHA id as the parent commit. It is also called commit-tree.

If you reset your branch and push it to the remote repository, Git will compare the source and the target repositories (local vs. remote) and detect that some of the commits are missing. Then, it will reject the merge.

If you reset your branch to one of the previous commits and push it to the remote repository, you will see errors below.

![Reset vs Revert](gitresetvsrevert.png)
_Git Reset vs. Git Revert_
