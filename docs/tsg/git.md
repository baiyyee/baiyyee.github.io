# Git


### fatal: Could not read from remote repository

```
(WeTest) ➜  WeTest git:(dev) git push      
ssh_exchange_identification: Connection closed by remote host
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.


(WeTest) ➜  WeTest git:(dev) git remote -v
origin  git@github.com:baiyyee/WeTest.git (fetch)
origin  git@github.com:baiyyee/WeTest.git (push)


(WeTest) ➜  WeTest git:(dev) git remote set-url origin https://github.com/baiyyee/WeTest.git   
(WeTest) ➜  WeTest git:(dev) git status                                                     
On branch dev
Your branch is ahead of 'origin/dev' by 2 commits.
  (use "git push" to publish your local commits)

nothing to commit, working tree clean


(WeTest) ➜  WeTest git:(dev) git remote -v                                                  
origin  https://github.com/baiyyee/WeTest.git (fetch)
origin  https://github.com/baiyyee/WeTest.git (push)


(WeTest) ➜  WeTest git:(dev) git push     
Counting objects: 11, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (11/11), done.
Writing objects: 100% (11/11), 1.20 KiB | 25.00 KiB/s, done.
Total 11 (delta 9), reused 0 (delta 0)
remote: Resolving deltas: 100% (9/9), completed with 8 local objects.
To https://github.com/baiyyee/WeTest.git
   3c1d746..a3f9e0e  dev -> dev
```

<br>

**【解决方案】**


1.同一个开发环境中，其他两个仓库可正常交互，但另外一个频繁提示找不到远程仓库或是没权限，研究后发现克隆方式不同：
> 方式一：git clone https://github.com/baiyyee/baiyyee.github.io.git
>
> 方式二：git clone git@github.com:baiyyee/WeTest.git

建议：以后采用 方式一 来克隆仓库，连通性更好

<br>
2.通过命令 `git remote -v` 可查看已克隆到本地的仓库是采用哪一种方式

<br>
3.对于已采用方式二可能且已经出现了 `fatal: Could not read from remote repository` 问题的，可通过执行以下命令更新为方式一：
```
git remote set-url origin https://github.com/baiyyee/WeTest.git
```