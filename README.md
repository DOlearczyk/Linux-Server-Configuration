# Linux-Server-Configuration
Fifth project of Udacity's Full Stack Web Developer Nanodegree

Server IP Address: 52.38.113.174
SSH Port: 2200
URL: http://52.38.113.174/

# Configuration changes

Added user grader: `adduser grader`
Added user grader to sudoers: `sudo usermod -a -G sudo grader`

Updated package indexes: `sudo apt-get update`
Upgraded installed packages: `sudo apt-get upgrade`

Allow logging as grader by authorized_keys:
```
su - grader
mkdir .ssh
chmod 700 .ssh
sudo cp /root/.ssh/authorized_keys .ssh/authorized_keys
sudo chown grader:grader .ssh/authorized_keys 
chmod 644 .ssh/authorized_keys
```

Fixed warning `sudo: unable to resolve host ip-10-20-47-177` by adding line `127.0.0.1 ip-10-20-44-28` to file `/etc/hosts`

Disabled logging in via root by changing line `PermitRootLogin without-password` to `PermitRootLogin no` in file `/etc/ssh/sshd_config`. Also in the same file changed line `Port 22` to `Port 2200`

Turned Uncomplicated Firewall (UFW) on with default rules: `sudo ufw enable`
Allowed connections for SSH(port 2200), HTTP(port 80) and NTP(port 123):
```
sudo ufw allow 2200/tcp
sudo ufw allow 80/tcp
sudo ufw allow 123/udp
```

Changed timezone to UTC using command: `sudo dpkg-reconfigure tzdata`

Installed Apache web server: `sudo apt-get install apache2`
Installed mod_wsgi for serving Python apps from Apache, helper package python-setuptools and python-dev package: 
```
sudo apt-get install python-setuptools
sudo apt-get install libapache2-mod-wsgi
sudo apt-get install python-dev
```
Fixed warning `Could not reliably determine the servers's fully qualified domain name` by creating new config file and enabling it:
```
echo "ServerName HOSTNAME" | sudo tee /etc/apache2/conf-available/fqdn.conf
sudo a2enconf fqdn
```

Installed git: `sudo apt-get install git`
Enabled mod_wsgi: `sudo a2enmod wsgi`
Setup directories for catalog project on `/var/www` directory:
```
sudo mkdir catalog
cd catalog
sudo mkdir catalog
cd catalog
sudo mkdir static templates
```
Cloned my git % TODO Finish









