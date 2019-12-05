# 记一次 oneinstack 配置服务器

## 引言

自己的服务器因为换过好多次，每次搭建又格外费时，在网上闲逛时发现了 [oneinstack](https://oneinstack.com/auto/) 这个超好用的工具 ,可以傻瓜式「一键」安装。

## node 安装

oneinstack 不支持 node 环境的安装，在这里

```shell
yum install -y nodejs
```

## 配置防火墙

如果此时你输入公网 ip， 发现无法访问页面， 那可能是端口未开启。 CentOS 7.0 中已经用 firewalld 取代 iptables，下面能使用 firewalld 启动防火墙

```
systemctl start firewalld.service
```

输入以上命令，报了一个错误 「 Failed to start firewalld.service: Unit firewalld.service is masked.」，执行下面的命令的命令后再启动防火墙

```
systemctl unmask firewalld.service
```

然后把 80 端口和 443 端口添加到防火墙的开放端口中

```
firewall-cmd --permanent --zone=public --add-port=80/tcp
firewall-cmd --permanent --zone=public --add-port=443/tcp
```

然后重启一遍防火墙后生效

```
systemctl restart firewalld.service
```

## 配置安全组

https://oneinstack.com/docs/securitygroup/#11

配置完后安全组记得**添加实例**，或者在实例中直接配置安全组

## 最后

然后访问实例的公网 ip， 就可以看到 oneinstack 的首页
