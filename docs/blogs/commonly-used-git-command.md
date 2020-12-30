### 常用 git 命令

#### 1. 回滚到某次提交
###### 1. 回滚到上一次版本
```
git reset --hard HEAD
```

###### 2. 回滚到某个commit_id(通过git log查找)
```
git reset --hard commit_id
```

###### 3. 提交回滚
```
git push origin HEAD --force
```

#### 2. 撤销add
```
git reset --mixed
```

#### 3. 撤销commit
```
git reset --soft HEAD^
```

#### 4. 更新远程分支列表
```
git remote update origin --prune
git remote prune origin
```

#### 5. 本地工程同步到remote时，git push 被拒绝
```
git pull origin master --allow-unrelated-histories
```

#### 6. 将本地已存在项目提交到远端
```
cd existing-project
git init
git add .
git commit -m "Initial Commit"
git remote add origin 'repo url'
git push -u origin master
```

#### 7. 解决修改.gitignore不生效
```
git rm -r --cached .
git add .
git commit 'update gitignore'
```

#### 8. 撤销所有改动，包括修改和新增文件
```
git reset --hard
git clean -df
```