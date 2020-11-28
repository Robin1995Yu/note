# git
## git是什么
- git是一个版本管理工具
## git仓库创建
- git init 将当前的目录作为一个git仓库
## 文件上传
- git add (file-name...) 将文件提交
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
- git checkout -- (file-name) 将暂存区或者分支中的指定文件替换当前文件
  - 回到最近一次add或者commit的状态
- git reset HEAD (file-name) 将分支中的指定文件替换当前文件
  - 回到最近一次commit的状态
## 删除文件
- git rm (file name) 将文件从git库中删除
- 需要提交
## 远程仓库
- git remote add (远程仓库名) (ssh-path)
  - 远程仓库名为本地的名字 一般命名为origin
- git push -u (远程仓库名) master
  - 参数-u 将本地的master分支和远程的master分支关联
  - 在一次之后就可以省去这个参数
- git clone (ssh-path)
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
      - git branch (分支名)
        - 创建一个新的分支
      - git switch (分支名)
        - 切换到分支
        - -c 创建分支并切换）
    - 新的分支都是从已有的分支上创建的
    - 新创建分支相当于创建一个指针 指向当前分支的提交
  - 分支的合并
    - 命令
      - git merge (分支名)
        - 将分支合并到当前分支
    - 将一条分支的指针指向另一条分支的指针
## 冲突解决
- 冲突的产生
  - 分支在合并之前有一个要修改的文件被修改 产生冲突
  - 冲突是的文本如下
```
*************************
*                       *
*   (((((((HEAD         *
*   text in HEAD        *
*   =======             *
*   text in branch      *
*   )))))))branch name  *
*                       *
*************************
```
- 冲突的解决
  - 保留需要的内容然后再次提交即可
## 分支管理策略
- Fast forward
  - 速度较快
  - 在删除分支之后 会丢失分支信息
  - 在冲突的时候无法使用这种模式
- git --no--ff (分支名) 禁用Fast forward提交
  - 通过新建一个commit提交分支
  - 需要和commit时一样的参数
- 分支策略
  - 主分支 master
    - 稳定版本 不在这个上面开发
  - 开发分支 dev
    - 来自与master分支 在这个上面开发
  - 个人分支
    - 每个人自己从dev上新建一个线程 将自己开发好的合并到dev分支
  - 在dev分支稳定后（通过测试）将它合并到master分支上完成发布
## BUG分支
- 命名规则 issue-bugname
  - 在完成bug修改后将它合并到它分出来到分支上
  - 删除
- 保存现场
  - git stash 保存现场
  - 可以切换分支完成别的工作
  - git stash list 显示保存的现场列表
  - git stash apply 恢复现场
  - git stash drop 删除现场
  - git stash pop 恢复现场并删除
  - 后三个命令可以指明是哪个现场
- 将一次提交作用在当前分支
  - git cherry-pich (commit-id)
## Feature分支
- 命名规则 feature-model_name
- 为了不在开发新功能到时候的代码污染主分支而产生的分支
- 过程
  - 从开发分支上新建feature分支
  - 在feature分支上开发
  - 选择是否和开发分支合并
    - 合并 通过合并命令合并
    - 不合并 通过git branch -D feature强制删除这个分支
## 多人合作
- 远程仓库
  - 默认名为origin
  - 把本地的master分支和远程仓库的master分支对应起来
  - 通过git remote查看当前关联的远程仓库
    - 加上-v参数显示详细信息
  - 推送到远程仓库实际上是把本地的分支推送到对应的远程仓库
    - git push (仓库名) (分支名)
  - 分支是否需要推送
    - master 需要时刻和远程同步
    - dev 所有成员都在这个分支上工作 需要时刻同步
    - BUG分支 用于本地修复bug 不需要推送到远程仓库
    - feature分支 取决与是否有同事一起开发这个功能
- 抓取分支
  - git clone (ssh-path)
    - 从仓库克隆
    - 默认只能看到master分支
  - git checkout -b dev orgin/dev
  - git switch -c dev orgin/dev
    - 将云端的分支克隆到本地新创建到分支
- 提交分支
  - git push 将分支提交到对应的远程仓库
  - 如果分支提交到远程仓库出现冲突
    1. 将分支抓取下来 通过git pull将远程仓库对应的分支抓取
    2. 处理冲突
    3. 重新将分支提交
    - 如果提示git pull时提示当前分支没有和远程分支建立链接关系
      - git branch --set-upstream-to (branch-name) origin/(branch-name)
## 标签
- 什么是标签
  - 和commit绑定的
  - 为了给commit一个更加友好的名称
- 创建标签
  - git tag (tag-name)
  - 参数-a 指定标签名（可省略）
  - 参数-m 指定说明文字
- 查看标签列表
  - git tag
- 打标签
  - 默认是打在最近一次提交
  - git tag (tag-name) (commit-id) 将标签打在指定的commit上
- 查看标签
  - git show (tag-name) 显示标签对应的commit的具体信息
- 标签删除
  - git tag -d(tag-name)
- 推送标签到远程仓库
  - git push origin (tag-name) 推送指定标签
  - git push origin --tag 推送所有标签
  - git push origin :refs/tags/(tag-name) 删除远程仓库的标签
## 自定义git
- 所有的配置都在.git/config文件中 可以手动修改
- 用户配置（针对这台机器上的所有仓库）的配置放在用户文件夹下的.gitconfig下
- git config -- global color.ui true git命令行会适当的显示颜色
- 忽略指定文件
  - 在根目录创建.gitignore文件
  - 忽略的原则
    - 系统自动生成的文件 如缩略图
    - 忽略编译生成的文件 如.class文件
    - 忽略敏感文件 如包含数据库信息的配置文件
    - 通过git add -f强制提交忽略的文件
    - 通过git check-ignore -v (file-name)检查哪里忽略了这个文件
  - 忽略文件的语法
    - 一行写一类忽略
    - \* 替代任意长度的文字
    - !(file-name)特殊情况
## 配置别名
- 给命令配置别名
  - git config --global alias.(alias-name) (command)