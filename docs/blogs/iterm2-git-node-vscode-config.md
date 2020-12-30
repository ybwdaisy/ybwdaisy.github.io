### iTerm2+Git+Node+VSCode 配置

#### 命令行工具 iTerm2
1. 安装 iTerm2
2. 配置主题 oh-my-zsh
```
sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```
3. 自动高亮插件
```
cd ~/.oh-my-**zsh**/plugins/
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git
```

`vi ~/.zshrc `并添加到最后：
```
source ~/.oh-my-zsh/plugins/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
```
保存退出并执行 `source ~/.zshrc`

4. 自动补全插件
```
cd ~/.oh-my-zsh/plugins/
git clone https://github.com/zsh-users/zsh-autosuggestions
```
`vi ~/.zshrc `并修改
```
plugins=(zsh-autosuggestions git)
```
保存退出并执行 `source ~/.zshrc`

配置默认文件夹
`iTerm2->Preferences->Profiles->General->Working Directory->Directory输入默认文件夹路径`

#### Git 配置
1. 安装Homebrew
```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"
```

2. 安装 Git
```
brew install git
```

3. 配置 user.name 和 user.email
```
git config --global user.name "username"
git config --global user.email "useremail"
```

4. 配置 alias 节省时间
```
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.ci commit
git config --global alias.br branch
git config --global alias.pl pull
git config --global alias.ps push
```

### Node 配置
1. 下载 Node 并安装：`https://nodejs.org/`
2. 配置淘宝镜像源
```
npm config set registry https://registry.npm.taobao.org
```

3. [可选] 安装 node 版本管理工具 nvm：`https://github.com/nvm-sh/nvm`
4. [推荐]安装 yarn
```
brew install yarn
```

### VS Code 常见插件
1. Eslint
2. Git History
3. GitLens
4. Path Intellisense
5. Prettier