# 记一次  oneinstack 配置服务器

## 引言

因为自己是大学生，所以一直都是用的腾讯云学生机，但是前几天腾讯突然告诉我腾讯云学生认证到期了，可能是当时填错了，没办法我就又申请了学生认证😂，谁让我还是学生呢，但是以前老的学生优惠的学生机就不能再按照学生优惠的方式续费了，所以我又买了一个新的服务器。买完服务器准备手动开始各种配置的时候便发现了一个神器，也就是接下来要介绍给大家的。就是 [oneinstack](https://oneinstack.com/) ,可以傻瓜式「一键」安装

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

## 服务器https

以前的服务器 https 都是自己上的，使用 oneinstack 可以一键让自己的服务器带点绿 :green_apple: 

运行`./vhost.sh` 就会跳到配置虚拟环境界面，oneinstack 采用 Let's Encrypt https 证书，免费使用为 90 天，到期后 oneinstack 会自动帮你续费，配置完毕后在默认文件夹里添加项目就可以输入网址以 https 的方式访问

## 有关 https 碰到的问题

我自己创建了一个二级域名 api.haoqinzz.cn 来作为我一些 api 接口的提供网址，然后想用 oneinstack 为其上 https，但是一直失败，我就索性直接创建 http 试一下，结果成功了，然后我讲我的接口服务迁移至此用 pm2 启动后网址输入 test