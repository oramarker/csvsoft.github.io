

Etcher flash OS



### Enable Remote SSH

```
sudo systemctl enable ssh
sudo systemctl start ssh
```



### Upgrade system

```
sudo apt update
sudo apt upgrade -y
```



### Install Python pip

```sheel
sudo apt-get update
sudo apt-get install python-pip
```



### Install Open-JDK-8

```bash
sudo apt-get install openjdk-8-jdk
```



### Install Open Media Vault

```
wget -O - https://raw.githubusercontent.com/OpenMediaVault-Plugin-Developers/installScript/master/install | sudo bash
```





### USB monitoring

Added udev rules

sudo vi /etc/udev/rules.d/add_usb.rules

```shell
ACTION=="bind", SUBSYSTEM=="usb",ENV{DRIVER}=="usb-storage",RUN=="/bin/su pi --command='/home/pi/sleepyfiles/add_usb.py'"
```



```
curl -d "sudo omv-rpc -u admin 'filesystemmgmt' 'enumerateFilesystems'" -H "Content-Type: application/json" -X POST http://localhost:8080/executeCmd

curl -d "sudo omv-rpc -u admin filesystemmgmt enumerateFilesystems" -H "Content-Type: application/json" -X POST http://localhost:8080/executeCmd



```



### Niginx configuration

### TLS 

```shell
sudo apt-get update
sudo apt-get install software-properties-common
sudo add-apt-repository ppa:certbot/certbot
sudo apt-get update
sudo apt-get install python-certbot-nginx

# restart nginx
nginx -t && nginx -s reload
sudo systemctl restart nginx

sudo certbot --nginx -d vinsonzhang.asuscomm.com
```



sleep station reverse proxy

```
sudo vi 	/etc/nginx/sites-enabled/reverse-proxy.conf
```



Install usbdisk control

```
sudo apt-get install udisks2
#power off usb device
sudo udisksctl power-off -b /dev/sdc
#spin down a usb drive
sudo hdparm -y /dev/sdc
```





Default users and groups

user:sleepyadmin

group:sleepyusers

```shell
groupadd sleepyusers
# add user to a new group
usermod -a -G <group> <user>
```



hfsplus (Mac filesystem)

```shell
sudo apt-get install hfsprogs

sudo fsck.hfsplus /dev/sdXY 
```





LibHeif

```
sudo apt install libheif-examples
```



exiftool

```shell
sudo apt install exiftool
```





```shell
ffmpeg -i ./video/202103/raw/81A2553B-4947-4769-A42F-E6FF31E01527_L0_001_1440_1920.MOV -ss 00:00:03.435 -frames:v 1 A6081.png

```





### System D

### check journal last lines

```shell
journalctl --unit=my.service -n 100 --no-pager
journalctl --unit=everpx -n 100 -f
```





### PiShrink

https://www.tomshardware.com/how-to/back-up-raspberry-pi-as-disk-image

```shell
# install shrink script
wget https://raw.githubusercontent.com/Drewsif/PiShrink/master/pishrink.sh
sudo chmod +x pishrink.sh
sudo mv pishrink.sh /usr/local/bin

# copy sdc card data to usb
sudo dd if=/dev/mmcblk0 of=/srv/sandisk128G/everpxPi.img bs=1M

# shrink the image
cd /srv/sandisk128G
sudo pishrink.sh -z everpxPi.img
```



### acme and lets encrypt

```shell
sudo su
curl https://get.acme.sh | sh
#reopen a new terminal
#Register an email account
acme.sh --register-account -m vinson.zhang@gmail.com
[Mon Aug 23 18:57:12 EDT 2021] No EAB credentials found for ZeroSSL, let's get one
[Mon Aug 23 18:57:13 EDT 2021] Registering account: https://acme.zerossl.com/v2/DV90
[Mon Aug 23 18:57:14 EDT 2021] Registered
[Mon Aug 23 18:57:14 EDT 2021] ACCOUNT_THUMBPRINT='SM25svWSajnGI-m2nOUBcQFfGuXQcGP3nDtQ-lXEoHk'

# api keys
export GD_Secret=
export GD_Key=
# issue an certificate
acme.sh --issue -d "*.everpx.com" --dns dns_gd 	

#webroot mode
  acme.sh  --issue  -d *.everpx.com  --nginx /etc/nginx/sites-available/everpx.com

# install the cert
acme.sh --install-cert -d everpx.com \
--cert-file /etc/nginx/cert/everpx.com/cert.pem \
--key-file /etc/nginx/cert/everpx.com/key.pem \
--fullchain-file /etc/nginx/cert/everpx.com/fullchain.pem \
--reloadcmd "systemctl reload nginx.service"

cp /home/everpx/.acme.sh/*.everpx.com/*.everpx.com.key /etc/nginx/cert/everpx.com/key.pem
cp /home/everpx/.acme.sh/*.everpx.com/*.everpx.com.cer /etc/nginx/cert/everpx.com/cert.pem
cp /home/everpx/.acme.sh/*.everpx.com/fullchain.cer /etc/nginx/cert/everpx.com/fullchain.pem


    listen 8443 ssl http2;
    listen [::]:8443 ssl http2;
    ssl on;
    ssl_certificate /etc/nginx/cert/everpx.com/fullchain.pem;
    ssl_certificate_key     /etc/nginx/cert/everpx.com/key.pem;
    ssl_trusted_certificate /etc/nginx/cert/everpx.com/cert.pem;


```



### Install a Fresh New system

0. Upgrade system
```shell
 sudo apt-get update
 sudo apt-get upgrade
```

1. install JDK and Nginx

   ```shell
   # JDK 11
   sudo apt install openjdk-11-jre-headless
   sudo apt-get  install nginx
   
   # list application firewall profiles
   sudo ufw app list
   
   # enable nginx https only
   sudo ufw allow 'Nginx HTTPS'
   ```

2. Install hfs-dogs to support macos file system

   ```shell
     sudo apt-get install hfsprogs
   ```
3. Install ImageMagic with HEIC support

  ```shell
sudo sed -Ei 's/^# deb-src /deb-src /' /etc/apt/sources.list
sudo apt-get update
sudo apt-get install build-essential autoconf libtool git-core
sudo apt-get build-dep imagemagick libmagickcore-dev libde265 libheif

#libde265
cd /usr/src/ 
sudo git clone https://github.com/strukturag/libde265.git  
#sudo git clone https://github.com/strukturag/libheif.git 
cd libde265/ 
sudo ./autogen.sh 
sudo ./configure 
sudo make  
sudo make install 

# heiflib
wget https://github.com/strukturag/libheif/archive/v1.3.2.tar.gz
sudo wget https://github.com/strukturag/libheif/archive/v1.3.2.tar.gz
sudo tar xzvf v1.3.2.tar.gz 
cd libheif-1.3.2/
cd /usr/src/libheif/ 
sudo ./autogen.sh 
sudo ./configure 
sudo make  
sudo make install

#imageMagick
cd /usr/src/ 
sudo wget https://www.imagemagick.org/download/ImageMagick.tar.gz 
sudo tar xf ImageMagick.tar.gz 
cd ImageMagick-7*
sudo ./configure --with-heic=yes 
sudo make  
sudo make install  
sudo ldconfig
  ```
4. Create everpx service

 4.1 users and directories

```shell
# Add user everpx and set password
sudo useradd everpx
sudo passwd everpx
sudo usermod -d /home/everpx -m everpx
sudo mkdir /home/everpx
sudo chown -R everpx /home/everpx
sudo usermod --shell /bin/bash everpx

# Create group everpx
sudo groupadd everpx
sudo groupadd sleepyusers
# Add user to everpx
sudo usermod -a -G sudo everpx
sudo usermod -a -G everpx everpx
sudo usermod -a -G sleepyusers everpx


# Create deployment directories
sudo mkdir -p /opt/everpx/db
sudo mkdir -p /opt/everpx/logs
sudo mkdir -p /opt/everpx/env
sudo mkdir -p /opt/everpx/work
sudo mkdir -p /opt/everpx/lib
sudo mkdir -p /opt/everpx/work/sleepyfiles/servletupload
sudo mkdir -p /opt/everpx/work/sleepyfiles/uploadtmp

sudo chown -R everpx /opt/everpx
sudo chgrp -R everpx /opt/everpx

# mount point for usb disks
sudo mkdir /srv
sudo chown everpx /srv

```



 4.2 Variables

```shell
# create everpx service
nano /opt/everpx/env/everpx.env
APP_ROOT=/opt/everpx
BINARY="/opt/everpx/lib/sleepstation.jar"
PORT="8080"
USER="everpx"
JAVA_OPTS="-Xmx2G -XX:MaxDirectMemorySize=1G"
APP_CONFIG="-Dspring.profiles.active=prod  -DSERVER_SERVLET_CONTEXT_PATH=/sleepstation"
LOGGING="-DSLEEP_STATSION_LOGS_DIR=/opt/everpx/logs -DSLEEP_STATSION_LOG_LEVEL=info"


```



4.3 Service configurations

```shell
nano /etc/systemd/system/everpx.service
# /etc/systemd/system/everpx.service

[Unit]
Description=everpx station 
After=syslog.target

[Service]
EnvironmentFile=/opt/everpx/env/everpx.env
WorkingDirectory=/opt/everpx/work
User=everpx
ExecStart=/usr/bin/java $JAVA_OPTS $APP_CONFIG $LOGGING  -jar $BINARY
#https://www.freedesktop.org/software/systemd/man/systemd.exec.html
StandardOutput=null
StandardError=null
SyslogIdentifier=everpx
SuccessExitStatus=143   
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
```



4.3 Copy jar

4.4 Create a soft link

```shell
ln -s sleepstation-0.0.2-SNAPSHOT.jar  sleepstation.jar
```

4.5 Enable the service

```shell
sudo systemctl enable everpx.service
sudo systemctl status everpx.service
```

4.6 Add everpx sudoers
```shell
# group memembers are able to execute commands defined in the DISK alias without password on all hosts
visudo /etc/sudoers.d/everpx_sudoers
Cmnd_Alias    DISK=/usr/bin/mount,/usr/bin/umount
%everpx  ALL=NOPASSWD:DISK
```
4.7  Start the service

```shell
sudo systemctl  start everpxl
```




