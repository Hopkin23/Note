# git常用操作

1. reset指定节点(常因为reset错误节点)

    ```bash
    git reflog show // 查看所有的操作日志
    git log -g    //查看所有的操作日志
    git reset --hard HEAD@{1} //恢复到指定版本
    ```
