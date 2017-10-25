# install opensip server


## 1. install software package
```sh
# apt install gcc make flex bison libmysqlclient-dev mysql-server libncurses5-dev libncursesw5-dev git vim -y
```
You need set password for mysql.  // mysql2017


## 2. download opensips for git repo
open brows https://www.opensips.org/Downloads/Downloads

GIT clone of latest stable release (2.3):
```sh
# git clone https://github.com/OpenSIPS/opensips.git -b 2.3 opensips-2.3
```
## 3. configure opensips for install
```sh
# cd opensips-2.3
# make menuconfig
``` 


# install opensip server web 

## 1. install software package
```sh
# apt install apache2 php7.0 php7.0-mysql php7.0-xmlrpc php-pear -y
```




## in mysql from opensips-cp mysql

```sh
# mysql -Dopensips -p < opensips-cp/config/tools/admin/add_admin/ocp_admin_privileges.mysql
```
