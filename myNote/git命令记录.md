# Git 的命令记录

## 克隆
```obj
git clone  <targetURL>
```

## 查看本地提交记录

```objc
git log
```

暂存

```js
// 快速暂存
git stash

// 暂存抱歉
git stash save '名字'
```

恢复暂存

```js
// 快速恢复
git pop

// 恢复指定暂存内容，index 通过 git stash list 获取
git stash apply stash@{index} 
```



## 放弃本地修改内容

### 1. 已经使用过 git add .
```obj
git reset HEAD filepathname 
git reset HEAD .
```
### 2. 未使用过
```obj
git checkout . 
git checkout someFilePath
```
### 3. 已经Commit过的代码

```js
// 已修改的代码仍保留
git reset --soft HEAD^
```



## 分支

### 1. 创建本地分支

```objc
git checkout <somebranch>
git checkout -b <newbranch>
```

### 2. 推送本地分支到远程分支（远程分支不存在）

```objc
git checkout <youreBranch>
git push --set-upstream origin <remoteBranchName>
```

### 3. 删除本地分支

```obj
git branch -D <branchName>
```

### 4. 拉取远程分支

```obj
git checkout -b dev origin/dev
```
### 5. 删除远程分支

```objc
git push origin --delete <needDeleteBranchName>
```

## fork 分支更新源代码

### 1. 查看远程状态
```obj
git remote -v
```

### 2. 把作者的仓库添加到remote
```obj 
git remote add upstream  targetURL
```

### 3. 同步fork
从上游仓库 fetch 分支和提交点，传送到本地，并会被存储在一个本地分支 ```upstream/master```
```obj
git fetch upstream
```

### 4. 切换到本地分支，防止出错
```obj
git checkout devloper
```

### 5. 合并代码
```obj
git merge upstream/master
```

## 代码回滚