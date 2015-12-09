# zabbix

##zabbixサーバーインストール&設定

####EPELリポジトリを有効にする

```
# cd /usr/local/src
# rpm -ivh http://ftp.riken.jp/Linux/fedora/epel/6/x86_64/epel-release-6-8.noarch.rpm
```

#### zabbixサーバーインストール
```
yum --enablerepo=epel install zabbix-server zabbix-server-mysql
```

####DB作成

```
# mysql -u root -p
mysql> CREATE USER zabbix;
mysql> CREATE DATABASE zabbix;
mysql> GRANT ALL PRIVILEGES ON zabbix.* TO zabbix@localhost IDENTIFIED BY 'zabbix';
mysql> FLUSH PRIVILEGES;
```

####スキーマ作成

```
$ mysql -u zabbix -pzabbix zabbix < /usr/share/doc/zabbix-server-mysql-1.8.22/create/schema/mysql.sql
$ mysql -u zabbix -pzabbix zabbix < /usr/share/doc/zabbix-server-mysql-1.8.22/create/data/data.sql
$ mysql -u zabbix -pzabbix zabbix < /usr/share/doc/zabbix-server-mysql-1.8.22/create/data/images_mysql.sql
```

####zabbixサーバー設定ファイルの更新

```
# vi /etc/zabbix/zabbix_server.conf
```

```
DBName=zabbix
DBUser=zabbix
DBPassword=zabbix
```

####zabbixサーバー起動
```
service zabbix-server start
chkconfig zabbix-server on
```
***

##Webフロントエンドのインストール

```
# yum --enablerepo=epel install zabbix-web-mysql
```

####PHP設定ファイルを更新(必須)
```
date.timezone = Asia/Tokyo
post_max_size = 32M
max_execution_time = 600
max_input_time = 600
memory_limit = 256M
upload_max_filesize = 16M
```

#### Apache再起動
```
# service httpd restart
```

####Webブラウザで以下URLにアクセス
1. http://[ホスト]/zabbix/
2. ウィザードに従ってすすめる
3. ログイン画面に遷移したらadmin / zabbixでログイン
4. パスワード変更
5. 日本語へ変更
6. 監視対象のホストを追加

***

##Zabbixエージェントのインストール
(監視したいサーバーにエージェントをインストールするが、ここではZabbixサーバーと同一サーバとする)

####インストール
```
# yum --enablerepo=epel install zabbix-agent
```

####Zabbixエージェントの設定ファイル
```
/etc/zabbix/zabbix_agentd.conf
```

```
# vi  zabbix_agentd.conf
```

```
Server：ZabbixサーバーのIPアドレス
Hostname：監視対象ホスト、つまり自ホストのホスト名
ListenIP：Zabbixエージェントが待ち受け（Listen）をするIPアドレス。ホストに複数のIPアドレスが振られている場合、Zabbixサーバーと通信できるIPアドレスをここに指定する
```

```
Server=127.0.0.1
Hostname=[ホスト名]
ListenIP=127.0.0.1
```

####Zabbixエージェントの起動
```
service zabbix-agent start
chkconfig zabbix-agent on
```
