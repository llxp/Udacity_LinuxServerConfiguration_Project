
# Linux Server Configuration Project

Host: [udacity-postgresql-test.northeurope.cloudapp.azure.com](http://udacity-postgresql-test.northeurope.cloudapp.azure.com)
### Access to the server: ###
The server is powered off by default to reduce some costs.<br/>
To start the server simply go to<br/>
[https://udacity-vm-manager.azurewebsites.net/](https://udacity-vm-manager.azurewebsites.net/)<br/>
and enter the private ssh key into the textarea.<br/>
When the Status is "deallocated" press on the button "Start".<br/>
The machine needs about 2 minutes to be fully powered on.<br/>
When the server is powered on, you can access the server using ssh on port 2200 using the provided certificate.

On Linux/Mac:<br/>
Copy the private key to ```~\.ssh\id_ed25519``` </br> (if you are using a different location, change that in the ssh command:
> ```ssh grader@udacity-postgresql-test.northeurope.cloudapp.azure.com -p 2200 -i ~\.ssh\id_ed25519```

On Windows:<br/>
Use the same command as in Linux/Mac in powershell or use putty:
Host Name (or IP address): grader@udacity-postgresql-test.northeurope.cloudapp.azure.com
> Port: 2200
Connection -> SSH -> Auth:
Private key file for authentication: <br/>file with private key converted to ppk (putty file format for private keys)
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
To activate the flask apache2 configuration I used the following command:
```sudo a2ensite flask```
and
```sudo service apache2 restart```
to restart the apache2 service
To deploy the Item Catalog Project I used the following command:
```sudo git clone https://github.com/llxp/udacity_fsnd_itemcatalog_backend /var/www/```
and then created the file ```myapp.wsgi``` with the content from above.
In the end I needed to install the dependencies of the flask app:
```
sudo mkdir /var/www/.cache/
sudo mkdir /var/www/.local/
chown -R root:www-data /var/www/.cache/
chown -R root:www-data /var/www/.local/
sudo pip install flask-bootstrap
sudo pip install psycopg2
sudo -H www-data pip install psycopg2-binary
sudo -H -u www-data pip install flask-bootstrap
sudo pip install -r /var/www/udacity_fsnd_itemcatalog_backend/requirements.txt
sudo -H -u www-data pip3 install -r /var/www/udacity_fsnd_itemcatalog_backend/requirements.txt
```

## Security ##
The directory /var/www/udacity_fsnd_itemcatalog_backend was set to the owner ```root``` and the group ```www-data``` and permissions where set to:

*.py: 550<br/>
plain text files: 700<br/>
itemcatalog.db: 760<br/>
client_secrets.json: 740<br/>

The *.py and the myapp.wsgi files where set to owner ```www-data``` and group ```www-data```
The plain text files where set to owner ```root``` and group ```root```
The directories ```static``` and ```templates``` where set to owner ```www-data``` and group ```www-data```

#
This project was completed using the learning resources found on the following pages:
- [udacity](https://udacity.com)
- [stackoverflow.com](https://stackoverflow.com)
- [askubuntu.com](https://askubuntu.com)
- [https://jsephler.co.uk/session-issue-with-flask-deployment-apache2-wgsi-flask/](https://jsephler.co.uk/session-issue-with-flask-deployment-apache2-wgsi-flask/)
- [https://security.stackexchange.com/questions/143442/what-are-ssh-keygen-best-practices](https://security.stackexchange.com/questions/143442/what-are-ssh-keygen-best-practices)