# 记一次  oneinstack 配置服务器

## 引言

因为自己是大学生，所以一直都是用的腾讯云学生机，但是前几天腾讯突然告诉我腾讯云学生认证到期了，可能是当时填错了，没办法我就又申请了学生认证😂，谁让我还是学生呢，但是以前老的学生优惠的学生机就不能再按照学生优惠的方式续费了，所以我又买了一个新的服务器。买完服务器准备手动开始各种配置的时候便发现了一个神器，也就是接下来要介绍给大家的。就是 [oneinstack](https://oneinstack.com/) ,可以傻瓜式「一键」安装，想看碰到的 bug 及一些解决方案可以直接跳过配置服务器部分。<a href="#遇到的问题">「jump」</a>

## 配置服务器环境

我写此博客时 oneinstack 的版本为 2018-04-24, 其使用方式如下

### 基本安装

```shell
yum -y install wget screen curl python #for CentOS/Redhat
# apt-get -y install wget screen curl python #for Debian/Ubuntu

# 下载源码与脚本
wget http://mirrors.linuxeye.com/oneinstack-full.tar.gz #包含源码，国内外均可下载

# 解压
tar xzf oneinstack-full.tar.gz

# 如果需要修改目录(安装、数据存储、Nginx日志)，请修改options.conf文件
cd oneinstack 

# 如果网路出现中断，可以执行命令`screen -R oneinstack`重新连接安装窗口
screen -S oneinstack 

# 注：请勿sh install.sh或者bash install.sh这样执行
./install.sh 

# 接下来根据其指示和自己喜好安装需要的软件即可
```

### node 安装

oneinstack 不支持 node 环境的安装，作为一个前端er, node 还是不可获取的，以前我配置 node 都是去官网下载然后解压然后建立全局软链接的形式，这次也是换了一个更简单的方式

#### node.js v.8x 安装命令

```shell
#curl --silent --location https://rpm.nodesource.com/setup_8.x | bash -
```

#### 安装node

```shell
yum install -y nodejs
```

#### 查看 nodejs 安装版本

```shell
node -v
```

至此 node 就已经安装完成，也是全局变量，相比以前也省了很多时间和力气，如果需要也可以安装 nodejs 的版本管理工具，但是在这里我没有安装，正确与否我没有尝试

### node.js版本管理器n

#### 安装n

```
npm install -g n1
```

#### 安装指定版本

```
#n <version>1
```

#### 安装最新版本

```
# n latest1
```

#### 安装稳定版本

```
#n stable1
```

#### 删除某个版本

```
#n rm <version> 1
```

#### 查看所有版本

```
#n list
```

## 其他配置

### 添加附加组件

```shell
./addons.sh
```

### 创建 FTP 虚拟用户账户

```shell
./pureftpd_vhost.sh
```

### 增删虚拟主机

```shell
# 增加虚拟主机./vhost.sh# 删除虚拟主机
./vhost.sh
```

### 备份和自动备份

```shell
# Set backup options ./backup_setup.sh # Start backup, You can add cron jobs./backup.sh # crontab -l # Examples 0 1 * * * cd ~/oneinstack;./backup.sh  > /dev/null 2>&1 &
```

### 更新版本

```shell
./upgrade.sh
```

### 卸载

```shell
./uninstall.sh
```

## 管理服务

Nginx/Tengine/OpenResty:

```
service nginx {start|stop|status|restart|reload|configtest}
```

MySQL/MariaDB/Percona:

```
service mysqld {start|stop|restart|reload|status}
```

PostgreSQL:

```
service postgresql {start|stop|restart|status}
```

MongoDB:

```
service mongod {start|stop|status|restart|reload}
```

PHP:

```
service php-fpm {start|stop|restart|reload|status}
```

HHVM:

```
service supervisord {start|stop|status|restart|reload}
```

**注**：hhvm进程交给supervisord管理，了解更多请访问《[Supervisor管理hhvm进程](https://blog.linuxeye.com/408.html)》
Apache:

```
service httpd {start|restart|stop}
```

Tomcat:

```
service tomcat {start|stop|status|restart}
```

Pure-Ftpd:

```
service pureftpd {start|stop|restart|status}
```

Redis:

```
service redis-server {start|stop|status|restart}
```

Memcached:

```
service memcached {start|stop|status|restart|reload}
```

## 启用 mysql 服务

oneinstack 默认仅允许主机本机链接数据库，需要远程连接数据库的话，需要打开对应端口

### 打开 3306 端口

CentOS 系统

```shell
iptables -I INPUT 4 -p tcp -m state --state NEW -m tcp --dport 3306 -j ACCEPT # 允许访问 3306
service iptables save #保存 iptables 规则
iptables -nvl # 查看 iptables 规则
```

Ubuntu/Debian 

```shell
iptables -I INPUT 4 -p tcp -m state --state NEW -m tcp --dport 3306 -j ACCEPT # 允许访问 3306
iptables-save > /etc/iptables.up.rules #保存 iptables 规则
```

### 数据库授权

#### MySQL8.0版本

```shell
# mysql -uroot -p
 MySQL [(none)]> create user db_user@'%' identified by 'db_pass'; #创建用户
 MySQL [(none)]> grant all privileges on db_name.* to db_user@'%' with grant option; #授权
 MySQL [(none)]> exit; #退出数据库控制台，特别注意有分号
```

#### 其余MySQL版本

```shell
# mysql -uroot -p
 MySQL [(none)]> grant all privileges on db_name.* to db_user@'%' identified by 'db_pass'; #授权语句，特别注意有分号
 MySQL [(none)]> flush privileges;
 MySQL [(none)]> exit; #退出数据库控制台，特别注意有分号
```

## 服务器https

以前的服务器 https 都是自己上的，使用 oneinstack 可以一键让自己的服务器带点绿 :green_apple: 

运行`./vhost.sh` 就会跳到配置虚拟环境界面，oneinstack 采用 Let's Encrypt https 证书，免费使用为 90 天，到期后 oneinstack 会自动帮你续费，配置完毕后在默认文件夹里添加项目就可以输入网址以 https 的方式访问

## 遇到的问题

### 无法访问带端口域名

我自己创建了一个二级域名 api.×××× 来作为我一些 api 接口的提供网址，然后想用 oneinstack 为其上https，向服务器中上传 node 文件用 pm2 跑起来后，本来在本地 localhost:3000 可以访问的文件，到了服务器后无法通过 api.××××:3000 来访问，最开始想到了类似阿里云安全组的问题，然后我去后台看了下我的服务器的安全组，并没有禁用 3000 端口。后来脑子里自己冒出来了一个想法，用 nginx 反向代理，在这里我就不具体给出反向代理的代码了，格式很简单，就是访问 api.××××/getApi 相当于访问 api.××××:3000/getApi ,然后竟然就成功访问到了 :zap:

2018/9/10: oneinstack 默认端口都是不开放的，因此我感觉是未将 3000 端口开放的原因

### 引用 api 碰到的跨域问题

主域名下的项目引用 api.×××× 下的 api 是要跨域的，谷歌搜索到的解决方案基本是如下代码

```js
app.all('*', function(req, res, next) {
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "Content-Type,Content-Length, Authorization, Accept,X-Requested-With");
    res.header("Access-Control-Allow-Methods","PUT,POST,GET,DELETE,OPTIONS");
    res.header("X-Powered-By",' 3.2.1')
    if(req.method=="OPTIONS") res.send(200);/*让options请求快速返回*/
    else  next();
});
```

但是并没有解决我的跨域问题，我是在 nginx 反向代理时加入如下代码实现的

```conf
location / {  
    add_header Access-Control-Allow-Origin *;
	...
} 
```

### https 引用 http 下的接口报错

我的 api.×××× 域名在开始的时候上 https 并没有成功，我就使用的是 http 协议，但是解决了跨域问题后又报错，报错的内容大致就是 https 下无法引用 http 中的接口，最后我其实换了一个域名又上了 https ... :joy_cat:

## 最后

最后基本项目都已部署到了服务器上，访问项目也有耀眼的小绿标，还是 hin 开心的