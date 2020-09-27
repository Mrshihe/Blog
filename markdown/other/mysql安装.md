### windows10系统下Mysql安装
#### 下载Mysql(以8.0.19为例)
[下载地址](https://dev.mysql.com/downloads/mysql/)

### 将zip解压到指定文件夹下，新建my.ini文件放在根目录下，my.ini如下
```
[mysqld]
# 设置3306端口
port=3306
# 设置mysql的安装目录, 修改为自己的解压目录
basedir=C:\Program Files\MySQL
# 设置mysql数据库的数据的存放目录, 修改为自己的解压目录
datadir=C:\Program Files\MySQL\Data
# 允许最大连接数
max_connections=200
# 允许连接失败的次数。
max_connect_errors=10
# 服务端使用的字符集默认为utf8mb4
character-set-server=utf8mb4
# 创建新表时将使用的默认存储引擎
default-storage-engine=INNODB
# 默认使用“mysql_native_password”插件认证
#mysql_native_password
default_authentication_plugin=mysql_native_password
[mysql]
# 设置mysql客户端默认字符集
default-character-set=utf8mb4
[client]
# 设置mysql客户端连接服务端时默认使用的端口
port=3306
default-character-set=utf8mb4
```
utf8mb4 为utf8超集包括了Emoji表情
不需要手动创建Data文件夹

### 在系统变量path中添加mysql变量,即mysql安装目录下的bin目录 （如E:\mysql\bin）

### 初始化Mysql,进入Mysql解压目录下的bin目录
> 用管理员身份运行CMD，而不是powershell。有些命令再powershell中无效 如 sc delete mysql

- 执行 mysqld --initialize --console 成功后后生成初始密码 A temporary password ...... root@localhost: *********(为初始密码)
- 执行 mysqld --install [服务名] 服务名可选，默认为mysql  提示 Service successfully installed.则为成功
- 上步中如果出现 The service already exists! 说明mysql服务存在，此时执行 sc delete mysql 删除mysql, 提示 [SC] DeleteService 成功 后再次执行 mysqld --install
- 执行 net start mysql (mysql为上步骤的服务名,默认为mysql) 提示 Mysql服务已经启动成功则表示mysql已经装好

### 修改初始密码
- 执行 mysql -u root -p ，提示输入密码后，输入初始密码后回车，显示 mysql> 则为进入mysql成功
- 执行 ALTER USER 'root'@'localhost' IDENTIFIED BY '新密码'; 成功后 执行 exit; 可退出 