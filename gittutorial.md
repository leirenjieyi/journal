# Git 简要教程
## git 创建版本库
创建版本库可以分为三种情况
* 创建本地版本库
    
    ```
    git init
    ```
    命令即可,会在当前工作目录生成一个 _.git_目录.

* 克隆远程版本库到本地

    以克隆 github 上的版本库为例:
    
    ```
    git clone git@github.com:leirenjieyi/gittest.git
    ```
    克隆了github上的gittest版本库.

* 将本地版本库和远程版本库相关联

    以关联 github 上的 gittest版本库为例

    首先创建本地版本库

    ``` 
    git init 
    ```

    然后在github上创建Repositories,将远程版本库和本地版本库相关联
    ```
    git remote add origin git@github.com:leirenjieyi/gittest.git 
    
    git push -u origin master
    ```
    初次关联将master分支push到远程库origin上(origin是默认的代表远程库),需要添加参数 _-u_

    这里需要注意,git本身支持ssh协议,所以在push之前需要将本地的ssh公钥保存到github上,以便本地可以通过ssh协议和github相连;ssh秘钥生成及github设置参考 [_github help_](https://help.github.com/articles/connecting-to-github-with-ssh/)

## git 使用
### git 文件提交

* git 提交文件

注意:

1. git管理的是更改,而不是文件
git根据每次的提交生成唯一的一个ID,通过这个ID全局标识一次提交.
2. git可分为工作区、暂存区、当前分支
工作区为当前目录，所做的任何修改会直接发生在工作区；工作区修改做完后通过 `git add <FileName>`,将更改提交到 _暂存区_ ,再通过 `git commit -m "message"`将 _暂存区_ 中的更改同意提交到 _分支_ ;或是直接通过 `git commit -a -m "message"` 命令直接将更改提交到 _分支_

常用步骤是:

(1) 编写/修改代码

(2) `git add <File1> <File2> ...`将文件提交到 _暂存区_ 

(3) `git commit -m "message"` 提交到 _分支_

或是直接 `git commit -a -m "message"` 一次将全部更改提交

> 提交当前目录所有文件 ` git add --all`
> 撤销当前暂存区中的所有提交(撤销add命令没有commit之前的所有提交) `git rm --cached -r .` 

## 查看工作区的修改

如果写了一段时间代码,自己忘记都修改了哪些文件,可以通过 `git status`查看
```
PS E:\gittest> git status
On branch dev
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   license.txt
        modified:   readme.txt

no changes added to commit (use "git add" and/or "git commit -a")
```
`git status` 命令参数查看通过 `git status --help` 


有些时候,会生成一些中间文件,这些中间文件我们不想提交到版本库中,并且在 `git status`的时候也不想出现这些文件,可以在工作目录创建一个 _.gitignore_ 文件,一些常用的文件可以在 [这里](https:\\github.com/github/gitignore)找到.

## 回滚修改
`git checkout <FileName>`回滚最近一次的 add 或是 commit,哪个最近回滚哪个;或是直接 `git checkout` 回滚工作目录中的全部修改.\
`git reset --hard HEAD` 回滚到最近一次提交，`git reset  --hard GUID` 回滚到GUID指定的版本。

```bash
PS D:\git\Share\test.git> "bbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbb">>.\test.txt
PS D:\git\Share\test.git> git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   test.txt

no changes added to commit (use "git add" and/or "git commit -a")
PS D:\git\Share\test.git> git add .
PS D:\git\Share\test.git> "cccccccccccccccccccccccccccccccccccccccccc">>.\test.txt
PS D:\git\Share\test.git> cat .\test.txt
aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaab b b b b b b b b b b b b b b b b b b b b b b b b b b b b b b b b b b b b


 c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c c


PS D:\git\Share\test.git> git checkout test.txt
PS D:\git\Share\test.git> cat .\test.txt
aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaab b b b b b b b b b b b b b b b b b b b b b b b b b b b b b b b b b b b b
```

```bash
PS D:\git\Share\test.git> cat .\test.txt
aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaab b b b b b b b b b b b b b b b b b b b b b b b b b b b b b b b b b b b b



PS D:\git\Share\test.git> git log --pretty=oneline
efda7f365cf605f6be152afc68ae4d1a0def5bd8 (HEAD -> master) bbbbbb
e69ec8140fd02e8e400d9e2e388784fe80c2d3e2 a
PS D:\git\Share\test.git> git reset --hard e69ec8140fd02e8e400d9e2e388784fe80c2d3e2
HEAD is now at e69ec81 a
PS D:\git\Share\test.git> cat .\test.txt
aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
```


## 对比文件
 `git diff <File1> -- <File2>`
 ```
PS E:\gittest> git diff HEAD -- .\license.txt
diff --git a/license.txt b/license.txt
index d70907f..b0f78fe 100644
--- a/license.txt
+++ b/license.txt
@@ -1,4 +1,2 @@
 computer
-adfdsafdsafd
-fdsfdsafds
-fdsafdsaff
\ No newline at end of file
+license share
\ No newline at end of file
 ```
HEAD 表示分支上的最新一次提交,HEAD^表示上一次的提交,HEAD^^表示上上次的提交......,如果过多可以使用HEAD~100,表示前100次的提交.

## 与远程库的提交
    映射远程仓库

    
    PS E:\git\MyProject\console_1_singalR> git remote add origin git@github.com:leirenjieyi/SignalR-Console-Sample.git
    PS E:\git\MyProject\console_1_singalR> git remote
    origin

    首次push需要添加参数-u
    PS E:\git\MyProject\console_1_singalR> git push -u origin master
    Counting objects: 23, done.
    Delta compression using up to 4 threads.
    Compressing objects: 100% (19/19), done.
    Writing objects: 100% (23/23), 5.68 KiB | 0 bytes/s, done.
    Total 23 (delta 5), reused 0 (delta 0)
    remote: Resolving deltas: 100% (5/5), done.
    Branch master set up to track remote branch master from origin.
    To github.com:leirenjieyi/SignalR-Console-Sample.git
    10e4cdd..9bfb3e1  master -> master

## gitignore文件过滤规则

- /target/ ：过滤文件设置，表示过滤这个文件夹
- *.mdb? ，*.ldb? ，*.sln 表示过滤某种类型的文件
- /mtk/do.c ，/mtk/if.h? 表示指定过滤某个文件下具体文件
- !*.c , !/dir/subdir/???? !开头表示不过滤
- *.[oa]??? 支持通配符：过滤repo中所有以.o或者.a为扩展名的文件



## Q&A


> ### 1.远程仓库与本地仓库不同时，如何进行关联
> 我在Github新建一个仓库，写了ReadMe，然后把本地一个写了很久仓库上传。
>
> 先pull，因为两个仓库不同，发现refusing to merge unrelated histories，无法pull因为他们是两个不同的项目，要把两个不同的项目合并，git需要添加一句代码，在git pull，这句代码是在git 2.9.2版本发生的，最新的版本需要添加--allow-unrelated-histories
> 
> 假如我们的源是origin，分支是master，那么我们 需要这样写git pull origin master --allow-unrelated-histories需要知道，我们的源可以是本地的路径
> ### 2.添加了密钥对到github上，采用ssh协议，却一直提示 `Permission denied (publickey)`. 
>这应该是一个git中ssh的bug,在采用ssh-keygen生成rsa类型的密钥对时，如果不是默认的id_rsa名称，则无法使用。
>如果采用别的密钥名称，则只能在git bash当前环境中使用，可以通过 `eval $(ssh-agent -s)`启动 ssh-agent,然后使用 `ssh-add` 将私钥添加到ssh-agent中，通过 `ssh-add -l` 可以查看 ssh-agent中的注册私钥信息。
>总之最简单的方法就是使用默认的id_rsa名称。
