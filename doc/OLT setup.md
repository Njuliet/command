#OLT开局设置
##一、连接设备
```
$ telnet 10.11.104.2
Trying 10.11.104.2...
Connected to 10.11.104.2.

>>User name: root
>>User password: admin
WINDAKA-OLT>enable       //开启特权模式
WINDAKA-OLT#config       //开启全局配置模式
```
##二、配置OLT设备名
```
MA5680T(config)#sysname WINDAKA-OLT	            //设置OLT名称
WINDAKA-OLT(config)#terminal user name          //添加操作用户root
User Name(length<6,15>):windaka                 //设置用户名
User Password(length<6,15>):windaka.0532        //要求输入密码
Confirm Password(length<6,15>):windaka.0532     //要求再次确认一遍密码 
User profile name(<=15 chars)[root]:root        //输入用户管理级别
User's Level:
1. Common User  2. Operator  3. Administrator:3 //选择用户权限
Permitted Reenter Number(0--4):4                //设置用户可重复登录次数
User's Appended Info(<=30 chars):Windaka Administrator  //添加描述
Adding user succeeds
Repeat this operation? (y/n)[n]:n               //是否继续添加用户
WINDAKA-OLT(config)#
```
##三、确认业务板、上行板
```
WINDAKA-OLT(config)#display board 0 //查看当前已发现的插板，0代表框
WINDAKA-OLT(config)#board confirm 0 //确认单板，使OLT的EPON端口激光器打开
```

##四、创建DBA（Dynamically Bandwidth Assignment动态带宽分配）模板

> 类型(type)分为5种，分别是type1,type2,type3,type4,type5.其中：
>> type1 为固定带宽模式；
>> type2 为保证带宽模式；
>> type3 为保证带宽的同时设置最大带宽值；
>> type4 为仅设定最大带宽模式；
>> type5 为3种模式的综合，即设置最大带宽，在保证带宽的同时采用固定带宽模式。
 
```
WINDAKA-OLT(config)#dba-profile add profile-id 111  type3 assure 1024 max 102400
```
##五创建链路模板，ONU模板
```
WINDAKA-OLT(config)#ont-srvprofile gpon profile-name FTTH
WINDAKA-OLT(config-gpon-srvprofile-1)#ont-port eth 4 pots 2
WINDAKA-OLT(config-gpon-srvprofile-1)#port vlan eth 1 2
WINDAKA-OLT(config-gpon-srvprofile-1)#port vlan eth 2 3
WINDAKA-OLT(config-gpon-srvprofile-1)#port vlan eth 3 4
WINDAKA-OLT(config-gpon-srvprofile-1)#port vlan eth 4 5
WINDAKA-OLT(config-gpon-srvprofile-1)#commit
WINDAKA-OLT(config-gpon-srvprofile-1)#quit

WINDAKA-OLT(config)#ont-lineprofile gpon profile-name FTTH
WINDAKA-OLT(config)#tcont 1 dba-profile-id 111
WINDAKA-OLT(config)#
WINDAKA-OLT(config)#
WINDAKA-OLT(config)#
WINDAKA-OLT(config)#

WINDAKA-OLT(config-gpon-lineprofile-10)#tcont 0 dba-profile-id 512 //绑定t-cont与DBA模版
WINDAKA-OLT(config-gpon-lineprofile-10)#tcont 1 dba-profile-id 10 //TCONT 1 绑定DBA为10模版
WINDAKA-OLT(config-gpon-lineprofile-10)#tcont 2 dba-profile-id 10 //TCONT 2 绑定DBA为10模版
WINDAKA-OLT(config-gpon-lineprofile-10)#tcont 3 dba-profile-id 10 //TCONT 3 绑定DBA为10模版
WINDAKA-OLT(config-gpon-lineprofile-10)#tcont 4 dba-profile-id 10 //TCONT 4 绑定DBA为10模版
WINDAKA-OLT(config-gpon-lineprofile-10)#gem add 0 eth tcont 0 //建立GEM port，绑定相应的TCONT通道
WINDAKA-OLT(config-gpon-lineprofile-10)#gem add 1 eth tcont 1 //gem port 1 绑定TCONT 1
WINDAKA-OLT(config-gpon-lineprofile-10)#gem add 1 eth tcont 2 //gem port 2 绑定TCONT 1
WINDAKA-OLT(config-gpon-lineprofile-10)#gem add 1 eth tcont 3 //gem port 3 绑定TCONT 1
WINDAKA-OLT(config-gpon-lineprofile-10)#gem add 1 eth tcont 4 //gem port 4 绑定TCONT 1
WINDAKA-OLT(config-gpon-lineprofile-10)#gem mapping 0 0 vlan 888 //建立GEM Port端口映射，这里使用索引号与vlan映射。
WINDAKA-OLT(config-gpon-lineprofile-10)#gem mapping 1 0 vlan 101 //gemport 1 的第0个索引号与vlan101映射
WINDAKA-OLT(config-gpon-lineprofile-10)#gem mapping 2 0 vlan 102 //gemport 2 的第0个索引号与vlan102映射
WINDAKA-OLT(config-gpon-lineprofile-10)#gem mapping 3 0 vlan 101 //gemport 3 的第0个索引号与vlan103映射
WINDAKA-OLT(config-gpon-lineprofile-10)#gem mapping 4 0 vlan 101 //gemport 4 的第0个索引号与vlan104映射
WINDAKA-OLT(config-gpon-lineprofile-10)#commit
WINDAKA-OLT(config-gpon-lineprofile-10)#quit
```
##四、创建VLAN
```
WINDAKA-OLT(config)#vlan 100 smart  //创建网管VLAN 100
WINDAKA-OLT(config)#vlan 800 smart //创建对讲外层VLAN
WINDAKA-OLT(config)#vlan 1000 to 4000 smart //创建宽带外层VLAN
WINDAKA-OLT(config)#vlan attrib 1000 to 4000 q-in-q //设置宽带外层VLAN QinQ属性
WINDAKA-OLT(config)#vlan attrib 800 q-in-q //设置对讲外层VLAN QinQ属性
WINDAKA-OLT(config)#port vlan 2500 to 2600 0/17 0 //设置二层VLAN传给上行口
WINDAKA-OLT(config)#port vlan 800 0/18 0 //设置对讲VLAN传给上行口
```

##五、设置VLAN透传
```
WINDAKA-OLT(config)#interface giu 0/17 //进入17板
WINDAKA-OLT(config-if-giu-0/17)#


```










##六、创建业务模板
```
WINDAKA-OLT(config)#ont-srvprofile gpon profile-id 10
WINDAKA-OLT(config-gpon-srvprofile-10)#ont-port pots 1 eth 2 //ONT能力，支持1个电话业务，2个网络接口
WINDAKA-OLT(config-gpon-srvprofile-10)#commit
WINDAKA-OLT(config-gpon-srvprofile-10)#quit
```
##七、创建ONU业务
```
WINDAKA-OLT(config)#interface gpon 0/1
WINDAKA-OLT(config-if-gpon-0/1)#port 0 ont-auto-find enable
WINDAKA-OLT(config-if-gpon-0/1)#display ont autofind 0
WINDAKA-OLT(config-gpon-srvprofile-10)#quit
```
