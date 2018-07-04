# conter install mysql 8.0

## 获取源

```sh
## CentOS 7 and Red Hat (RHEL) 7 ##
yum localinstall https://dev.mysql.com/get/mysql80-community-release-el7-1.noarch.rpm
        rpm -Uvh https://repo.mysql.com/mysql80-community-release-el7-1.noarch.rpm
```

## 安装命令

安装mysql8.0

```sh
yum install mysql-community-server
```


## 启动mysql
启动mysql和加入系统自动启动mysql

```sh
systemctl start mysqld.service       ## use restart after update
systemctl enable mysqld.service
```


##
