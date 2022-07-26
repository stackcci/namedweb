namedmanager


#1. บทนำ NamedManager เป็นระบบการจัดการ DNS บนเว็บที่สามารถใช้เพื่อเพิ่ม ปรับ และลบโซน DNS/บันทึกข้อมูล รองรับ Bind เป็นบริการ DNS แบ็กเอนด์ และรองรับ IPv4 และ IPv6

#2. วัตถุประสงค์ จุดประสงค์ที่ง่ายและตรงไปตรงมาที่สุดคือการจัดการระเบียน DNS สะดวกในการเพิ่มและลบ

![Alt text](img/first.png "前台管理截图")

####3.安装和使用

**本文是针对centos 6 系统为范本**

下载包并安装。rpm包在rpm目录

#####安装namedmanager-www 管理界面

**安装如下：**

	rpm -Uvh namedmanager-www-1.5.1-1.el6.noarch.rpm
	[root@localhost noarch]# rpm -ihv namedmanager-www-1.5.1-1.el6.noarch.rpm 
	Preparing...                ########################################### [100%]
	   1:namedmanager-www       ########################################### [100%]
	   Reloading httpd...
	   Reloading httpd: 
	   Run cd /usr/share/namedmanager/resources/; ./autoinstall.pl to install the SQL database.

	cd /usr/share/namedmanager/resources/

	./autoinstall.pl

**输入mysql密码完成Sql 导入和配置文件生成。截图如下：**

![Alt text](img/importsql.png "导入sql语名")

####数据库部分参考:（已经安装并熟知的可以跳过）
    
**安装数据库**

	yum -y install mysql-server

**启动Mysql**

	service mysqld start

**设置mysql密码**

	mysqladmin -u root password '123456

**namedmanager配置文件如下：**

	/etc/namedmanager/config.php

	<?php
	$config["db_host"] = "localhost";                       // hostname of the MySQL server
	$config["db_name"] = "namedmanager";                            // database name
	$config["db_user"] = "namedmanager";                            // MySQL user
	$config["db_pass"] = "iv262niram6";                             // MySQL password (if any)
	$config["AUTH_METHOD"] = "sql";
	?>

**导入完成后可web管理:**

	https://xx.xx.xx.xx/namedmanager/index.php

	默认用户名和密码分别为: setup  和setup123

**接着设置API key 备用：**

![Alt text](img/api.png "api设置")


#####安装named-bind

**安装bind**
	
	yum -y install bind

**安装named-bind**

	[root@localhost noarch]# rpm -ivh namedmanager-bind-1.5.1-1.el6.noarch.rpm 
	Preparing...                ########################################### [100%]
	   1:namedmanager-bind      ########################################### [100%]

	BIND/NAMED CONFIGURATION

	NamedManager BIND components have been installed, you will need to install
	and configure bind/named to use the configuration file by adding the
	following to /etc/named.conf:

**配置bind**

在/etc/named.conf中添加如下：
	
	include "/etc/named.namedmanager.conf";

**重启bind**

	service named restart

截图:

![Alt text](img/named_bind.png "named-bind安装")	

**接下来修改bind集成配置**

/etc/namedmanager/config-bind.php内容如下:
	
	<?php
	$config["api_url"]              = "https://58.215.133.102/namedmanager";               // Application Install Location
	$config["api_server_name"]      = "58.215.133.102";     // the authentication process)
	$config["api_auth_key"]         = "verycloud";                                 // API authentication key
	$config["log_file"]             = "/var/log/messages";
	$config["lock_file"]            = "/var/lock/namedmanager_lock";
	$config["bind"]["version"]              = "9";  // version of bind 
	$config["bind"]["reload"]               = "/usr/sbin/rndc reload";           // command to reload bind config & zonefiles
	$config["bind"]["config"]               = "/etc/named.namedmanager.conf";   // configuration file to write bind config too
	$config["bind"]["zonefiledir"]          = "/var/named/";                        // directory to write zonefiles too
	$config["bind"]["verify_zone"]          = "/usr/sbin/named-checkzone";          // Used to verify each gen
	$config["bind"]["verify_config"]        = "/usr/sbin/named-checkconf";          // Used to verify generatetion
	?> 



####参考文档

官方安装手册

<https://projects.jethrocarr.com/p/oss-namedmanager/page/Installation-RPM/>

<http:/www.selfcai.com.cn>
