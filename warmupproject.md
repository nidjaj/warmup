# create AWS Instance (centos7)

Attach disk of 4GB with disk accidental termination policy 
Attach one Elastic IP to your instance
Now wait for AWS  running status and logged into server using your keys

Launch Update instance
#  format disk 
* fdisk -l    
* check for disk name
* fdisk disk-name 
# LVM

* yum install lvm2
* pvcreate disk-name
* pvdisplay disk-name
---
# Volume Group create
* vgcreate mylvm /dev/xvdb
* vgdisplay mylvm
# Lvm Create
* lvcreate --name mylvm1 --size 3.9G mylvm1
# format partition

* mkfs.xfs /dev/mylvm/mylvm1

# Mounting
* Need to mount on /home2
* mkdir /home2
* mount /dev/mylvm/mylvm1 /home2
* put entry in fstab
* vi /etc/fstab
* /dev/mylvm/mylvm1 /home2  xfs  noexec  
###  check if mounting is done properly
  mount -a
## Disable Selinux
* setenforce 0
* shutdown -r now 
* sestatus
##  Set time zone to IST
* timedatectl set-timezones Asia/Kolkata 
## Install Basic Utility like netstat , wget vim , git
* yum install ctdb-tests (for netstat)
* yum install wget   (wget)
* yum install vim   (vim)
* yum install git  (git)
#  Reboot Successfully !!!!!!!
Configure motd file
* wget https://adhocnw.org/motd.txt
* vim  motd.txt
* change Disk : 8G , Free Disk : 4G , Public IP, Private IP
* write cat motd.txt in .bash_profile
* source .bash_profile
#  Apache installation 
* yum install httpd
* systemctl start httpd.service
* systemctl enable httpd.service

#  SSL

* yum install mod_ssl openssl
* systemctl restart httpd.service
* systemctl reload httpd.service

# apachectl graceful
* add port 443 https in inbound of security of your instance
# PHP
* yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
* yum install http://rpms.remirepo.net/enterprise/remi-release-7.rpm
* yum-config-manager --enable remi-php70
* yum install php php-mcrypt php-cli php-gd php-curl php-mysql php-ldap php-zip php-fileinfo
* php -v
  ##  User Setup
### change default home directory to /home2
* vi /etc/default/useradd
* set HOME=/home2
* useradd blu
* passwd -d blu # delete password
* mkdir /home2/blu/public_html
## Virtual Host Configuration
* cd /etc/httpd/conf.d
* vi  blu-php.conf 

### write this in blu.php.conf 
    <VirtualHost *:80>
    ServerName bluboy.adhocnw.com
    ServerAlias www.bluboy.adhocnw.com
    ServerAdmin webmaster@bluboy.adhocnw.com
    DocumentRoot /home2/blu/public_html
    <Directory /home2/blu/public_html>
    Options -Indexes +FollowSymLniks
    AllowOverride All
    </Directory>
    ErrorLog /home2/blu/error.log
    CustomLog /home2/blu/access.log  combined
    </VirtualHost>

### make new files
* vi  /home2/blu/error.log
* vi  /home2/blu/access.log

* apachectl configtest
 Syntax OK
* systemctl restart httpd
### changing DocumentRoot in /etc/httpd/conf/httpd.conf from /var/www/html to /home2/blu/public_html and /var/www to /home2
now http://ip/page_in_public_html will be shown
#  to run DNS

( for windows )

* open file at below path
C:/Windows/System32/driver/etc/hosts
write
elastic-ip-aws bluboy.adhocnw.com
# Update server packages
 * yum update httpd 
