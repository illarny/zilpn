# 安装 Centos 7.4

## 选择安装 
Install Centos 7

## 选择语言 
English->English(United States)，点击“Continue”

## 选择硬盘 
一般是最大的那个，如果原有的硬盘格式不符合，直接“Delete all”

## 设置密码,比如：ccbti1008

## 重启登录

## 连接网线，查看网络 
```bash
ping www.baidu.com
```

## 如果没有网络,自动获取一个ip
```bash
dhclient
```


# 安装 mysql,php,nginx,redis

### 安装工具，如果是阿里云，默认有不需要自己安装
```bash
yum install net-tools
yum install wget
```

### 创建用户组和用户 www
```bash
groupadd www
useradd -g www -d /home/www -m www
```

### 安装 mysql
```bash
wget http://repo.mysql.com/mysql-community-release-el7-5.noarch.rpm
rpm -ivh mysql-community-release-el7-5.noarch.rpm
ls -1 /etc/yum.repos.d/mysql-community*
# 安装
yum install mysql-server
# 启动服务
service mysqld start
# 登录
mysql -uroot -p
```

### 设定mysql字符集，支持utf8mb4
```mysql
mysql> -- 设定字符集;
mysql> show variables like 'character%';
-- 如果不是 utf8mb4，设定
SET character_set_client = utf8mb4;
SET character_set_connection = utf8mb4;
SET character_set_database = utf8mb4;
SET character_set_results = utf8mb4;
SET character_set_server = utf8mb4;
```

### 安装 php7.2
```bash
# 设定yum源
rpm -Uvh https://mirror.webtatic.com/yum/el7/epel-release.rpm
rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm
# 安装 php7.2
yum install php72w-common php72w-fpm php72w-opcache php72w-gd php72w-mysqlnd php72w-mbstring php72w-pecl-redis php72w-pecl-memcached php72w-devel php72w-bcmath php72w-soap
```

### 修改php时区
```bash
vim /etc/php.ini
#  date.timezone = "Asia/Shanghai"
```

### 修改php-fpm配置，修改用户和组
```bash
vim /etc/php-fpm.d/www.conf
# 将 user = apache 和 group = apache 改为:
#  user = www
#  group = www
```

### 给php设置session目录，后台用
```bash
cd /var/lib;
mkdir /var/lib/php/session;
chown -R www:www /var/lib/php/session;
chown -R www:www php;
chmod -R 777 php;
# 重启php-fpm
service php-fpm restart
```

### 安装 redis
```bash
yum install redis
# 修改 redis.conf 文件 
# 禁止外网访问 Redis，添加或修改，如果绑定的是127.0.0.1则不需要修改，
# 一般不用修改，但是要检查一下
#  bind 127.0.0.1
vim /etc/redis.conf
## 启动redis
redis-server /etc/redis.conf &
```

### 安装 nginx
```bash
yum install nginx
nginx -t
nginx

# 使用ip:port测试，比如本机ip 192.168.31.109，在浏览器中输入 192.168.31.109
# 如果无法访问 可能是80端口问开启 开启
firewall-cmd --zone=public --add-port=80/tcp --permanent
systemctl restart firewalld.service

# 重启nginx 再次浏览器测试
nginx -s reload
```


# 拉取并部署代码

### 安装git
```bash
yum install git
```

### clone代码
```bash
cd /home/www
mkdir /home/www/webroot/ccbti
cd /home/www/webroot/ccbti
git clone http://gitlab.zilpn.com:19522/cbti/backend.git
# 输入代码库的用户名和密码
```

### 初始化代码
```bash
cd /home/www/webroot/ccbti/backend
php init
# 选择 0 开发模式
```

### 配置代码
```bash
vim /home/www/webroot/ccbti/backend/common/config/main-local.php
# 修改数据库名称





## 如果访问浏览器出现“Access denied.”，可能需要关闭防火墙
setenforce 0