# conter install mysql 8.0

## 获取源

```sh
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


## 查看临时密码

```sh
grep 'A temporary password is generated for root@localhost' /var/log/mysqld.log |tail -1
```



## config mysql

```
/usr/bin/mysql_secure_installation
```

output:

```
Securing the MySQL server deployment.

Enter password for user root: 

The existing password for the user account root has expired. Please set a new password.

New password: 

Re-enter new password: 

VALIDATE PASSWORD PLUGIN can be used to test passwords
and improve security. It checks the strength of password
and allows the users to set only those passwords which are
secure enough. Would you like to setup VALIDATE PASSWORD plugin?

Press y|Y for Yes, any other key for No: y

There are three levels of password validation policy:

LOW    Length >= 8
MEDIUM Length >= 8, numeric, mixed case, and special characters
STRONG Length >= 8, numeric, mixed case, special characters and dictionary                  file

Please enter 0 = LOW, 1 = MEDIUM and 2 = STRONG: 0
Using existing password for root.

Estimated strength of the password: 100 
Change the password for root ? ((Press y|Y for Yes, any other key for No) : y

New password: 

Re-enter new password: 

Estimated strength of the password: 50 
Do you wish to continue with the password provided?(Press y|Y for Yes, any other key for No) : y
By default, a MySQL installation has an anonymous user,
allowing anyone to log into MySQL without having to have
a user account created for them. This is intended only for
testing, and to make the installation go a bit smoother.
You should remove them before moving into a production
environment.

Remove anonymous users? (Press y|Y for Yes, any other key for No) : y
Success.


Normally, root should only be allowed to connect from
'localhost'. This ensures that someone cannot guess at
the root password from the network.

Disallow root login remotely? (Press y|Y for Yes, any other key for No) : y
Success.

By default, MySQL comes with a database named 'test' that
anyone can access. This is also intended only for testing,
and should be removed before moving into a production
environment.


Remove test database and access to it? (Press y|Y for Yes, any other key for No) : y
 - Dropping test database...
Success.

 - Removing privileges on test database...
Success.

Reloading the privilege tables will ensure that all changes
made so far will take effect immediately.

Reload privilege tables now? (Press y|Y for Yes, any other key for No) : y
Success.

All done! 
```


## 本地连接数据库

```
mysql -u root -p

## OR ##
mysql -h localhost -u root -p
```


## 创建数据库和用户

* DB_NAME = webdb
* USER_NAME = webdb_user
* REMOTE_IP = 10.0.15.25
* PASSWORD = password123
* PERMISSIONS = ALL

```mysql
## CREATE DATABASE ##
mysql> CREATE DATABASE webdb;

## CREATE USER ##
mysql> CREATE USER 'webdb_user'@'10.0.15.25' IDENTIFIED BY 'password123';

## GRANT PERMISSIONS ##
mysql> GRANT ALL ON webdb.* TO 'webdb_user'@'10.0.15.25';

##  FLUSH PRIVILEGES, Tell the server to reload the grant tables  ##
mysql> FLUSH PRIVILEGES;
```

## 修密码等级

如果设置出现 `ERROR 1819 (HY000): Your password does not satisfy the current policy requirements` 错误信息, 是因为密码的等级设置过高, 需要将安全等降低或是重新用随机软件生成密码.

```mysql

## 查看密码安全等级
mysql> set global validate_password.policy=0;
Query OK, 0 rows affected (0.00 sec)

mysql> SHOW VARIABLES LIKE 'validate_password%';
+--------------------------------------+-------+
| Variable_name                        | Value |
+--------------------------------------+-------+
| validate_password.check_user_name    | ON    |
| validate_password.dictionary_file    |       |
| validate_password.length             | 8     |
| validate_password.mixed_case_count   | 1     |
| validate_password.number_count       | 1     |
| validate_password.policy             | LOW   |
| validate_password.special_char_count | 1     |
+--------------------------------------+-------+
14 rows in set (0.00 sec)

## 创建新用户
mysql> create user 'user'@'%' identified by 'abcd1234';
Query OK, 0 rows affected (0.08 sec)

````


## 开启远程连数据库

### 1. Fedora 28/27/26 and CentOS/Red Hat (RHEL) 7.5

1.1 Add New Rule to Firewalld
```
firewall-cmd --permanent --zone=public --add-service=mysql
## OR ##
firewall-cmd --permanent --zone=public --add-port=3306/tcp
````

1.2 Restart firewalld.service
```
systemctl restart firewalld.service
```

### 2. CentOS/Red Hat (RHEL) 6.9

2.1 Edit /etc/sysconfig/iptables file:
```
nano -w /etc/sysconfig/iptables
```

2.2 Add following INPUT rule:
```
-A INPUT -m state --state NEW -m tcp -p tcp --dport 3306 -j ACCEPT
```

2.3 Restart Iptables Firewall:
```
service iptables restart
## OR ##
/etc/init.d/iptables restart
```

### 3. Test remote connection

```
mysql -h 10.0.15.25 -u myusername -p
````

Posted in Featured, Linux, Servers, SQL
Tagged CentOS, Fedora, MySQL, Oracle, Red Hat, RHEL, Scientific Linux



