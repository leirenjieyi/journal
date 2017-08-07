# Git ��Ҫ�̳�
## git �����汾��
�����汾����Է�Ϊ�������
* �������ذ汾��
    
    ```
    git init
    ```
    �����,���ڵ�ǰ����Ŀ¼����һ�� _.git_Ŀ¼.

* ��¡Զ�̰汾�⵽����

    �Կ�¡ github �ϵİ汾��Ϊ��:
    
    ```
    git clone git@github.com:leirenjieyi/gittest.git
    ```
    ��¡��github�ϵ�gittest�汾��.

* �����ذ汾���Զ�̰汾�������

    �Թ��� github �ϵ� gittest�汾��Ϊ��

    ���ȴ������ذ汾��

    ``` 
    git init 
    ```

    Ȼ����github�ϴ���Repositories,��Զ�̰汾��ͱ��ذ汾�������
    ```
    git remote add origin git@github.com:leirenjieyi/gittest.git 
    
    git push -u origin master
    ```
    ���ι�����master��֧push��Զ�̿�origin��(origin��Ĭ�ϵĴ���Զ�̿�),��Ҫ��Ӳ��� _-u_

    ������Ҫע��,git����֧��sshЭ��,������push֮ǰ��Ҫ�����ص�ssh��Կ���浽github��,�Ա㱾�ؿ���ͨ��sshЭ���github����;ssh��Կ���ɼ�github���òο� [_github help_](https://help.github.com/articles/connecting-to-github-with-ssh/)

## git ʹ��
### git �ļ��ύ

* git �ύ�ļ�

ע��:

1. git������Ǹ���,�������ļ�
git����ÿ�ε��ύ����Ψһ��һ��ID,ͨ�����IDȫ�ֱ�ʶһ���ύ.
2. git�ɷ�Ϊ���������ݴ�������ǰ��֧
������Ϊ��ǰĿ¼���������κ��޸Ļ�ֱ�ӷ����ڹ��������������޸������ͨ�� `git add <FileName>`,�������ύ�� _�ݴ���_ ,��ͨ�� `git commit -m "message"`�� _�ݴ���_ �еĸ���ͬ���ύ�� _��֧_ ;����ֱ��ͨ�� `git commit -a -m "message"` ����ֱ�ӽ������ύ�� _��֧_

���ò�����:

(1) ��д/�޸Ĵ���

(2) `git add <File1> <File2> ...`���ļ��ύ�� _�ݴ���_ 

(3) `git commit -m "message"` �ύ�� _��֧_

����ֱ�� `git commit -a -m "message"` һ�ν�ȫ�������ύ

> �ύ��ǰĿ¼�����ļ� ` git add --all`
> ������ǰ�ݴ����е������ύ(����add����û��commit֮ǰ�������ύ) `git rm --cached -r .` 

## �鿴���������޸�

���д��һ��ʱ�����,�Լ����Ƕ��޸�����Щ�ļ�,����ͨ�� `git status`�鿴
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
`git status` ��������鿴ͨ�� `git status --help` 


��Щʱ��,������һЩ�м��ļ�,��Щ�м��ļ����ǲ����ύ���汾����,������ `git status`��ʱ��Ҳ���������Щ�ļ�,�����ڹ���Ŀ¼����һ�� _.gitignore_ �ļ�,һЩ���õ��ļ������� [����](https:\\github.com/github/gitignore)�ҵ�.

## �ع��޸�
`git checkout <FileName>`�ع����һ�ε� add ���� commit,�ĸ�����ع��ĸ�;����ֱ�� `git checkout` �ع�����Ŀ¼�е�ȫ���޸�.\
`git reset --hard HEAD` �ع������һ���ύ��`git reset  --hard GUID` �ع���GUIDָ���İ汾��

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


## �Ա��ļ�
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
HEAD ��ʾ��֧�ϵ�����һ���ύ,HEAD^��ʾ��һ�ε��ύ,HEAD^^��ʾ���ϴε��ύ......,����������ʹ��HEAD~100,��ʾǰ100�ε��ύ.

## ��Զ�̿���ύ
    ӳ��Զ�ֿ̲�

    
    PS E:\git\MyProject\console_1_singalR> git remote add origin git@github.com:leirenjieyi/SignalR-Console-Sample.git
    PS E:\git\MyProject\console_1_singalR> git remote
    origin

    �״�push��Ҫ��Ӳ���-u
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

## gitignore�ļ����˹���

- /target/ �������ļ����ã���ʾ��������ļ���
- *.mdb? ��*.ldb? ��*.sln ��ʾ����ĳ�����͵��ļ�
- /mtk/do.c ��/mtk/if.h? ��ʾָ������ĳ���ļ��¾����ļ�
- !*.c , !/dir/subdir/???? !��ͷ��ʾ������
- *.[oa]??? ֧��ͨ���������repo��������.o����.aΪ��չ�����ļ�



## Q&A


> ### 1.Զ�ֿ̲��뱾�زֿⲻͬʱ����ν��й���
> ����Github�½�һ���ֿ⣬д��ReadMe��Ȼ��ѱ���һ��д�˺ܾòֿ��ϴ���
>
> ��pull����Ϊ�����ֿⲻͬ������refusing to merge unrelated histories���޷�pull��Ϊ������������ͬ����Ŀ��Ҫ��������ͬ����Ŀ�ϲ���git��Ҫ���һ����룬��git pull������������git 2.9.2�汾�����ģ����µİ汾��Ҫ���--allow-unrelated-histories
> 
> �������ǵ�Դ��origin����֧��master����ô���� ��Ҫ����дgit pull origin master --allow-unrelated-histories��Ҫ֪�������ǵ�Դ�����Ǳ��ص�·��
> ### 2.�������Կ�Ե�github�ϣ�����sshЭ�飬ȴһֱ��ʾ `Permission denied (publickey)`. 
>��Ӧ����һ��git��ssh��bug,�ڲ���ssh-keygen����rsa���͵���Կ��ʱ���������Ĭ�ϵ�id_rsa���ƣ����޷�ʹ�á�
>������ñ����Կ���ƣ���ֻ����git bash��ǰ������ʹ�ã�����ͨ�� `eval $(ssh-agent -s)`���� ssh-agent,Ȼ��ʹ�� `ssh-add` ��˽Կ��ӵ�ssh-agent�У�ͨ�� `ssh-add -l` ���Բ鿴 ssh-agent�е�ע��˽Կ��Ϣ��
>��֮��򵥵ķ�������ʹ��Ĭ�ϵ�id_rsa���ơ�
