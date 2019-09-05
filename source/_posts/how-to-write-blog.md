---
title: 如何发布博客
date: 2019-09-05 23:00:28
categories: 使用说明
tags:
 - git
 - blog
---

## Know Branch

>
 - mainstream 源文件（markdown）
 - master 网页文件 (HTML)
 - write 您需要在此分支 checkout 一个新分支

[see network](https://github.com/ECNUPRML/ecnuprml.github.io/network)

## Step

```bash
> git clone git@github.com:ECNUPRML/ecnuprml.github.io.git
> git checkout write

  Switched to a new branch 'write'
  Branch 'write' set up to track remote branch 'write' from 'origin'.

> git checkout -b yourname/topic
  
  Switched to a new branch 'yourname/topic'
```

在 `source/_posts/` 目录中编写您的博客内容。若要插图请新建一个同名文件夹，将图片放于该文件夹中。

确认无误后提交您的分支

```bash
> git add .
> git commit -m "msg"
> git push --set-upstream origin yourname/topic
```

如需修改，请继续在您的分支提交修改。

确认无误后，在 `github` 的 `repo` 中请求将您的分支合并到 `mainstream` 中。

![Create pull request](PR.PNG)


等待管理员确认您的合并请求。

![Merged](merge.PNG)

发布成功！

