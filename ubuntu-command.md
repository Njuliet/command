# kvmla 配置
web : meteor user
ritter : build user

### 创建一个sudo用户
```bash
# adduser 'ritter'
# adduser ritter sudo 
# adduser ritter root
# adduser ritter ssh
```

### 重新指定语言
```bash
$ sudo apt-get install localepurge
$ sudo locale-gen en_US.UTF-8 zh_CN.UTF-8
$ locale    //查看本地语言
```

### 自动补齐
```bash
$ sudo apt-get install bash-completion
$ sudo apt-get install --reinstall bash-completion
$ cp /etc/skel/.bashrc ~/
$ . ~/.bashrc
```

### 安装fish
```bash
$ sudo install fish
$ fish
```
### update
```
$ sudo apt-get update
E: The method driver /usr/lib/apt/methods/http could not be found.
```
Fixed:
```bash
$ sudo apt-get install apt-transport-https
$ sudo apt-get update
```

### 将程序放到后台运行
```bash
$ nohup meteor &
```


## 安装ShadowSocks
简介`Shadowsocks`安装配置过程。

### ubuntu 安装方法

```
 $ sudo apt-get update 
 $ sudo apt-get install python-pip
 $ sudo apt-get shadowsocks
```
### 配置文件
需要在`/etc/`目录下创建一个`shadowsocks.json`的文件, 配置文件内容如下:
```
{
    "server":"my_server_ip",
    "server_port":8388,
    "local_address": "127.0.0.1",
    "local_port":1080,
    "password":"mypassword",
    "timeout":300,
    "method":"rc4-md5"
}
```
各字段的含义：

|name	|info|
| :------ | :--------------------------------: |
|server	|服务器 IP (IPv4/IPv6)，注意这也将是服务端监听的 IP 地址|
|server_port|	服务器端口|
|local_port|	本地端端口|
|password|用来加密的密码|
|timeout|	超时时间（秒）|
|method|加密方法，"rc4-md5"|

### 运行方法
如果想直接在命令行下运行, 如果要停止运行，将命令中的start改成stop。

```
$ sudo ssserver -c /etc/shadowsocks.json -d start
```

### 配置开机自启动

```
$ sudo chmod 755 /etc/shadowsocks.json
$ sudo apt–get install python–m2crypto   //安装支持算法的库
```
需要将启动项增加到, 启动文件中.
```
$ sudo vi /etc/rc.local

/usr/local/bin/ssserver –c /etc/shadowsocks.json
```


# Ubuntu config

## 语言设置
如果没有设置好语言启动不了`meteor`, 会显示`mongoDB`服务启动失败.

解决方法如下:
```
$ sudo apt-get install locales
$ sudo locale-gen en_US.UTF-8
$ sudo localedef -i en_GB -f UTF-8 en_US.UTF-8 
```


## 开发机配置

修改/etc/hosts，加上：

```
127.0.0.1 dev.badui.com
```
#### 推荐一个频繁切换hosts的小工具[[SwitchHosts](http://oldj.github.io/SwitchHosts/)]
< img src='https://cloud.githubusercontent.com/assets/4111893/26032565/0e388dd2-38c9-11e7-8230-c595534dec95.png' width='300px'></img>

---
在本地安装nginx，配置文件加上：

```
map $http_upgrade $connection_upgrade {
    default upgrade;
    ''      close;
}
server {
	server_name dev.badui.com;
	listen 80;
	return 301 https://$host$request_uri;
}
server {
	server_name dev.badui.com;
	listen 443 ssl;
	ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
	ssl_ciphers 'ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA:ECDHE-RSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-RSA-AES256-SHA256:DHE-RSA-AES256-SHA:ECDHE-ECDSA-DES-CBC3-SHA:ECDHE-RSA-DES-CBC3-SHA:EDH-RSA-DES-CBC3-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:AES128-SHA256:AES256-SHA256:AES128-SHA:AES256-SHA:DES-CBC3-SHA:!DSS';
	ssl_prefer_server_ciphers on;
	ssl_session_timeout 5m;
	ssl_session_cache shared:SSL:50m;
	ssl_session_tickets off;
	ssl_certificate cert/xxx.pem;
	ssl_certificate_key cert/xxx.key;
	add_header Strict-Transport-Security "max-age=31536000";
	location / {
		proxy_pass http://127.0.0.1:3000;
        	proxy_http_version 1.1;
        	proxy_set_header   Upgrade          $http_upgrade;
        	proxy_set_header   Connection       $connection_upgrade;

        	proxy_set_header   Host             $host;
        	proxy_set_header   X-Real-IP        $remote_addr;
        	proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;
        	proxy_set_header   X-Forwarded-Proto $scheme;

        	proxy_buffering off;
        	proxy_buffer_size  128k;
        	proxy_buffers   32 32k;
        	proxy_busy_buffers_size 128k;
        	proxy_connect_timeout 1200s;
        	proxy_read_timeout 1200s;
        	proxy_send_timeout 1200s;
        	proxy_redirect off;
        	proxy_next_upstream error timeout invalid_header http_500 http_503 http_404;
        	expires 1d;
	}
}
```


如果你发现证书过期了，去服务器上取一个最新的证书，顺便帮忙更新一下本文。

用devlive的配置，在默认的3000端口启动meteor:

```
ROOT_URL=https://dev.badui.com meteor --settings .deploy/dev/settings-dev.json
```

在微信web开发者工具中可以通过公众号网页授权登录前台页面 https://dev.badui.com

在Chrome中可以通过微信扫码登录前台 https://dev.badui.com 或后台 https://dev.badui.com/admin

## 在手机上调试

手机要改hosts需要越狱，要拿开发机做proxy也经常不灵。所以最省事的办法是配置一台无线路由器，让开发机和手机都通过这个无线路由器上网。在路由器上改域名解析，比如极路由可以装一个插件叫“自定义hosts”，在里面加一条“192.168.199.101    dev.badui.com”即可。

## 调试微信支付

除了设置上述手机调试步骤之外，微信支付的统一下单接口有个notify_url参数，是异步接收微信支付结果通知的回调地址，由于回调地址必须为外网可访问的url，可以用ngrok做一个tunnel，notify_url参数填ngrok生成的url，这样本地程序就能接收到回调的通知了。注意在提交代码时把notify_url改回服务器的地址，比如 https://dev.badui.com/pay-cb 。

## 调试公众号消息接口

这个就复杂了。公众号消息高度依赖回调，而且回调地址是在公众号后台填写的，必须是已备案的域名，而ngrok生成的域名没法备案，所以就行不通了。有一个办法是在服务器上自己做这个tunnel，这样就可以用服务器的已备案域名来接收回调并tunnel到本地（参考 
 https://blog.rodneyrehm.de/archives/38-You-may-not-need-localtunnel-or-ngrok.html ）。但这样调试必然影响公众号的正常服务，所以只能在测试用的公众号上做。
