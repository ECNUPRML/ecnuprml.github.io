---
title: 如何发布博客
date: 2019-09-05 23:00:28
categories: 使用说明
tags:
 - git
 - blog
---

## Know Branch

 - mainstream 源文件（markdown）
 - master 网页文件 (HTML)
 - write 您需要在此分支 checkout 一个新分支 new

[see network](https://github.com/ECNUPRML/ecnuprml.github.io/network)

请确保 write 分支是 new 分支的 parent，即当前两个分支的 log 是完全相同的。

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

在 request 创建成功后，会自动触发 Travis CI Build 您的网页。在 build 成功后您可以在 Archives 上查看最新发布的文章。（[为何能够自动触发？](https://github.com/gymgle/g2ex.github.io/blob/d105dc258a9184b667578d9196c223a567fe2269/source/_posts/2019-06-28-hexo-with-travis-ci.md)）

但此时，您的分支并没有合并到 `mainstream` 上。合并成功需满足两个前提：

1. At least 2 reviews approve your code.
2. Travis CI Build 成功。（会自动开启 build）

若满足以上条件则可以确认合并请求。

![Merged](merge.PNG)

合并成功！

