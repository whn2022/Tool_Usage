# 一、git命令

--help 查看对应命令

## git add

git add <file_name> 添加文件到暂存区

## git branch

git branch 列出本地所有分支

-v 查看最近一次提交信息

-a 查看远程分支

git branch <new_branch_name> 创建分支

git branch -m/-M <new_branch_name> 修改当前分支名称

## git checkout

git checkout <branchname> 切换分支 （如果没有该分支，可以加-b参数创建分支）

git checkout -- <file> 丢弃工作区对某个文件的修改

## git commit

git commit -m "本次提交的说明"（尽量带上m参数）

-a 表示自动暂存所有已跟踪文件的修改并提交

## git config

git config --global http.proxy http://127.0.0.1:你的代理端口号 为 Git 的 HTTP/HTTPS 协议设置全局代理

git config --global https.proxy http://127.0.0.1:你的代理端口号 为 Git 的 HTTP/HTTPS 协议设置全局代理

git config --global --unset http.proxy  取消代理
git config --global --unset https.proxy  取消代理

## git diff

git diff 暂存区与工作区状态相比较（必须先上传文件到暂存区）

--cached参数比较的是最后一次提交和暂存区的差异

## git init

git init 在当前文件夹创建.git文件，用以关联远程仓库

## git log

git log 显示提交历史 

--oneline origin/master..master 查看远程没有本地有的提交

--oneline master..origin/master 查看本地没有远程有的提交

## git ls-files

git ls-files(显示暂存区文件列表，只要文件被跟踪就会显示)

## git merge

git merge <branch_name> 合并branch到当前分支，可能有冲突

## git pull

git pull <remote_repository_name> <remote_branch_name> 

--allow-unrelated-histories 没有共同祖先的分支进行合并

--no-edit  不编辑默认合并信息，只适用于合并提交或修改最近一次提交

## git push

git push -u <remote_repository_name> <remote_branch_name> （首次推送需要用u来进行关联）

git push <remote_repository_name> --delete <remote_branch_name>  删除某个远程分支（不能为默认分支）

## git remote

git remote add <remote_name> <remote_url> 关联远程仓库（主要是通过url决定，name为别名，后续使用时可使用该别名）

git remote show 显示关联的远程仓库

## git status

git status 查看当前工作区和暂存区的状态

## git fetch

git fetch 将远程主机的最新内容拉到本地，由用户决定是否合并

## git stash

git stash 暂存当前分支的修改（暂存区和工作目录文件的修改）

git stash -u 存储未跟踪文件

git stash -a 存储未跟踪的文件和被忽略的文件

git stash pop 恢复之前的工作进度

# 二、git相关原理

![一文彻底搞清Git工作原理，实战案例](https://picx.zhimg.com/70/v2-992c7beb294c449a3b0fde75d05fbb09_1440w.image?source=172ae18b&biz_tag=Post)

## 文件四大状态

1. **未跟踪（Untracked）**
   - 新创建的文件，Git 尚未纳入版本控制。
   - **示例**：新建的 `newfile.txt`，从未执行过 `git add`。
2. **已修改（Modified）**
   - 已跟踪的文件被修改，但尚未暂存。
   - **示例**：修改了 `README.md` 但未 `git add`。
3. **已暂存（Staged）**
   - 文件的当前版本已标记为待提交（通过 `git add`）。
   - **示例**：执行 `git add README.md` 后，文件进入暂存区。
4. **已提交（Committed）**
   - 文件的当前版本已安全存入本地仓库（通过 `git commit`）。
   - **示例**：执行 `git commit` 后，文件快照永久存储。

## 暂存区

记录下一次提交的修改快照

位置 ： .git目录下的index文件中（需要提交才能看见）

## 本地版本库

存储所有提交的历史记录

位置：.git/objects/

# 三、git解析

## git diff命令输出解析

```
$ git diff
diff --git "a/git\344\275\277\347\224\250.md" "b/git\344\275\277\347\224\250.md"
index 3a88bcf..dc06651 100644
--- "a/git\344\275\277\347\224\250.md"
+++ "b/git\344\275\277\347\224\250.md"
@@ -6,6 +6,10 @@ git remote add <remote_name> <remote_url> 关联远程仓库

 git remote show 显示关联的远程仓库

+git add <file_name> 添加文件到暂存区
+
+git diff 暂存区与工作区状态相比较（必须先上传文件到暂存区）
+
```



`diff --git "a/git\344\275\277\347\224\250.md" "b/git\344\275\277\347\224\250.md"`

这一部分解释了比较的是**暂存区（a/）**和**工作区（b/）**中的 `git使用.md` 文件



`index 3a88bcf..dc06651 100644`

- `3a88bcf` → 暂存区中文件的旧哈希值。

- `dc06651` → 工作区中文件的新哈希值（修改后）。

- `100644` → 文件权限（普通文件）

  

`@@ -6,6 +6,10 @@`

`@@ -旧文件起始行,旧文件行数 +新文件起始行,新文件行数 @@`



```
git remote show 显示关联的远程仓库

+git add <file_name> 添加文件到暂存区
+
+git diff 暂存区与工作区状态相比较（必须先上传文件到暂存区）
+
```



- **`+` 开头的行**：新增了两行内容（关于 `git add` 和 `git diff` 的说明）。
- **无符号的行**：未修改的上下文内容

## git status命令输出解析

```
On branch master // 在主分支

No commits yet // 新仓库，尚未有提交记录

Changes to be committed:// 已暂存的更改
  (use "git rm --cached <file>..." to unstage)// 将文件从暂存区中移除，但是不删除物理文件
        new file:   "git\344\275\277\347\224\250.md"

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)// 丢弃工作区的更改，将文件恢复到暂存区或最近一次提交的版本
        modified:   "git\344\275\277\347\224\250.md"
  
  
 
 
 
Your branch is ahead of 'Tool_Usage/master' by 1 commit. // 本地尚有一个未提交
Your branch is behind 'origin/master' by X commits. // 本地尚未拉取
Your branch and 'origin/master' have diverged,
and have X and Y different commits each, respectively. // 本地与远程有不同的提交历史
```

## git log命令输出解析

```
$ git log
commit e75775a53624f56616fc46e4e17382fe165b2e5e (HEAD -> master, Tool_Usage/master)//同步
Author: whn2024 <1105185445@qq.com>
Date:   Thu Jul 10 23:06:28 2025 +0800

    git相关使用更新

commit 51b4c7439a475fe142df001922bc457166f6b779
Merge: f140746 d798b42
Author: whn2024 <1105185445@qq.com>
Date:   Thu Jul 10 23:01:03 2025 +0800

    Merge branch 'master' of https://github.com/whn2022/Tool_Usage

commit f140746cdfbf2d57e11626d180c3ca2db3d1f507
Author: whn2024 <1105185445@qq.com>
Date:   Thu Jul 10 22:45:29 2025 +0800

    git相关使用更新

commit 6f2cc8a8060645ba722fd7b41959a281a512052e
Author: whn2024 <1105185445@qq.com>
Date:   Thu Jul 10 22:35:13 2025 +0800
```



# 四、.git文件

| 文件/目录  | 作用                                                         |
| :--------- | :----------------------------------------------------------- |
| `HEAD`     | 当前所在的分支（如 `ref: refs/heads/master`）。              |
| `config`   | 仓库的本地配置（远程地址、用户信息等）。                     |
| `objects/` | 存储所有 Git 对象（提交、文件内容等），是版本库的核心数据。  |
| `refs/`    | 存储分支和标签的指针（如 `refs/heads/master` 指向最新提交）。 |
| `index`    | 暂存区（Staging Area）的二进制文件，记录 `git add` 的内容。  |
| `logs/`    | 操作历史（如 `git reflog` 的数据来源）。                     |