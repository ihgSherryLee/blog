---
title: multi-git-account
categories:
  - git
tags:
  - git 多账号
  - git 提交历史
abbrlink: 8d0071d2
date: 2019-08-09 10:50:43
---

#### 创建多个git账号&修改git提交历史

因公司使用gitlab，账号为公司邮箱，自己也有个GitHub，账号为私人邮箱，要怎么做才能使用不同的账号push不同的仓库呢？

生成不同的ssh key:

```
ssh-keygen -t rsa -f ~/.ssh/id_rsa_github -C "email@email.com"
```

我把公司的命名为id_rsa，github的加了个后缀，然后将key复制到网站上。

```
vi id_rsa_github.pub
```

相信之前肯定设置了global的email和name，网上大部分说法都是讲global的设置删除，但也可以不删，只是在相对应的gitlab仓库下设置gitlab账号就好（我是global使用github账号，因为公司项目较少，需设置的仓库较少）

```
// 全局
git config --global user.email githubEmail
git config --global user.name ihgSherryLee

//gitlab项目
git config user.email gitlabEmail
git config user.name gitlabName
```

一旦使用其他账号commit/push过，想要修改git的提交记录（因为github的contribution不好看），可以用脚本来做：

```
git filter-branch --env-filter '

OLD_EMAIL="oldEmail@email.com"
CORRECT_NAME="name"
CORRECT_EMAIL="email"

if [ "$GIT_COMMITTER_EMAIL" = "$OLD_EMAIL" ]
then
    export GIT_COMMITTER_NAME="$CORRECT_NAME"
    export GIT_COMMITTER_EMAIL="$CORRECT_EMAIL"
fi
if [ "$GIT_AUTHOR_EMAIL" = "$OLD_EMAIL" ]
then
    export GIT_AUTHOR_NAME="$CORRECT_NAME"
    export GIT_AUTHOR_EMAIL="$CORRECT_EMAIL"
fi
' --tag-name-filter cat -- --branches --tags
```

存储为项目根目录下email.sh(第一次覆盖更新时)

```
chmod +x email.sh 
./email.sh 
```

若要在同一个项目再覆盖一次，执行后回提示

```
Cannot create a new backup.
A previous backup already exists in refs/original/
Force overwriting the backup with -f
```

要把refs/original/删掉：

```
rm -rf .git/refs/original/
或
git filter-branch -f --index-filter 'git rm --cached --ignore-unmatch Rakefile' HEAD

./email.sh 
```

这样就能修改成功，然后push

```
git push origin --force --all
git push origin --force --tags
```

