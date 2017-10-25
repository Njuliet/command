# install opensip server


## 1. install software package
```bash
# apt install gcc make flex bison libmysqlclient-dev mysql-server libncurses5-dev libncursesw5-dev git vim -y
```
You need set password for mysql.  // mysql2017


## 2. download opensips for git repo
open brows https://www.opensips.org/Downloads/Downloads

GIT clone of latest stable release (2.3):
```bash
# git clone https://github.com/OpenSIPS/opensips.git -b 2.3 opensips-2.3
```
## 3. configure opensips for install
```bash
# cd opensips-2.3
# make menuconfig
``` 


# install opensip server web 

## 1. install software package
```bash
# apt install apache2 php7.0 php7.0-mysql php7.0-xmlrpc php-pear php7.0-gd php7.0-cli -y
```




## in mysql from opensips-cp mysql

```bash
# mysql -Dopensips -p < opensips-cp/config/tools/admin/add_admin/ocp_admin_privileges.mysql
```
## register user admin 

```bash
# mysql -u root -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 44
Server version: 5.7.20-0ubuntu0.16.04.1 (Ubuntu)

Copyright (c) 2000, 2017, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| opensips           |
| performance_schema |
| sys                |
+--------------------+
5 rows in set (0.02 sec)

mysql> use opensips
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> insert into ocp_admin_privileges (username,password,ha1,available_tools,permissions)values('admin', 'admin',md5('admin:admin'),'all','all');
Query OK, 1 row affected (0.00 sec)
```
