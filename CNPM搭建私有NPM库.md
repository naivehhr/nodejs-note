## 搭建私有NPM

[GITBOOK](https://ggpp224.gitbooks.io/web-notes/content/CNPM%E7%A7%81%E6%9C%89%E6%9C%8D%E5%8A%A1%E5%99%A8%E6%90%AD%E5%BB%BA.html)

[一些参数配置](https://xcoder.in/2016/07/09/lets-cnpm-base-deploy/)

### 安装
```
#从github获取源代码
$ git clone git://github.com/cnpm/cnpmjs.org.git
$ cd cnpmjs.org

# 创建mysql表结构
$ mysql -u yourname -p
mysql> create database cnpmjs
mysql> use cnpmjs;
mysql> source docs/db.sql
```


### 配置

```
// 这是单独配置config，也可以写到默认配置中
vim config/config.js
```

```
enableCluster: true,
bindingHost: '172.18.22.198',
  // default system admins
admins: {
    // name: email
    fengmk2: 'fengmk2@gmail.com',
    admin: 'guopengb@chanjet.com',
    dead_horse: 'dead_horse@qq.com',
},  
  database: {
    db: 'cnpmjs',
    username: 'root',
    password: 'baby',

    // the sql dialect of the database
    // - currently supported: 'mysql', 'sqlite', 'postgres', 'mariadb'
    dialect: 'mysql',

    // custom host; default: 127.0.0.1
    host: '127.0.0.1',

    // custom port; default: 3306
    port: 3306,

      // public mode: all users can publish
  enablePrivate: false,

  // registry scopes, if don't set, means do not support scopes
  scopes: [ '@cjt','@cnpm', '@cnpmtest', '@cnpm-test' ],
  // sync mode select
  // none: do not sync any module, proxy all public modules from sourceNpmRegistry
  // exist: only sync exist modules
  // all: sync all modules
  syncModel: 'exist', // 'none', 'all', 'exist'
    // sync devDependencies or not, default is false
  syncDevDependencies: true,
registryHost: ip
```


### 安装依赖

```
npm install
```


### 启动服务

```
node dispatch.js --harmony_generators
// npm run start/test/stop
```


### 安装cnpm客户端 

```
sudo npm install cnpm -g
// 更改默认的registry，指向私有的registry
cnpm set registry http://172.18.22.198:7001
```

### 用npm账号登录你的私有registry

```
$ cnpm login
Username: guopeng
Password: ***
Email: (this IS public) ggpp224@sina.com
```
### 创建私有测试npm包

```
$ cd /tmp
$ mkdir helloworld && cd helloworld
$ cnpm init
name: @cjt/helloworld
version: 1.0.0
```
生成package.json :

```
{
  "name": "@cjt/helloworld",
  "version": "1.0.0",
  "description": "my first scoped package",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC"
}
```
注意，包名helloworld前必须要以公司名@cjt为前缀， @cjt在 config/index.js的scopes中配置

```
// registry scopes, if don't set, means do not support scopes
  scopes: [ '@faegroup','@cnpm', '@cnpmtest', '@cnpm-test' ],

  如果在管理员列表中添加用户在没有添加包的公司作用域也没有发布包的权限，修改 `middleware/publishable.js`中没有包作用域判断权限代码
```

### 发布

```
cnpm publish
```

### 使用

[查看已存在的包]( http://172.18.22.198:7002/)  http://172.18.22.198:7002/

- 设置私有npm源

```
npm login --registry http://10.12.116.40:7001 --scope=@faegroup
``` 
- 安装

```
//如果是@faegroup开头的包则在私有npm server中查找
npm install 
```

[npm 混合公共仓库和私有仓库](https://breeswish.org/blog/2016/02/16/npm-hybridize-public-and-private-repository/)