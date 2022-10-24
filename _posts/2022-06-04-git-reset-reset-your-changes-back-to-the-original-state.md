---
layout: post
title: "Git Reset: Reset Your Changes Back to the Original State"
description: "Git Reset: Reset Your Changes Back to the Original State"
img_path: /assets/img/posts/2022-06-04-git-reset-reset-your-changes-back-to-the-original-state
image:
  path: hardreset0.png
categories: [DevOps, Git]
tags: [git, cloud]
date: 2022-06-04 16:00 -0400
---

## Git Reset: Reset Your Changes Back to the Original State

Version control systems come in handy when you make mistakes or want to start over. One of their many features is the ability to reset changes. With the reset, you can reset your branch back to its original state with a commit SHA id or to another branch.

Git has three reset options; [soft reset](#soft-reset), [mixed reset](#mixed-reset), and [hard reset](#hard-reset).

## Before We Start

Before we start, let’s clone a repository, check out a new branch, and commit some changes. So, we can reset them with all three options and see the difference between them.

I will be using my [GitRevertReset](https://github.com/musaugurlu/GitResetRevert) repository for this post. It has a sample DotNet 6 Web API application created by the command below.
{% raw %}

```console
dotnet new webapi -o GitRevertReset
```

{% endraw %}

If you follow along, below are the command to run to prepare the repo.
{% raw %}

```console
git clone https://github.com/musaugurlu/GitResetRevert.git
cd GitResetRevert
git checkout -b feature/newApi
```

{% endraw %}
Then, make some changes to the project. I have added a new API to the controller. If you follow along, add the below code to the [WeatherForecastController.cs](https://github.com/musaugurlu/GitResetRevert/blob/feature/newApi/Controllers/WeatherForecastController.cs#L33-L42) file under the Controllers folder.
{% raw %}

```c#
[HttpPost(Name = "PostWeatherForecast")]
public IActionResult Post()
{
    return Ok(new WeatherForecast
    {
        Date = DateTime.UtcNow,
        TemperatureC = Random.Shared.Next(-20, 55),
        Summary = Summaries[Random.Shared.Next(Summaries.Length)]
    });
}
```

{% endraw %}

Then, commit your changes.

{% raw %}

```console
git commit -a -m "Added Post API"
```

{% endraw %}
if you run git status on your terminal, you will end up with the screen below.

![status](gitreset0.png)
_Git Repository_

## Soft Reset

For Git to track your changes, you need to add files to Git’s track with the command `git add <file name>` (or `git add -A` for all the files). When you commit your changes, Git adds the changes in the tracked files into the local repository, and then you synch the local repository with the remote one with the git push command.

Soft reset resets your branch to a given branch or a commit SHA id and keeps the files tracked. So that you can update your changes, add only the updated files to git track, and commit again.

**_The command:_**
{% raw %}

```console
git reset --soft <branch name or Commit short/long SHA id>
```

{% endraw %}

> **Note:** If you reset your branch to a remote branch, add `origin/` at the beginning of your branch so that Git can know it is at origin (the remote location).

![Git Reset](softreset2.png)
_Soft Reset_

![GitLog Before Reset](softreset1.png)
_Git Log Before Soft Reset_

![Git Log After Reset](softreset3.png)
_Git Log After Soft Reset_

## Mixed Reset

A mixed reset does almost the same effect as a soft reset with only one difference: It removes the file from git tracking. If you want to commit your files again, whether or not you updated them, you have to add them to git tracking again with the command `git add`

**The command:**
{% raw %}

```console
git reset --mixed <branch name or Commit short/long SHA id>
```

{% endraw %}

> **Note:** If you reset your branch to a remote branch, add `origin/` at the beginning of your branch so that Git can know it is at origin (the remote location).

![Mixed Reset](mixedreset1.png)
_Mixed Reset_

![Git Log After Mixed Reset](mixed2.png)
_Git Log After Mixed Reset_

## Hard Reset

Unlike the mixed and soft reset, Hard reset resets the branch to the given branch or commits SHA id and removes the files that do not exist in the target branch.

**The command:**
{% raw %}

```console
git reset --hard <branch name or Commit short/long SHA id>
```

{% endraw %}

> **Note:** If you reset your branch to a remote branch, add `origin/` at the beginning of your branch so that Git can know it is at origin (the remote location).

![Hard Reset](hardreset0.png)
_Hard Reset_

![Git Log After Hard Reset](hardreset2.png)
_Git Log After Hard Reset_
