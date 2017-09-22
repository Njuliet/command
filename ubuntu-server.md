
## ubuntu 16.04 64位
需要新更新系统.
```sh
# apt update
$ apt install -y build-essential
```

### install node.js 
install node.js for v6.11.3

1. 需要配置node.js源, 添加到服务器中.
```ssh
$ curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash - 
$ apt install -y nodejs
```


### install mongodb

install mongodb enterprise

1. Import the public key used by the package management system.
```sh
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6
```

2. Create a /etc/apt/sources.list.d/mongodb-enterprise.list file for MongoDB.
```sh
echo "deb [ arch=amd64,arm64,ppc64el,s390x ] http://repo.mongodb.com/apt/ubuntu xenial/mongodb-enterprise/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-enterprise.list
```
3. Reload local package database.
```sh
sudo apt-get update
```

4. Install the MongoDB Enterprise packages.
```sh
sudo apt-get install -y mongodb-enterprise
```

5. Rebmove Data
```sh
apt-get purge mongodb-enterprise*
```
delete log file

```sh
sudo rm -r /var/log/mongodb
sudo rm -r /var/lib/mongodb
```


mongodb command:
1. start mongodb
```sh
service mongod start
```

mongodb config:
```sh
# vim /etc/mongodb.conf
```


### nginx

### redis

### mysql

