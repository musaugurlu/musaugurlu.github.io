---
layout: post
title: Windows 7 – 0X000014c Error – Missing Registry File
description: Windows 7 – 0X000014c Error – Missing Registry File
img_path: /assets/img/posts/2016-03-16-windows-7-0x000014c-error-missing-registry-file
image:
  path: winerror0x000014c.jpg
categories: [Workstation]
tags: [windows, workstation, win7]
date: 2016-03-16 00:00 -0400
---

## Windows 7 – 0X000014c Error – Missing Registry File

I have encountered the error above many times. I spent lots of my time, even re-installed the windows to fix it. After searching for this online, i found a quick fix.

What causes this issue is one or more damaged system files stored in

> `C:\Windows\system32\config\`

and windows keeps backup files for them which are in

> `C:\Windows\system32\config\RegBack` .

If we move the backup file from this folder to the config folder in system32, the issue will be fixed. To do that, we will need to access the command prompt. you may use windows 7, windows 8, or Windows 10 installation disk. Once you get in the command prompt, you will just need to type the below command, then restart the computer. Boom. it works.
{% raw %}

```bat
copy "C:\windows\system32\config\RegBack\SYSTEM" "C:\windows\system32\config\SYSTEM"
```

if you wonder what files stored in that folder, you may go to folder and run `dir` command as seen below

```bat
c:                                    #->this changes the current drive to C: Drive
cd windows\system32\config\regback    #-> this command takes you to regback folder
dir                                   #->this command lists the files and folder under current folder(regback)
```

{% endraw %}
