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
Cloned my git: `git clone https://github.com/DOlearczyk/catalog.git project`  
Moved all project files to catalog directory `cp -r project/* /var/www/catalog/catalog`  
Deleted previous directory `rm -r project`  
Made git directory web inaccessible by creating file .htaccess and pasting `RedirectMatch 404 /\.git` line:
```
cd /var/www/catalog/
sudo nano .htaccess
RedirectMatch 404 /\.git
```
Install project requirements using virtualenv with neccessary permissions. Also install PostgreSQL adapter psycopg since initial project used sqlite:
```
cd /var/www/catalog/catalog/
sudo apt-get install python-pip
sudo pip install virtualenv
sudo vitualenv venv
sudo chmod -R 777 venv
source venv/bin/activate
pip install -r requirements.txt
pip install python-psycopg2
deactivate
```
Renamed `project.py` to `__init__.py`, inserted Google and Facebook client secrets, changed engine used from SQLite to PostgreSQL and renamed user table to users since user is reserved word in PostgreSQL.

Configured and enabled a new virtual host by creating config file `sudo nano /etc/apache2/sites-available/catalog.conf` and pasted in following code:
```
<VirtualHost *:80>
      ServerName 52.38.113.174
      ServerAdmin admin@52.38.113.174
      WSGIScriptAlias / /var/www/catalog/catalog.wsgi
      <Directory /var/www/catalog/catalog/>
          Order allow,deny
          Allow from all
      </Directory>
      Alias /static /var/www/catalog/catalog/static
      <Directory /var/www/catalog/catalog/static/>
          Order allow,deny
          Allow from all
      </Directory>
      ErrorLog ${APACHE_LOG_DIR}/error.log
      LogLevel warn
      CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
Enabled the virtual host: `sudo a2ensite catalog`
Moved to directory `cd /var/www/catalog`, created file `sudo nano catalog.wsgi`and pasted in following code:
```
#!/usr/bin/python
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/catalog/")

from catalog import app as application
application.secret_key = 'Add your secret key
```
Restarted apache: `sudo service apache2 restart`

##
Installed PostgreSQL: `sudo apt-get install postgresql postgresql-contrib`  
Remote connections aren't allowed on default which I can see in `/etc/postgresql/9.3/main/pg_hba.conf` file  
Created linux user catalog: `sudo adduser catalog`  
Changed to postgres user: `sudo su postgres`  
Connected to database system: `psql`  
Created catalog user: `CREATE USER catalog WITH PASSWORD 'db-password';`  
Allowed him to create tables: `ALTER USER catalog CREATEDB;`  
Created catalog database with catalog user as owner: `CREATE DATABASE catalog WITH OWNER catalog;`  
Connected to catalog db: `\c catalog`  
Revoked all rights to db: `REVOKE ALL ON SCHEMA public FROM public;`  
Grant access to catalog dbv to catalog user: `GRANT ALL ON SCHEMA public TO catalog;`  
Exit PostgreSQL `\q` and switch to previous user `exit`  
In project directory `/var/www/catalog/catalog/` I run `python database_setup.py` to create db schema and insert initial records.  
Restarted apache: `sudo service apache2 restart`













