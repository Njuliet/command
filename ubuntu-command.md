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
127.0.0.1 devlive.maodou.io
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
	server_name devlive.maodou.io;
	listen 80;
	return 301 https://$host$request_uri;
}
server {
	server_name devlive.maodou.io;
	listen 443 ssl;
	ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
	ssl_ciphers 'ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA:ECDHE-RSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-RSA-AES256-SHA256:DHE-RSA-AES256-SHA:ECDHE-ECDSA-DES-CBC3-SHA:ECDHE-RSA-DES-CBC3-SHA:EDH-RSA-DES-CBC3-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:AES128-SHA256:AES256-SHA256:AES128-SHA:AES256-SHA:DES-CBC3-SHA:!DSS';
	ssl_prefer_server_ciphers on;
	ssl_session_timeout 5m;
	ssl_session_cache shared:SSL:50m;
	ssl_session_tickets off;
	ssl_certificate devlive.maodou.io/fullchain.pem;
	ssl_certificate_key devlive.maodou.io/key.pem;
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

其中，证书fullchain.pem的内容是：

`vim /usr/local/etc/nginx/devlive.maodou.io/fullchain.pem`

```
-----BEGIN CERTIFICATE-----
MIIGBjCCBO6gAwIBAgISAw+J4O66BFAWeC0qYFkRl9GKMA0GCSqGSIb3DQEBCwUA
MEoxCzAJBgNVBAYTAlVTMRYwFAYDVQQKEw1MZXQncyBFbmNyeXB0MSMwIQYDVQQD
ExpMZXQncyBFbmNyeXB0IEF1dGhvcml0eSBYMzAeFw0xNzA1MDMwMjI3MDBaFw0x
NzA4MDEwMjI3MDBaMBwxGjAYBgNVBAMTEWRldmxpdmUubWFvZG91LmlvMIICIjAN
BgkqhkiG9w0BAQEFAAOCAg8AMIICCgKCAgEAx+HYU8ay23I/Olx8wAnfwCpbYftT
UfbKzHbHRGRmvVrcMNu0WiesiZUEoyaJqiwMZBpRaa4kpeW+z47fx6ySpFTYNSJ7
dCJXFNsfhtGNjCK2qA7QTXpQ8BhNhGyjNzqlEZGo+dPKeQGdTOwQWT604Zl4A8Yn
7Ku4A2/PkesbQnFhMC3ggICs88ewzSN8cjmJ1ZJR4UfJt0EbY0KI9NRCgLPTMGis
qcP4nSZhjBv7G9valE+yPMyQAlztbah8BpNQyvPHL6+WGYwaBP9Wcg6SybWpbB7U
Y1syKU9py4ulmZvF87uqfcH3QXuh3NdSaSJP4F3YeNAB3KvoJJepb5Jx69l+7UBw
YrEfa/koMnbGMV3sQQ+tKSpJtXTRUz9VI8p6+Z10Ev62pyN+2MwZbsLD7T+v3J2k
R7HZo0/TUjYrAlw8HWpKtsLX0bGP+KKHHVSmDeBsF1M1tqL2TAsr2petA0HRYLnI
AXDXYG/yabr2/97wG+OUTJsPs/XkKBUH1URfybrHIVpa4QaZGjYP4aJCyUv4tI77
duCRZ2jbGc251Ku+JV6J3rqDDRqz49Sy5i+ib5eDr6/XVIcth9olgK+COBDT0AZL
1Jqm/fgjLtZlAD8rGD9g90z9Z4lN6RGh1iEb6dUyavl1r4X6zNTJEmT50/O5L4Li
ZsVKPP0g3cstPrcCAwEAAaOCAhIwggIOMA4GA1UdDwEB/wQEAwIFoDAdBgNVHSUE
FjAUBggrBgEFBQcDAQYIKwYBBQUHAwIwDAYDVR0TAQH/BAIwADAdBgNVHQ4EFgQU
QEGHrqsQLfIiJJ2MVMymCGSZrVEwHwYDVR0jBBgwFoAUqEpqYwR93brm0Tm3pkVl
7/Oo7KEwcAYIKwYBBQUHAQEEZDBiMC8GCCsGAQUFBzABhiNodHRwOi8vb2NzcC5p
bnQteDMubGV0c2VuY3J5cHQub3JnLzAvBggrBgEFBQcwAoYjaHR0cDovL2NlcnQu
aW50LXgzLmxldHNlbmNyeXB0Lm9yZy8wHAYDVR0RBBUwE4IRZGV2bGl2ZS5tYW9k
b3UuaW8wgf4GA1UdIASB9jCB8zAIBgZngQwBAgEwgeYGCysGAQQBgt8TAQEBMIHW
MCYGCCsGAQUFBwIBFhpodHRwOi8vY3BzLmxldHNlbmNyeXB0Lm9yZzCBqwYIKwYB
BQUHAgIwgZ4MgZtUaGlzIENlcnRpZmljYXRlIG1heSBvbmx5IGJlIHJlbGllZCB1
cG9uIGJ5IFJlbHlpbmcgUGFydGllcyBhbmQgb25seSBpbiBhY2NvcmRhbmNlIHdp
dGggdGhlIENlcnRpZmljYXRlIFBvbGljeSBmb3VuZCBhdCBodHRwczovL2xldHNl
bmNyeXB0Lm9yZy9yZXBvc2l0b3J5LzANBgkqhkiG9w0BAQsFAAOCAQEAicFm+5yJ
iSAdb3pMev/fqo11eHPEaUxxaSKWGCThkVPe7TzLH+AANr4H/T+Z7rgKSKvsaLR5
gmXbtcNQ/6htU3r2K0O/2vTJb2tZCY56Mg6LWttvBeW4AnF4Gt1E4uIyC6S6jwyw
fIESv+Ai1bWFTnlE0ISoNlEX7rZicv4lsQ0sfxkcRn0ub5fY8FU6MkJDyVCAj19c
qPZeNCmscMh00BZOOJNMfPw83S62rOh3Yl6124j2DdQEZ2MryAq/oADVNJrJmsBt
RWhKpCe0xw7tE+ngnscTnvNvMUs/j94+trmt5ZHATORRZ1ZibOkpuoOt8Fc42ICj
wEdTxQQbVqyOWA==
-----END CERTIFICATE-----
-----BEGIN CERTIFICATE-----
MIIEkjCCA3qgAwIBAgIQCgFBQgAAAVOFc2oLheynCDANBgkqhkiG9w0BAQsFADA/
MSQwIgYDVQQKExtEaWdpdGFsIFNpZ25hdHVyZSBUcnVzdCBDby4xFzAVBgNVBAMT
DkRTVCBSb290IENBIFgzMB4XDTE2MDMxNzE2NDA0NloXDTIxMDMxNzE2NDA0Nlow
SjELMAkGA1UEBhMCVVMxFjAUBgNVBAoTDUxldCdzIEVuY3J5cHQxIzAhBgNVBAMT
GkxldCdzIEVuY3J5cHQgQXV0aG9yaXR5IFgzMIIBIjANBgkqhkiG9w0BAQEFAAOC
AQ8AMIIBCgKCAQEAnNMM8FrlLke3cl03g7NoYzDq1zUmGSXhvb418XCSL7e4S0EF
q6meNQhY7LEqxGiHC6PjdeTm86dicbp5gWAf15Gan/PQeGdxyGkOlZHP/uaZ6WA8
SMx+yk13EiSdRxta67nsHjcAHJyse6cF6s5K671B5TaYucv9bTyWaN8jKkKQDIZ0
Z8h/pZq4UmEUEz9l6YKHy9v6Dlb2honzhT+Xhq+w3Brvaw2VFn3EK6BlspkENnWA
a6xK8xuQSXgvopZPKiAlKQTGdMDQMc2PMTiVFrqoM7hD8bEfwzB/onkxEz0tNvjj
/PIzark5McWvxI0NHWQWM6r6hCm21AvA2H3DkwIDAQABo4IBfTCCAXkwEgYDVR0T
AQH/BAgwBgEB/wIBADAOBgNVHQ8BAf8EBAMCAYYwfwYIKwYBBQUHAQEEczBxMDIG
CCsGAQUFBzABhiZodHRwOi8vaXNyZy50cnVzdGlkLm9jc3AuaWRlbnRydXN0LmNv
bTA7BggrBgEFBQcwAoYvaHR0cDovL2FwcHMuaWRlbnRydXN0LmNvbS9yb290cy9k
c3Ryb290Y2F4My5wN2MwHwYDVR0jBBgwFoAUxKexpHsscfrb4UuQdf/EFWCFiRAw
VAYDVR0gBE0wSzAIBgZngQwBAgEwPwYLKwYBBAGC3xMBAQEwMDAuBggrBgEFBQcC
ARYiaHR0cDovL2Nwcy5yb290LXgxLmxldHNlbmNyeXB0Lm9yZzA8BgNVHR8ENTAz
MDGgL6AthitodHRwOi8vY3JsLmlkZW50cnVzdC5jb20vRFNUUk9PVENBWDNDUkwu
Y3JsMB0GA1UdDgQWBBSoSmpjBH3duubRObemRWXv86jsoTANBgkqhkiG9w0BAQsF
AAOCAQEA3TPXEfNjWDjdGBX7CVW+dla5cEilaUcne8IkCJLxWh9KEik3JHRRHGJo
uM2VcGfl96S8TihRzZvoroed6ti6WqEBmtzw3Wodatg+VyOeph4EYpr/1wXKtx8/
wApIvJSwtmVi4MFU5aMqrSDE6ea73Mj2tcMyo5jMd6jmeWUHK8so/joWUoHOUgwu
X4Po1QYz+3dszkDqMp4fklxBwXRsW10KXzPMTZ+sOPAveyxindmjkW8lGy+QsRlG
PfZ+G6Z6h7mjem0Y+iWlkYcV4PIWL1iwBi8saCbGS5jN2p8M+X+Q7UNKEkROb3N6
KOqkqm57TH2H3eDJAkSnh6/DNFu0Qg==
-----END CERTIFICATE-----
```

密钥key.pem的内容是：

`vim /usr/local/etc/nginx/devlive.maodou.io/key.pem`

```
-----BEGIN PRIVATE KEY-----
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
ySRf0XE4SIx/9y/zlLMHgaHl9Dk+7Dc=
-----END PRIVATE KEY-----
```

如果你发现证书过期了，去服务器上取一个最新的证书，顺便帮忙更新一下本文。

用devlive的配置，在默认的3000端口启动meteor:

```
ROOT_URL=https://devlive.maodou.io meteor --settings .deploy/dev/settings-dev.json
```

在微信web开发者工具中可以通过公众号网页授权登录前台页面 https://devlive.maodou.io

在Chrome中可以通过微信扫码登录前台 https://devlive.maodou.io 或后台 https://devlive.maodou.io/admin

## 在手机上调试

手机要改hosts需要越狱，要拿开发机做proxy也经常不灵。所以最省事的办法是配置一台无线路由器，让开发机和手机都通过这个无线路由器上网。在路由器上改域名解析，比如极路由可以装一个插件叫“自定义hosts”，在里面加一条“192.168.199.101    devlive.maodou.io”即可。

## 调试微信支付

除了设置上述手机调试步骤之外，微信支付的统一下单接口有个notify_url参数，是异步接收微信支付结果通知的回调地址，由于回调地址必须为外网可访问的url，可以用ngrok做一个tunnel，notify_url参数填ngrok生成的url，这样本地程序就能接收到回调的通知了。注意在提交代码时把notify_url改回服务器的地址，比如 https://devlive.maodou.io/pay-cb 。

## 调试公众号消息接口

这个就复杂了。公众号消息高度依赖回调，而且回调地址是在公众号后台填写的，必须是已备案的域名，而ngrok生成的域名没法备案，所以就行不通了。有一个办法是在服务器上自己做这个tunnel，这样就可以用服务器的已备案域名来接收回调并tunnel到本地（参考 
 https://blog.rodneyrehm.de/archives/38-You-may-not-need-localtunnel-or-ngrok.html ）。但这样调试必然影响公众号的正常服务，所以只能在测试用的公众号上做。
