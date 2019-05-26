## hue 简介

### 安装

#### 下载hue

`apt install git ant gcc g++ libkrb5-dev libmysqlclient-dev libssl-dev libsasl2-dev libsasl2-modules-gssapi-mit libsqlite3-dev libtidy-0.99-0 libxml2-dev libxslt-dev make libldap2-dev maven python-dev python-setuptools libgmp3-dev`

`sudo apt-get install libffi-dev`

```
cd hue
build/env/bin/hue runserver
```

#### 配置 hue

desktop/conf/hue.ini

secret_key 随便写

http_host=主机名

http_port=8888

time_zone=Asia/Shanghai

### hdfs 

hdfs-site.xml

dfs.webhdfs.enabled  true

core-site.xml

hadoop.proxyuser.httpfs.hosts   *

hadoop.proxyuser.httpfs.groups   *

httpfs-site.xml

httpfs.proxyuser.hue.hosts            httpfs.proxyuser.hue.groups           *

#### 启动httpfs 服务

`/usr/local/hadoop2.7.7/sbin/httpfs.sh start`

#### hue.ini  

找到hadoop 标签

```
      fs_defaultfs=hdfs://hadoop-1:9000
      webhdfs_url=http://hadoop-1:14000/webhdfs/v1
     
```

#### 运行

build/env/bin/hue runserver

#### 修改用户

/opt/modules/cdh/hue-3.7.0-cdh5.3.6/desktop/libs/hadoop/src/hadoop/fs/webhdfs.py

DEFAULT_HDFS_SUPERUSER = 'admin'

### hue 和yarn

 # Enter the host on which you are running the ResourceManager
```ini
  resourcemanager_host=hadoop-3

  # The port where the ResourceManager IPC listens on
  resourcemanager_port=8032

  # Whether to submit jobs to this cluster
  submit_to=True

  # Resource Manager logical name (required for HA)
  ## logical_name=

  # Change this if your YARN cluster is Kerberos-secured
  ## security_enabled=false

  # URL of the ResourceManager API
  resourcemanager_api_url=http://hadoop-3:8088

  # URL of the ProxyServer API
  proxy_api_url=http://hadoop-3:8088

  # URL of the HistoryServer API
  history_server_api_url=http://hadoop-1:19888
```
### hive hue

```xml

	<property>
  		<name>hive.server2.thrift.port</name>
  		<value>10000</value>	
	</property>

	<property>
  		<name>hive.server2.thrift.bind.host</name>
  		<value>hadoop-1</value>	
	</property>

	<property>
  		<name>hive.metastore.uris</name>
  		<value>hadoop-1:9083</value>	
	</property>
```

#### 启动hive 

 bin/hive --service metastore &

$ bin/hive --service hiveserver2 

尖叫提示：如果设置了uris，在今后使用Hive时，那么必须启动如上两个命令，否则Hive无法正常启动。

配置hue.ini

### hue mysql

## [[[mysql]]]
```ini
  # Name to show in the UI.
 nice_name="My SQL DB"

  # For MySQL and PostgreSQL, name is the name of the database.
  # For Oracle, Name is instance of the Oracle server. For express edition
  # this is 'xe' by default.
  ## name=mysqldb

  # Database backend to use. This can be:
  # 1. mysql
  # 2. postgresql
  # 3. oracle
  engine=mysql

  # IP or hostname of the database to connect to.
  host=localhost

  # Port the database server is listening to. Defaults are:
  # 1. MySQL: 3306
  # 2. PostgreSQL: 5432
  # 3. Oracle Express Edition: 1521
  port=3306

  # Username to authenticate with when connecting to the database.
  user=example

  # Password matching the username to authenticate with when
  # connecting to the database.
  password=123456
```
### hue  oozie





### hue hbase

```ini
   hbase_clusters=(Cluster|hbase-1:9090)

  # HBase configuration directory, where hbase-site.xml is located.
  hbase_conf_dir=/configure/hbase
```

 bin/hbase-daemon.sh start thrift