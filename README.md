# zabbix-tomcat-monitoring
### Step 1 – Setup LAMP
```bash
sudo apt update
sudo apt install apache2 libapache2-mod-php
sudo apt install mysql-server
sudo apt install php php-mbstring php-gd php-xml php-bcmath php-ldap php-mysql
```
### mysql_sercure_installation
```bash
sudo mysql_secure_installation
```
### configuration for apache2
```bash
vim /etc/php/7.4/apache2/php.ini
```
```bash
memory_limit 256M
upload_max_filesize 16M
post_max_size 16M
max_execution_time 300
max_input_time 300
max_input_vars 10000
date.timezone = 'Asia/Kolkata'
```
### Step 2 – Configure Zabbix Repository
```bash
wget https://repo.zabbix.com/zabbix/5.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_5.0-1+focal_all.deb
sudo dpkg -i zabbix-release_5.0-1+focal_all.deb
```
###  Step 3 – Installing Zabbix Server
```bash
sudo apt update
sudo apt install zabbix-server-mysql zabbix-frontend-php zabbix-agent zabbix-apache-conf
```
### Step 4 – Create Zabbix Database and User
```bash
mysql -u root -p

CREATE DATABASE zabbixdb character set utf8 collate utf8_bin;
CREATE USER 'zabbix'@'localhost' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON zabbixdb.* TO 'zabbix'@'localhost' WITH GRANT OPTION;
FLUSH PRIVILEGES;
```
```bash
cd /usr/share/doc/zabbix-server-mysql
zcat create.sql.gz | mysql -u zabbix -p zabbixdb
```
### Step 5 – Update Zabbix Configuration
```bash
sudo vi /etc/zabbix/zabbix_server.conf
```
```bash
  DBHost=localhost
  DBName=zabbixdb
  DBUser=zabbix
  DBPassword=password
```
```bash
sudo systemctl enable zabbix-server
sudo systemctl restart zabbix-server
```
```bash
sudo systemctl restart apache2
```
### Step 6 – Adjust Firewall for Zabbix
```bash
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --permanent --add-port=10050/tcp
sudo firewall-cmd --permanent --add-port=10051/tcp
```
```bash
sudo firewall-cmd --reload
```
### Step 7 – Running Zabbix Web Installer
```bash
http://your_server_ip/zabbix

```
### username && psaaword
```bash
Admin
zabbix
```

### Step 8 - Install & Enable Zabbix Java Gateway
```bash
 apt-get install zabbix-java-gateway  
 /etc/init.d/zabbix-java-gateway start
```
### zabbix-java-gateway service
```bash
systemctl enable zabbix-java-gateway
systemctl start  zabbix-java-gateway
systemctl status  zabbix-java-gateway
```
### zabbix-java-gateway configuration
```bash
 vi /etc/zabbix/zabbix_server.conf
```bash
JavaGateway=IP address (or hostname) of Zabbix Java gateway.
JavaGatewayPort=10052
StartJavaPollers=5
```
### tomcat -9 servr configuration
```bash

#t9 environment variable
[Unit]
Description=Tomcat 9 servlet container
After=network.target

[Service]
Type=forking

User=tomcat
Group=tomcat

Environment="JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64"
Environment="CATALINA_BASE=/usr/share/apache-tomcat-9.0.30"
Environment="CATALINA_OPTS=-Xms512M -Xmx512M -server -XX:+UseParallelGC"
Environment="JAVA_OPTS=-Djava.awt.headless=true -Dcom.sun.management.jmxremote -Dcom.sun.management.jmxremote.port=12345 -Dcom.sun.management.jmxremote.authenticate=false -Dcom.sun.management.jmxremote.ssl=false -Djava.rmi.server.hostname=192.168.100.1"

ExecStart=/usr/share/apache-tomcat-9.0.30/bin/startup.sh
ExecStop=/usr/share/apache-tomcat-9.0.30/bin/shutdown.sh

[Install]

```
