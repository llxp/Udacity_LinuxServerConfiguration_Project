# Linux Server Configuration Project

Host: [udacity-postgresql-test.northeurope.cloudapp.azure.com](http://udacity-postgresql-test.northeurope.cloudapp.azure.com)
### Access to the server: ###
The server is powered off by default to reduce cost.<br/>
To start the server simply go to<br/>
[https://udacity-vm-manager.azurewebsites.net/](https://udacity-vm-manager.azurewebsites.net/)<br/>
and enter the private ssh key into the textarea.<br/>
When the Status is "deallocated" press on the button "Start".<br/>
The machine needs about 2 minutes to be fully powered on.<br/>
When the access on the server is over, simply press on "Stop" again to poweroff the server.<br/>

#### Image: Ubuntu 18.04.1 LTS (amd64) ####
#### packages installed: ####
- apache2
- python3
- python3-pip
- libapache2-mod-wsgi-py3
- postgresql

#### Changes ####
The following files were changed:
- /etc/apache2/sites-available/flask.conf
```
<VirtualHost *>
    ServerName udacity-postgresql-test.northeurope.cloudapp.azure.com

    WSGIDaemonProcess myapp user=www-data group=www-data threads=5
    WSGIScriptAlias / /var/www/udacity_fsnd_itemcatalog_backend/myapp.wsgi

    <Directory /var/www/udacity_fsnd_itemcatalog_backend>
        WSGIProcessGroup myapp
        WSGIApplicationGroup %{GLOBAL}
        Order deny,allow
        Allow from all
    </Directory>
</VirtualHost>
```
- /etc/postgresql/10/main/pg_hba.conf (the important part of the file)
```
# DO NOT DISABLE!
# If you change this first entry you will need to make sure that the
# database superuser can access the database using some other method.
# Noninteractive access to all databases is required during automatic
# maintenance (custom daily cronjobs, replication, and similar tasks).
#
# Database administrative login by Unix domain socket
local   all             postgres                                peer

# TYPE  DATABASE        USER            ADDRESS                 METHOD

# "local" is for Unix domain socket connections only
local   all             all                                     peer
# IPv4 local connections:
host    all             all             127.0.0.1/32            md5
# IPv6 local connections:
host    all             all             ::1/128                 md5
# Allow replication connections from localhost, by a user with the
# replication privilege.
#local   replication     all                                     peer
#host    replication     all             127.0.0.1/32            md5
#host    replication     all             ::1/128                 md5
```
- /var/www/udacity_fsnd_itemcatalog_backend/myapp.wsgi
```
import sys
sys.path.insert(0, '/var/www/udacity_fsnd_itemcatalog_backend')
sys.stdout = sys.stderr

from app import app as application
```
- /etc/ssh/sshd_config (only changes made are listed here)
```
Port 2200
ListenAddress 0.0.0.0
ListenAddress ::
LoginGraceTime 30
MaxAuthTries 6
PubkeyAuthentication yes
AuthorizedKeysFile      %h/.ssh/authorized_keys
PasswordAuthentication no

```
This project was completed using the learning resources found on the following pages:
- [udacity](https://udacity.com)
- [stackoverflow.com](https://stackoverflow.com)
- [askubuntu.com](https://askubuntu.com)
- [https://jsephler.co.uk/session-issue-with-flask-deployment-apache2-wgsi-flask/](https://jsephler.co.uk/session-issue-with-flask-deployment-apache2-wgsi-flask/)
- [https://security.stackexchange.com/questions/143442/what-are-ssh-keygen-best-practices](https://security.stackexchange.com/questions/143442/what-are-ssh-keygen-best-practices)