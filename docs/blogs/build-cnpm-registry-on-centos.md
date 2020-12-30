### 在 CentOS 中搭建 cnpm 私有仓库

## 服务端
### 安装工具
```
$ npm install -g --build-from-source cnpmjs.org cnpm sqlite3
```

如果报错或者警告通过下面方式安装

```
$ npm install -g --unsafe-perm --verbose --build-from-source cnpmjs.org cnpm sqlite3
```

### 配置
配置文件位于`~/.cnpmjs.org/config.json`中，
```
{
  "bindingHost": "0.0.0.0",
  "admins": {
    "admin": "admin@example.com"
  },
  "scopes": [
    "@package"
  ],
  "registryHost": "registry.company.com"
}
```

`bindingHost`：设置`0.0.0.0`后，服务可外网访问
`admins`:  管理员配置，可多个
`scopes`: 包前缀，如果不是以这个前缀命名的包将不能发布，可多个
`registryHost`：下载包对应的服务器地址，如不配置会走默认`r.cnpmjs.org`

更多配置参考：https://github.com/cnpm/cnpmjs.org/blob/master/config/index.js

### 运行
```
$ cnpmjs.org start
```

运行后默认将会开启两个端口：`7001（registry）`，`7002（web）`

#### 7001（registry）
用来在命令行发布，下载包等对用的远程`registry`地址
比如：
`cnpmjs.org`官方：http://r.cnpmjs.org/
`npm`官方：https://registry.npmjs.org/

#### 7002（web）
用来在`web`端查看仓库信息，搜索包，包信息等
比如：
`cnpmjs.org`官方：https://cnpmjs.org/
`npm`官方：http://npmjs.com/

#### 将两个端口号通过`nginx`配置到对应域名
例如：
`7001`配置到`registry.company.com`
`7002`配置到`npm.company.com`

### 通过 pm2 运行
上述方法在命令行退出时，会自动关闭进程，如果需要持久化运行`cnpmjs.org`，可以借助进程管理工具`pm2`。
#### 安装 pm2
```
$ npm install pm2 -g
```

#### 运行
```
$ pm2 start `which cnpmjs.org` -- start
```

`--`可以在`pm2 start`的脚本后面增加参数。

## 客户端

### 发布包
#### 安装并配置配置 cnpm
```
$ npm install cnpm -g
$ cnpm set registry http://registry.company.com
```

这里的`registry`地址为上述`registryHost`字段。
#### 登录
```
$ cnpm login
```

输入用户名、密码、邮箱登录，为配置中`admins`字段
#### 发布
进入待发布工程目录，执行：
```
$ cnpm publish
```

### 下载包
```
$ cnpm install @package/package_name
```