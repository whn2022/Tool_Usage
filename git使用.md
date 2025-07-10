# 一、git命令

git init 在当前文件夹创建.git文件，用以关联远程仓库

git remote add <remote_name> <remote_url> 关联远程仓库

git remote show 显示关联的远程仓库

git add <file_name> 添加文件到暂存区

git diff 暂存区与工作区状态相比较（必须先上传文件到暂存区）（--cached参数比较的是最后一次提交和暂存区的差异）

git status 查看当前工作区和暂存区的状态

git commit -m "本次提交的说明"（尽量带上m参数）

git ls-files(显示暂存区文件列表，只要文件被跟踪就会显示)

git push -u <remote_repository_name> <remote_branch_name> （首次推送需要用u来进行关联）

git pull <remote_repository_name> <remote_branch_name> （--allow-unrelated-histories，没有共同祖先的分支进行合并）（--no-edit 不编辑默认合并信息）

# 二、git相关原理

![一文彻底搞清Git工作原理，实战案例](https://picx.zhimg.com/70/v2-992c7beb294c449a3b0fde75d05fbb09_1440w.image?source=172ae18b&biz_tag=Post)

## 暂存区

记录下一次提交的修改快照

位置 ： .git目录下的index文件中（需要提交才能看见）

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