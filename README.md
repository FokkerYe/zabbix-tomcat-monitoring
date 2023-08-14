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
