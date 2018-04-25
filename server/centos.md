


# redis install

In this section you’ll add the EPEL repository, and then use it to install Redis.

Add the EPEL repository, and update YUM to confirm your change:

```sh
sudo yum install epel-release
sudo yum update
```

Install Redis:

```sh
sudo yum install redis
```

Start Redis:

```sh
sudo systemctl start redis
```

Optional: To automatically start Redis on boot:

```sh
sudo systemctl enable redis
```

Verify the Installation
Verify that Redis is running with redis-cli:

```sh
redis-cli -h 127.0.0.1 -p 5004
```

配置`redis`文件如下:
```sh
sudo vim /etc/redis.conf
```







