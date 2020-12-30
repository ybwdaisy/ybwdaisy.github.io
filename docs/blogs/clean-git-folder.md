### 如何给 .git 文件夹瘦身？

推荐工具：https://github.com/newren/git-filter-repo

#### 对工程进行分析
```
git-filter-repo --analyze
```

#### 删除指定文件或文件夹，如：
```
git-filter-repo --path-glob 'android/*.hprof' --invert-paths --force
```

#### 更新所有分支
```
git push origin --force --all 
```

#### 更新所有 tag（如果有）
```
git push origin --force --tags
```