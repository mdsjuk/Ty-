# git安装

fsa查收n master

1 git config --user.name "mdsjuk"

2 git config --user.email"mdsjuk.com"

3 初始化本地仓库 git init

4 添加文件到暂存区 git add 文件 

5 添加文件到本地仓库 git commit -m "描述"

6 回退版本 git reset --hard HEAD^ 

7 前进版本 git reset --hard commitID 通过reflog查看commitID

8 回退 未add  git checkout -- 文件 仓库覆盖

9 回退 已经add git reset HEAD 清空暂存区 再 git checkout -- 文件

10 git diff  文件 比较两个文件 git diff HEAD 文件 本地库比较 

# git 连接到github

github 保存本地git的sshkey

github 创建repository

本地 git remote add 连接名 repository 例如 git remote add origin git@github.com:mdsjuk/Typora.git

本地推送到 github git push -u 连接名 master

git remote -v 查看有多少连接 

![image-20210325120820691](C:\Users\25431\AppData\Roaming\Typora\typora-user-images\image-20210325120820691.png)

git remote  remove 清除连接

# 分支管理

![image-20210325121408723](C:\Users\25431\AppData\Roaming\Typora\typora-user-images\image-20210325121408723.png)

dev 分支 将HEAD 指针指向dev 
