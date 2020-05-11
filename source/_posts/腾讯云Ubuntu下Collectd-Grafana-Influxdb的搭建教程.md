---
title: 腾讯云Ubuntu下Collectd+Grafana+Influxdb的搭建教程
date: 2019-11-23 16:13:53
tags: [grafana, collectd, influxdb]
categories: Collectd
---
本文介绍了腾讯云服务器Ubuntu下监控平台的搭建教程，主要组件分为三个部分，grafana、collectd、influxdb。grafana主要用来作为监控的前端展示，collectd负责采集服务器性能指标，influxdb则是一款开源的时序数据库，负责采集数据的存储
<!-- more -->
## 一、安装Collectd
### 1、安装
`sudo apt install collectd collectd-utils`
### 2、修改collectd.conf
开启常用插件
```shell
LoadPlugin cpu
LoadPlugin df
LoadPlugin disk
LoadPlugin interface
LoadPlugin irq
LoadPlugin load
LoadPlugin memory
LoadPlugin mysql
LoadPlugin network
LoadPlugin processes
LoadPlugin rrdtool
 
<Plugin cpu>
	ReportByCpu true
	ReportByState true
	ValuesPercentage false
</Plugin>
 
<Plugin interface>
	Interface "eth0"
	IgnoreSelected false
#	ReportInactive true
#	UniqueName false
</Plugin>
 
 
<Plugin network>
#	# client setup:
Server "127.0.0.1" "25826"
#	<Server "239.192.74.66" "25826">
#		SecurityLevel Encrypt
#		Username "user"
#		Password "secret"
#		Interface "eth0"
#		ResolveInterval 14400
#	</Server>
#	TimeToLive 128
#
#	# server setup:
#	Listen "ff18::efc0:4a42" "25826"
#	<Listen "239.192.74.66" "25826">
#		SecurityLevel Sign
#		AuthFile "/etc/collectd/passwd"
#		Interface "eth0"
#	</Listen>
#	MaxPacketSize 1452
#
#	# proxy setup (client and server as above):
#	Forward true
#
#	# statistics about the network plugin itself
#	ReportStats false
#
#	# "garbage collection"
#	CacheFlush 1800
</Plugin>
```
### 3、常用命令
```
sudo /etc/init.d/collectd start
sudo /etc/init.d/collectd restart
```
##二、 安装Influxdb
###1、安装
```
sudo apt install influxdb
sudo apt install influxdb-client
```
###2、修改influxdb.conf
```
###
### [collectd]
###
### Controls one or many listeners for collectd data.
###
 
  [[collectd]]
  enabled = true
  bind-address = "127.0.0.1:25826"
  database = "collectdb"
  # retention-policy = ""
  typesdb = "/usr/share/collectd/types.db"
 
  # These next lines control how batching works. You should have this enabled
  # otherwise you could get dropped metrics or poor performance. Batching
  # will buffer points in memory if you have many coming in.
 
  # Flush if this many points get buffered
  batch-size = 5000
 
  # Number of batches that may be pending in memory
  # batch-pending = 10
 
  # Flush at least this often even if we haven't hit buffer limit
  batch-timeout = "10s"
 
  # UDP Read buffer size, 0 means OS default. UDP listener will fail if set above OS max.
  read-buffer = 0
```
###3、常用命令
```
sudo service influxdb status
 
sudo service influxdb start
```
###4、新建数据库
```
create database collectdb
```
###5、查看端口是否监听成功
```
netstat -anp| grep 25826
```
###6、查看数据
```
root@VM-0-16-ubuntu:~# influx
Visit https://enterprise.influxdata.com to register for updates, InfluxDB server management, and monitoring.
Connected to http://localhost:8086 version 1.1.1
InfluxDB shell version: 1.1.1
> use collectdb
Using database collectdb
> show field keys;
name: cpu_value
fieldKey	fieldType
--------	---------
value		float
 
name: df_value
fieldKey	fieldType
--------	---------
value		float
 
name: disk_io_time
fieldKey	fieldType
--------	---------
value		float
 
name: disk_read
fieldKey	fieldType
--------	---------
value		float
 
name: disk_value
fieldKey	fieldType
--------	---------
value		float
 
name: disk_weighted_io_time
fieldKey	fieldType
--------	---------
value		float
 
name: disk_write
fieldKey	fieldType
--------	---------
value		float
 
name: entropy_value
fieldKey	fieldType
--------	---------
value		float
 
name: interface_rx
fieldKey	fieldType
--------	---------
value		float
 
name: interface_tx
fieldKey	fieldType
--------	---------
value		float
 
name: irq_value
fieldKey	fieldType
--------	---------
value		float
 
name: load_longterm
fieldKey	fieldType
--------	---------
value		float
 
name: load_midterm
fieldKey	fieldType
--------	---------
value		float
 
name: load_shortterm
fieldKey	fieldType
--------	---------
value		float
 
name: memory_value
fieldKey	fieldType
--------	---------
value		float
 
name: processes_value
fieldKey	fieldType
--------	---------
value		float
 
name: swap_value
fieldKey	fieldType
--------	---------
value		float
 
name: users_value
fieldKey	fieldType
--------	---------
value		float
 
> 
```
### 7、通过sql显示15条cpu_value的信息
```
> select * from cpu_value limit 15;
name: cpu_value
time			host			instance	type	type_instance	value
----			----			--------	----	-------------	-----
1573975276185910000	localhost.localdomain	0		cpu	user	3429
1573975276185917000	localhost.localdomain	0		cpu	system	2646
1573975276185920000	localhost.localdomain	0		cpu	wait	4424
1573975276185936000	localhost.localdomain	0		cpu	nice	149
1573975276185939000	localhost.localdomain	0		cpu	interrupt	0
1573975276185942000	localhost.localdomain	0		cpu	softirq17
1573975276185945000	localhost.localdomain	0		cpu	steal	0
1573975276185947000	localhost.localdomain	0		cpu	idle	122528
1573975286185912000	localhost.localdomain	0		cpu	user	3443
1573975286185919000	localhost.localdomain	0		cpu	system	2658
1573975286185923000	localhost.localdomain	0		cpu	wait	4428
1573975286185927000	localhost.localdomain	0		cpu	nice	149
1573975286185931000	localhost.localdomain	0		cpu	interrupt	0
1573975286185935000	localhost.localdomain	0		cpu	softirq17
1573975286185937000	localhost.localdomain	0		cpu	steal	0
 
> 
```
##三、安装Grafana
###1、安装grafana
```
wget https://dl.grafana.com/oss/release/grafana_6.4.4_amd64.deb
sudo dpkg -i grafana_6.4.4_amd64.deb
```
###2、启动
```
sudo service grafana-server start
```
###3、配置grafana
`访问http://127.0.0.1:3000，如果是腾讯云就是你的ip:3000`
