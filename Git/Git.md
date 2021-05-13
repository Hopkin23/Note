# 1

https://os.51cto.com/art/202103/648796.htm

设置用户名和有邮箱
` git config --global user.name "用户名" `
` git config --global user.email "邮箱" `--global 是全局设置，如果想对特定项目使用不同配置，可取消该参数

创建新仓库
`git init`

克隆
克隆本地仓库  `git clone /path/to/repository`
克隆远端仓库  `git clone username@host:/path/to/repository`

添加与提交
查看当前状态 `git status`
未跟踪文件或修改的文件添加进入暂存区    ` git add <filename>  /* `
提交暂存区文件到本地仓库`git commit -m "代码提交信息"`
添加提交合并`git commit -a -m "代码提交信息"`

推送改动
查看远端仓库 `git remote`
添加远端仓库 `git remote add origin <server>`
推送远端仓库 `git push origin master`

分支操作(默认主分支master)
查看分支 `git branch -a`
新建分支 `git branch <branch>`
删除分支 `git branch -d <branch>`
删除远程分支 `git branch origin -d <branch>`
切换分支 `git checkout <branch>`
快速检出上一个分支 `git checkout -`
推送分支 `git push origin <branch>`
合并分支 `git merge <branch>`
重命名`git branch -m old new`

拉取更新  
`git fetch 主机名 分支名   // 不合并`
`git pull 主机名 分支名    // 合并`

SSH key
` ssh-keygen -t rsa -C "邮箱" `  ----用户主目录下 .ssh 目录，其中包含 id_rsa 和 id_rsa.pub

更新
查看冲突 `git diff <source_branch> <target_branch>`

打标签 ` git tag -a 版本号 -m "版本说明" `


查看配置命令
` git config --help `


clone时一定要注意选择ssh协议的链接。否则可能导致clone失败，或者后续push失败