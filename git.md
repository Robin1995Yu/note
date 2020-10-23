# git
## git是什么
- git是一个版本管理工具
## git仓库创建
- git init 将当前的目录作为一个git仓库
## 文件上传
- git add <file name...> 将文件提交
- git commit -m "log" 将修改提交
## 版本管理
- git status 查看当前库的状态
  - changes not staged for commit
    - 文件还没有add
  - change to be commited
    - 已经add 但是还没提交
  - nothing to commit, working tree clean
    - 本地和仓库同步
- 创建仓库
- git diff 查看当前文件和仓库的差异
## 版本回退
- git log 查看git的版本
  - 每个版本都会有一个唯一的commit ID
- git reset --hard head^ 回退到上一版本
  - head标识当前版本
  - head^表示之前的版本 有几个^就表示前几个版本
  - head~100表示之前100个版本
  - 可以用commit ID的前几位声明版本（这个可以变到未来的版本）
    - 如果不知道未来的版本的commit ID 调用git reflog 可以显示之前的命令信息 包括提交命令和它对应的commit ID
  - 在回溯到之前的版本后就看不到那个版本之后的git log信息了
## 工作区和暂存区
- 工作区
  - 就是文件目录
- 版本库
  - 在.git文件夹里
  - stage/index 暂存区
    - add后但是还没有commit的文件会到这里来
  - 分支和HEAD指针
    - 分支
      - 主分支 master
        - 在库一创建就有
    - HEAD指针
      - 指向当前工作区某个分支的节点的指针
## 修改撤销
- git checkout -- <file name> 将暂存区或者分支中的指定文件替换当前文件
  - 回到最近一次add或者commit的状态
- git reset HEAD <file name> 将分支中的指定文件替换当前文件
  - 回到最近一次commit的状态
## 删除文件
- git rm <file name> 将文件从git库中删除
- 需要提交
## 远程仓库
- git remote add <远程仓库名> <ssh path>
  - 远程仓库名为本地的名字 一般命名为origin
- git push -u <远程仓库名> master
  - 参数-u 将本地的master分支和远程的master分支关联
  - 在一次之后就可以省去这个参数
- git clone <ssh path>
  - 从git远程仓库克隆
- git同样可以通过http协议 但是ssh协议是最快的
## 分支管理
- 分支的创建和合并
  - 什么是分支
    - 在库创建的时候 会默认创建一条master分支 叫做主分支
    - 每次提交的时候都会使分支向前一步
    - HEAD指针会指向当前分支的最近一次commit
  - 分支的创建
    - 命令
      - git branch
        - 查看当前所有的分支
        - 当前操作的分支前会标有*
        - -d 删除分支
      - git branch <分支名>
        - 创建一个新的分支
      - git switch <分支名>
        - 切换到分支
        - -c 创建分支并切换）
    - 新的分支都是从已有的分支上创建的
    - 新创建分支相当于创建一个指针 指向当前分支的提交
  - 分支的合并
    - 命令
      - git merge <分支名>
        - 将分支合并到当前分支
    - 将一条分支的指针指向另一条分支的指针
## 冲突解决
- 冲突的产生
  - 分支在合并之前有一个要修改的文件被修改 产生冲突
  - 冲突是的文本如下
```
*************************
*                       *
*   <<<<<<<HEAD         *
*   text in HEAD        *
*   =======             *
*   text in branch      *
*   >>>>>>>branch name  *
*                       *
*************************
```