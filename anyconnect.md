# Anyconnect 连接SS-Panel-mod-v3教程
###### 注意：本教程分为前端与后端，且默认前端与radius服务器为同一设备。
###### 本教程在写作时参考了 :
**https://www.zhaoj.in/read-2904.html**
**http://computingforgeeks.com/installing-freeradius-and-daloradius-centos-7/**
**https://mengyang.wang/anyconnect-node-mb/**
本教程基于Centos7.0 x86_64.
________
#### 前端部分
开始前，请确保你的ss-panel已经能够正常运行（数据库连接完毕，ss可以使用。）
###### 1. 安装radius
#
```yum -y install freeradius freeradius-utils freeradius-mysql```
###### 2. 新建一个数据库radius, 导入这张数据表：https://github.com/glzjin/Radius-install/raw/master/all.sql
#
```sql
    create database radius;
    source all.sql;
```
###### 3. 将radius与mysql连接
#
```shell
    ln -s /etc/raddb/mods-available/sql /etc/raddb/mods-enabled/
    vim /etc/raddb/mods-available/sql
```
把中间sql的一段改为：
```json
    sql {
        driver = "rlm_sql_mysql"
        dialect = "mysql"
        # Connection info:
        server = "localhost" 
        port = 3306  （改为你mysql的监听端口）
        login = "radius" （改为你的mysql用户名）
        password = "radiuspassword" （改为你的密码）
        # Database table configuration for everything except Oracle
        radius_db = "radius"
}
# Set to ‘yes’ to read radius clients from the database (‘nas’ table)
# Clients will ONLY be read on server startup.
read_clients = yes （这里的#注释要去掉）
# Table to keep radius client info
client_table = “nas”
```
然后回到shell，
```chgrp -h radiusd /etc/raddb/mods-enabled/sql```
###### 4. 配置radius的密钥
```vim /etc/raddb/clients.conf```
加上这样一段（可以直接全文涂掉重写）
```
    client everyone {
        ipaddr = 0.0.0.0
        proto = *
        secret = （你自己设的密钥，后面要用）
        require_message_authenticator = no
        nas_type = other
    }
```


    

