# linux-server-configuration
Udacity FSND project

Get your server.
1. Start a new Ubuntu Linux server instance on Amazon Lightsail. There are full details on setting up your Lightsail instance on the next page.
2. Follow the instructions provided to SSH into your server.

Public IP
34.214.197.23
52.33.99.231

User name
ubuntu

SSH into your server
Download default private key from AWS 
Do you want to add the certificate from the life "LightsaleDefaultPrivateKey-us-west-2.pem" to a keychain?
keychain login

- an error occured.  unable to import the item.
```
1.  mv ~/Downloads/LightsailDefaultPrivateKey-us-west-2.pem ~/.ssh/
2.  chmod 600 ~/.ssh/LightsailDefaultPrivateKey-us-west-2.pem
3.  ssh -i ~/.ssh/LightsailDefaultPrivateKey-us-west-2.pem ubuntu@52.33.99.231
```


private server:
ubuntu@ip-172-26-13-140

1.  Give grader access.
In order for your project to be reviewed, the grader needs to be able to log in to your server.

   1. Create a new user account named grader.
``` sudo adduser grader ```
   2. To check the User "grader" information:
   ```sudo apt-get install finger```
   ```finger grader```
     (provides additional information(login , directory, name, shell, etc) of the user named Udacity)
     

2. Give grader the permission to sudo.
    ```1.  sudo visudo```
    (edit the sudoers file . To save, use sudo visudo to edit the sudoers file otherwise file will not be saved)
    2.  add the below line of code after root ALL=(ALL:ALL) ALL 
    grader ALL=(ALL:ALL) ALL and save it (ctrl-X , then Y and Enter) Note: tab space between grader and ALL.
    Your new user(grader) is able to execute commands with administrative privileges. ( for example - sudo anycommand)
    3.  You can check the grader entry by entering the command: sudo cat /etc/sudoers
    4.  Reload SSH to apply new settings
          ```$ reload ssh```
        Note: use command if needed
        ```sudo apt get upstart``` 

    
3. Create an SSH key pair for grader using the ssh-keygen tool on your local machine in a folder called Udacity.
    1.  generate key-pair
        ``` ssh-keygen``` 

    2.  Save keygen file into (/home/ubuntu/.ssh/udacity_key_1.rsa) and fill in the password. public key    (udacity_key_1.rsa.pub) and identification key(udacity_key_1.rsa) will be generated.
  

    3.  Login into the grader account using on your virtual machine:
        i. to switch to user "grader", 
        ``` $ su - grader``` 
        enter password
        on successful login, you will see 
        grader@ip-172-26-13-140
        In the grader account:
        ii.  create .ssh folder in /home/grader
        ``` $ mkdir /home/grader/.ssh``` 
        iii.  create an empty authorized_keys file
        ``` $ touch /home/grader/.ssh/authorized_keys``` 
        iv.  paste the public keys into authroized_keys from udacity_key_1.rsa.pub
        ``` $ nano /home/grader/.ssh/authorized_keys``` 
        
        copy and paste identification key from local machine .ssh/udacity_key_1.rsa.pub file
        to grader's .ssh/authorized_keys file and save it.
        
        Next, set file permissions using the following commands:
        ``` $ chmod 700 .ssh``` 
        ``` $ chmod 644 .ssh/authorized_keys``` 
        
        
        Next, login to grader from another terminal by entering the following commands:
        
        ``` $ ssh -i ~/.ssh/udacity_key_1.rsa grader@52.33.99.231``` 
        (Note: Enter password if one was created.
        On successful login, you will see:  grader@ip-172-26-13-140:~$) 


Secure your server.
4. Update all currently installed packages.
    1. Update list of packages and their versions
    ```$ sudo apt-get update``` 
    
    2. Install the packages
    ```$ sudo apt-get upgrade``` 
       Note:  SELECT by highlighting "keep the local version currently installed" and hit ENTER


5.  Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123).
    1.  check the firewall status 
        ``` $ sudo ufw status``` 
    2.  block all incoming connections on all ports 
        ``` $ sudo ufw default deny incoming``` 
    3.  allow outgoing connections on all ports 
        ``` $ sudo ufw default allow outgoing``` 
    4.  allow ssh connection 
        ``` $ sudo ufw allow ssh``` 
    4.  allow incoming connection for SSH(port 2200) 
        ``` $ sudo ufw allow 2200/tcp``` 
    5.  allow incoming connection for HTTP(port 80) 
        ``` $ sudo ufw allow 80/tcp``` 
    6.  allow incoming connection for NTP(port 123) 
        ``` $ sudo ufw allow 123/udp``` 
    7.  check the added rules 
        ``` $ sudo ufw show added``` 
        
        should display:
        Added user rules (see 'ufw status' for running firewall):
        ufw allow 22
        ufw allow 2200/tcp
        ufw allow 80/tcp
        ufw allow 123/udp  
    8.  enable the firewall
        ```$ sudo ufw enable``` 
    9.  check whether firewall is enable or not
        ```$ sudo ufw status``` 
        
        should display:
        Status: active

To                         Action      From
--                         ------      ----
22                         ALLOW       Anywhere                  
2200/tcp                   ALLOW       Anywhere                  
80/tcp                     ALLOW       Anywhere                  
123/udp                    ALLOW       Anywhere                  
22 (v6)                    ALLOW       Anywhere (v6)             
2200/tcp (v6)              ALLOW       Anywhere (v6)             
80/tcp (v6)                ALLOW       Anywhere (v6)             
123/udp (v6)               ALLOW       Anywhere (v6)
    
6.  Change the SSH port from 22 to 2200. Make sure to configure the Lightsail firewall to allow it.
    ``` 1.  grader@ip-172-26-13-140:~$ sudo nano /etc/ssh/sshd_config``` 
        2. Change port 22 to port 2200
        3. Change "PermitRootLogin prohibit-password" to "PermitRootLogin no" to disable root login.
        4. Change "PasswordAuthentication no" to "PasswordAuthentication yes".
        5. Add "AllowUsers grader" at end of the file so that we will login through grader and save
        6. Confirm editing was saved by checking the file with:
          ```$ sudo cat /etc/ssh/sshd_config``` 
        7. Restart the SSH service
          ```$ sudo service ssh restart``` 

Warning: When changing the SSH port, make sure that the firewall is open for port 2200 first, so that you don't lock yourself out of the server. Review this video for details! When you change the SSH port, the Lightsail instance will no longer be accessible through the web app 'Connect using SSH' button. The button assumes the default port is being used. There are instructions on the same page for connecting from your terminal to the instance. Connect using those instructions and then follow the rest of the steps.


7. Configure the local timezone to UTC
    ``` $ sudo dpkg-reconfigure tzdata``` 
    Note: choose "None of the above" and hit enter
    choose UTC and hit enter from the options
    

8. Install and configure Apache to serve a Python mod_wsgi application.
    1.  Install Apache using  
        ``` $ sudo apt-get install apache2``` 
        Note: you will see the apache ubuntu default page when you type in "52.33.99.231" into the public ip address into a url page.      
    2.  Install mod_wsgi using 
        ```$ sudo apt-get install python-setuptools libapache2-mod-wsgi```
        
    3.  Configure apache to handle requests using the WSGI module by editng file.
        ```$ sudo nano /etc/apache2/sites-enabled/000-default.conf```
          
    4. Add "WSGIScriptAlias / /var/www/html/myapp.wsgi" before the closing block of Virtual Host.
        if you get an error, type:
        ```echo "ServerName localhost" | sudo tee /etc/apache2/conf-available/fqdn.conf```
        ```sudo a2enconf fqdn```
    
    5.  Restart apache2
        ```$ sudo service apache2 restart```
    
    6.  Test to verify Apache configurations work by writing a basic WSGI application.
       1.  Edit the wsgi file
          ```$ sudo nano /var/www/html/myapp.wsgi```
          
       2.  write a simple appliation
    ```def application(environ, start_response):
       status = '200 OK'
       output = 'Hello World!'

       response_headers = [('Content-type', 'text/plain'), ('Content-Length', str(len(output)))]
       start_response(status, response_headers)

       return [output]```
       
  Note:  Check "52.33.99.231" in your browser to see the Hello World! application run.  

 
 Install Git
    1. Install git 
    ```$ sudo apt-get install git```
    2. Setup git
    ```$ git config --global user.name "username"```
    ```$ git config --global user.email "email@domain.com"```
    3. Check configurations
    ```$ git config --list```

 
 Deploy Flask Application
    1.  
        1. Install python-dev because mod-wsgi is already installed
           ```$ sudo apt-get install python-dev```
        2. enable mod_wsgi
            ```$ sudo a2enmod wsgi```
    2.  
        1.  move into the /var/www directory
            1.  create the app directory structure
                ```$ sudo mkdir catalog```
            2.  move into that directory
                ```$ cd catalog```
            3.  create another directory
                ```$ sudo mkdir catalog```
            4.  make 2 subdirectories within the next catalog directory named static and templates
                ```$ cd catalog```
                ```$ sudo mkdir static templates```
            5.  make the init.py file
                ```$ sudo nano __init__.py```
            6.  Add this content to the file and close and save
                ```from flask import Flask
                app = Flask(__name__)
                @app.route("/")
                def hello():
                    return "Hello, I love Digital Ocean!"
                if __name__ == "__main__":
                app.run()```
    3.  
        1.  Create a virtual environment for our flask app
            ```$ sudo apt-get install python-pip```
        2.  Install virtualenv
            ```sudo pip install virtualenv```
            Note: run "export LC_ALL=C" if you receive an unsupported locale setting issue
        3.  Set the environment name
            ```$ sudo virtualenv venv```
            Note: if virutalenv command does not work
            ```$ sudo /usr/bin/easy_install virtualenv```
        4.  Install Flask by activating virtual environment:
            ```$ source venv/bin/activate```
        5.  Install Flask
            ```$ sudo pip install Flask```
        6.  Test to see if installation was successful
            ```$ sudo python __init__.py```
            Note:  should display "* Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)"
        7.  Deactivate virtual environment 
            ```$ deactivate```
            
            
   4.  
        1.  Edit FlaskApp file
        ```$ sudo nano /etc/apache2/sites-available/catalog.conf```
        2.  Configure the virtual host by adding your servername
            ```<VirtualHost *:80>
                ServerName mywebsite.com
                ServerAdmin admin@mywebsite.com
                WSGIScriptAlias / /var/www/FlaskApp/flaskapp.wsgi
                <Directory /var/www/FlaskApp/FlaskApp/>
                    Order allow,deny
                    Allow from all
                </Directory>
                Alias /static /var/www/FlaskApp/FlaskApp/static
                <Directory /var/www/FlaskApp/FlaskApp/static/>
                    Order allow,deny
                    Allow from all
                </Directory>
                ErrorLog ${APACHE_LOG_DIR}/error.log
                LogLevel warn
                CustomLog ${APACHE_LOG_DIR}/access.log combined
            </VirtualHost>```
            Note: Save and close
         3.  Enable virtual host
             ```$ sudo a2ensite FlaskApp```
             Note: if you ge an error for FlaskApp.conf file, so edit the FlaskApp.conf file by setting your Servername, ServerAdmin as:

    ```<VirtualHost *:80>
         ServerName 34.214.197.23
         ServerAdmin admin@34.214.197.23
         ServerAlias ec2-34-214-197-23.us-west-2.compute.amazonaws.com
     ```
   5.  
        i.  Create a wsgi file
            ```$ cd /var/www/catalog```
            ```$ sudo nano catalog.wsgi```
        ii. add the below content to the file
            ```#!/usr/bin/python
            import sys
            import logging
            logging.basicConfig(stream=sys.stderr)
            sys.path.insert(0,"/var/www/catalog/")
            from catalog import app as application
            application.secret_key = 'Add your secret key'```
            Note:  current directory structure should be:
                |--------catalog
                |----------------catalog
                |-----------------------static
                |-----------------------templates
                |-----------------------venv
                |-----------------------__init__.py
                |----------------catalog.wsgi
      6.  
        i.  Restart Apache
            ```$ sudo service apache2 restart```
  
  
  Clone the Github Repository
    1.  install Git 
        ```$ sudo apt-get install git```
    2.  Use cd ~/ to move into the directory
        ```$ cd ~/```
    3.  Create the app directory named code
        ```$ mkdir code```
    5.  Clone the Catalog App to the virtual machine
        ```$ cd code```
        ```$ git clone https://github.com/chezze911/FSND-Virtual-Machine-2.git```
    6.  Move catalog project to /var/www/catalog/catalog directory
        ```$ sudo mv FSND-Virtual-Machine-2/vagrant/catalog /var/www/catalog/catalog```
    7.  Move to the inner catalog directory
        ```$ cd catalog```
    8. folder structure should be:
        ```grader@ip-172-26-3-24: /var/www/catalog/catalog$ ls```
        within catalog(README.txt                   catalog_database_setup.pyc  fb_client_secrets.json
         Vagrantfile                  catalog_lotsofitems.py      lib
         appengine_config.py          catalog_project.py          static
         catalog_client_secrets.json  catalog_webserver.py        templates
         catalog_database_setup.py    catalogitemswithusers.db    webserver.py)
       (catalog_client_secrets.json     fb_client_secrets.json      catalog_lotsofitems.py      templates
        catalog_database_setup.py)    __init__.py      static      venv
    9. Create and open .htaccess file:
       ```$ cd /var/www/catalog/ and $ sudo vim .htaccess```
         Paste in the following:
         ```RedirectMatch 404 /\.git```
    10.  Install other Packages in (venv) grader@ip-172-26-13-140: /var/www/catalog/catalog/catalog
        1. ```$ sudo apt-get install python-pip```
        2.  ```$ source venv/bin/activate```
        3.  ```$ sudo pip install httplib2```
        4.  ```$ sudo pip install requests```
        5.  ```$ sudo pip install --upgrade oauth2client```
        Note:  run if you receive locale.Error:  unsupported locale settings
               ```$ export LC_ALL=C```
        6. ```$ sudo pip install sqlalchemy```
        7.  ```$ sudo pip install Flask-SQLAlchemy```
        8.  ```$ sudo pip install flask-seasurf```
  
    Note:   ```$ sudo mv FSND-Virtual-Machine-2/vagrant/catalog /var/www/catalog/catalog/```
    
    
    
   
Install PostgreSQL
    1.  Intall Python PostgreSQL adapter psycopg
        ```$ sudo apt-get install python-psycopg2```
    2.  Install PostgreSQL
        ```$ sudo apt-get install postgresql postgresql-contrib```
    3.  update the create_engine line in catalog_database_setup.py, catalog_project.py and catalog_lotsofitems.py
        ```engine = create_engine('postgresql://catalog:password@localhost/catalog')```
    4.  move the catalog_project.py file into __init__.py
        ```$ mv catalog_project.py __init__.py```
    5.  Change to default user postgres
        ```$ sudo su - postgres```
    6.  Connect to the system
        ```$ psql```
    7.  Create user catalog
        ```# CREATE USER catalog WITH PASSWORD 'password';```
    8. check lists of roles
        ```# \du```
    9.  Allow user to create database
        ```# ALTER USER catalog CREATEDB;```
    10. create database
        ```# CREATE DATABASE catalog WITH OWNER catalog;```
    11. connect to the database
        ```# \c catalog```
    12. Revoke all rights
        ```# REVOKE ALL ON SCHEMA public FROM public;```
    13.  Grant access to the catalog
        ```# GRANT ALL ON SCHEMA public TO catalog;```
    14.  When catalog_database_setup.py is executed, you can login to psql and check the tables as followed
        1.  Connect to the database
            ```\c catalog```
        2.  To see the tables in the schema
            ```\dt```
        3.  To see a single table
            ```\d [tablename]```
        4.  To see entries/data in a table
            ```select * from [tablename];```
        5.  To drop the table
            ```drop table [tablename];```
        6.  exit PostgreSQL:
            ``` \q```
            ``` exit```
        7.  restart
            ```$ sudo service postgresql restart```
      
        
If you built your project with Python 3, you will need to install the Python 3 mod_wsgi package on your server: sudo apt-get install libapache2-mod-wsgi-py3.
11. Install and configure PostgreSQL:
    1.  Install PostgreSQL
    ```$ sudo apt-get install postgresql```
    2. Login as user PostgreSQL
    ```$ sudo su - postgres```
    3. Get into the PostgreSQL shell with the command 
    ```$ psql```
    4.  Create a new database named catalog and create a new user named catalog in a postgreSQL shell
    ```postgre=# CREATE DATABASE catalog;```
    ```postgre=# CREATE USER catalog;```
        
    5.  Create a password for the user catalog
     ```postgres=# ALTER ROLE catalog WITH PASSWORD 'password';```
        
    6.  Give user catalog permission to catalog the appliation database
    ```postgres=# GRANT ALL PRIVILEDGES ON DATABASE catalog TO catalog;```
    
    7.  Quit and exit postgreSQL 
    ```postgre=# \q```
    ```postgre=# exit```
       
  
  Run the Application
  1.  Create the database schema
        python catalog_database_setup.py
        Note: ImportError: No module named sqlalchemy
        ```$ pip install Flask-SQLAlchemy```
        ImportError: No module named psycopg2
        ```$ pip install psycopg2```
        python catalog_lotsofitems.py
        
Note: if sqlalchemy.exc.DataError: (psycopg2.DataError) value too long for type character varying(250)
 
 1.  Go into psql from (/var/www/catalog/catalog) and change TABLE catalog_item COLUMN description TYPE character varying(250)
 and increase the number of characters to 2500.
 
 FIX:
 catalog=# ALTER TABLE public.catalog_item ALTER COLUMN description TYPE varchar (2500);
 
 resources:
 https://discussions.udacity.com/t/reset-postgresql-database/230592/3
 
 
        Note: if you run into ImportError: No module named sqlalchemy issue,
              pip install Flask-SQLAlchemy
              check installed versions with: pip freeze
  2.  Restart Apache
        sudo service apache2 restart
  3.  in the directory /var/www/catalog/catalog/catalog
        python __init__.py
        

If getting an internal server error, check the Apache error files:
Source: A2 Hosting
View the last 20 lines in the error log: $ sudo tail -20 /var/log/apache2/error.log
*If a file like 'g_client_secrets.json' couldn't been found:
Source: Stackoverflow


        Note: Might run into issues related to /etc/apache2/sites-available/FlaskApp.conf
        
        WSGIScriptAlias / /var/www/FlaskApp/flaskapp.wsgi
        <Directory /var/www/FlaskApp/FlaskApp/>
        Alias /static /var/www/FlaskApp/FlaskApp/static
        <Directory /var/www/FlaskApp/FlaskApp/static/>
  
        - Change the path /var/www/FlaskApp/flaskapp.wsgi to /var/www/catalog/catalog.wsgi in all lines
        Should look like this:
        <VirtualHost *:80>
        ServerName 34.214.197.23
        ServerAdmin admin@34.214.197.23
        ServerAlias ec2-34-214-197-23.us-west-2.compute.amazonaws.com
        WSGIScriptAlias / /var/www/catalog/catalog.wsgi
        <Directory /var/www/catalog/catalog.wsgi>
            Order allow,deny
            Allow from all
        </Directory>
        Alias /static /var/www/catalog/catalog.wsgi            
        <Directory /var/www/catalog/catalog.wsgi>
            Order allow,deny
            Allow from all
        </Directory>
        ErrorLog ${APACHE_LOG_DIR}/error.log
        LogLevel warn
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
        - related to catalog_client_secrets.json and fb_client_secrets.json files.  
          Give Absolute path to these files.
        - Edit __init__.py file
          Change Client_ID = json.loads( open('catalog_client_secrets.json', 'r'). read())['web']['client_id']
          open(r'/var/www/FlaskApp/FlaskApp/catalog/catalog_client_secrets.json', 'r').read())['web']['client_id']'''
          Similarly for fb_client_secrets.json file
        - Check last 10 errors in /var/log/apache2/error.log files
          sudo tail -10 /var/log/apache2/error.log
          Error:
          grader@ip-172-26-3-24:/var/www/FlaskApp/FlaskApp/catalog$ sudo tail -10 /var/log/apache2/error.log
[Sun Feb 11 00:24:51.566297 2018] [wsgi:warn] [pid 15969:tid 140132744648576] mod_wsgi: Compiled for Python/2.7.11.
[Sun Feb 11 00:24:51.566329 2018] [wsgi:warn] [pid 15969:tid 140132744648576] mod_wsgi: Runtime using Python/2.7.12.
[Sun Feb 11 00:24:51.566671 2018] [mpm_event:notice] [pid 15969:tid 140132744648576] AH00489: Apache/2.4.18 (Ubuntu) mod_wsgi/4.3.0 Python/2.7.12 configured -- resuming normal operations
[Sun Feb 11 00:24:51.566682 2018] [core:notice] [pid 15969:tid 140132744648576] AH00094: Command line: '/usr/sbin/apache2'
[Sun Feb 11 01:15:34.402512 2018] [mpm_event:notice] [pid 15969:tid 140132744648576] AH00491: caught SIGTERM, shutting down
[Sun Feb 11 01:15:34.459332 2018] [wsgi:warn] [pid 16421:tid 140128246941568] mod_wsgi: Compiled for Python/2.7.11.
[Sun Feb 11 01:15:34.459362 2018] [wsgi:warn] [pid 16421:tid 140128246941568] mod_wsgi: Runtime using Python/2.7.12.
[Sun Feb 11 01:15:34.459688 2018] [mpm_event:notice] [pid 16421:tid 140128246941568] AH00489: Apache/2.4.18 (Ubuntu) mod_wsgi/4.3.0 Python/2.7.12 configured -- resuming normal operations
[Sun Feb 11 01:15:34.459702 2018] [core:notice] [pid 16421:tid 140128246941568] AH00094: Command line: '/usr/sbin/apache2'
[Sun Feb 11 01:17:07.621369 2018] [wsgi:error] [pid 16425:tid 140128151066368] [client 146.74.94.82:54748] Target WSGI script not found or unable to stat: /var/www/FlaskApp/flaskapp.wsgi
Resources:
https://discussions.udacity.com/t/client-secret-json-not-found-error/34070/2

Change 
oauth_flow = flow_from_clientsecrets('catalog_client_secrets.json', scope='')
to
oauth_flow = flow_from_clientsecrets(r'/var/www/FlaskApp/FlaskApp/catalog/catalog_client_secrets.json', scope='')
        - Restart apache2 server
        sudo service apache2 restart
        
grader@ip-172-26-3-24:/var/www/FlaskApp/FlaskApp/catalog$ sudo tail -10 /var/log/apache2/error.log
[Mon Feb 12 06:25:01.606152 2018] [wsgi:warn] [pid 16421:tid 140128246941568] mod_wsgi: Compiled for Python/2.7.11.
[Mon Feb 12 06:25:01.606687 2018] [wsgi:warn] [pid 16421:tid 140128246941568] mod_wsgi: Runtime using Python/2.7.12.
[Mon Feb 12 06:25:01.606736 2018] [mpm_event:notice] [pid 16421:tid 140128246941568] AH00489: Apache/2.4.18 (Ubuntu) mod_wsgi/4.3.0 Python/2.7.12 configured -- resuming normal operations
[Mon Feb 12 06:25:01.606740 2018] [core:notice] [pid 16421:tid 140128246941568] AH00094: Command line: '/usr/sbin/apache2'
[Tue Feb 13 00:07:51.612702 2018] [mpm_event:notice] [pid 16421:tid 140128246941568] AH00491: caught SIGTERM, shutting down
[Tue Feb 13 00:07:52.625190 2018] [wsgi:warn] [pid 17445:tid 139905621985152] mod_wsgi: Compiled for Python/2.7.11.
[Tue Feb 13 00:07:52.625231 2018] [wsgi:warn] [pid 17445:tid 139905621985152] mod_wsgi: Runtime using Python/2.7.12.
[Tue Feb 13 00:07:52.625606 2018] [mpm_event:notice] [pid 17445:tid 139905621985152] AH00489: Apache/2.4.18 (Ubuntu) mod_wsgi/4.3.0 Python/2.7.12 configured -- resuming normal operations
[Tue Feb 13 00:07:52.625615 2018] [core:notice] [pid 17445:tid 139905621985152] AH00094: Command line: '/usr/sbin/apache2'

Fix: 
Delete ServerAlias line and ServerNAme line in File: /etc/apache2/sites-available/catalog.conf
sudo nano /etc/apache2/sites-available/catalog.conf


<VirtualHost *:80>
        ServerAdmin admin@34.214.197.23
        WSGIScriptAlias / /var/www/catalog/catalog.wsgi
        <Directory /var/www/catalog/catalog.wsgi>
            Order allow,deny
            Allow from all
        </Directory>
        Alias /static /var/www/catalog/catalog.wsgi
        <Directory /var/www/catalog/catalog.wsgi>
            Order allow,deny
            Allow from all
        </Directory>
        ErrorLog ${APACHE_LOG_DIR}/error.log
        LogLevel warn
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>



My current catalog.wsgi file:

#!/usr/bin/python
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/FlaskApp/")

from FlaskApp import app as application
application.secret_key = 'Add your secret key'

change catalog.wsgi file path to /var/www/catalog as followed:
#!/usr/bin/python
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/catalog/")

from FlaskApp import app as application
application.secret_key = 'Add your secret key'


To disable the default site:
sudo a2dissite 000-default

Reload apache2 service:
sudo service apache2 reload


Resources:
https://discussions.udacity.com/t/issue-getting-my-application-to-run-on-linux-server/473507/60
https://manpages.debian.org/jessie/apache2/a2ensite.8.en.html

  4.  add the ip address(http://34.214.197.23/) into your URL
  
grader@ip-172-26-3-24:~$ sudo tail -f /var/log/apache2/error.log
[Tue Feb 13 00:34:07.419808 2018] [mpm_event:notice] [pid 17445:tid 139905621985152] AH00491: caught SIGTERM, shutting down
[Tue Feb 13 00:34:08.421764 2018] [wsgi:warn] [pid 18063:tid 140533540386688] mod_wsgi: Compiled for Python/2.7.11.
[Tue Feb 13 00:34:08.421793 2018] [wsgi:warn] [pid 18063:tid 140533540386688] mod_wsgi: Runtime using Python/2.7.12.
[Tue Feb 13 00:34:08.422160 2018] [mpm_event:notice] [pid 18063:tid 140533540386688] AH00489: Apache/2.4.18 (Ubuntu) mod_wsgi/4.3.0 Python/2.7.12 configured -- resuming normal operations
[Tue Feb 13 00:34:08.422174 2018] [core:notice] [pid 18063:tid 140533540386688] AH00094: Command line: '/usr/sbin/apache2'
[Tue Feb 13 00:43:13.950517 2018] [mpm_event:notice] [pid 18063:tid 140533540386688] AH00493: SIGUSR1 received.  Doing graceful restart
[Tue Feb 13 00:43:13.970210 2018] [wsgi:warn] [pid 18063:tid 140533540386688] mod_wsgi: Compiled for Python/2.7.11.
[Tue Feb 13 00:43:13.970222 2018] [wsgi:warn] [pid 18063:tid 140533540386688] mod_wsgi: Runtime using Python/2.7.12.
[Tue Feb 13 00:43:13.970254 2018] [mpm_event:notice] [pid 18063:tid 140533540386688] AH00489: Apache/2.4.18 (Ubuntu) mod_wsgi/4.3.0 Python/2.7.12 configured -- resuming normal operations
[Tue Feb 13 00:43:13.970259 2018] [core:notice] [pid 18063:tid 140533540386688] AH00094: Command line: '/usr/sbin/apache2'

FIX: file: /etc/apache2/sites-available/FlaskApp.conf
delete lines ServerName and ServerAlias

Updated version:
<VirtualHost *:80>
                ServerAdmin admin@34.214.197.23
                WSGIScriptAlias / /var/www/FlaskApp/FlaskApp.wsgi
                <Directory /var/www/FlaskApp/FlaskApp.wsgi>
                        Order allow,deny
                        Allow from all
                </Directory>
                Alias /static /var/www/FlaskApp/FlaskApp/catalog/static
                <Directory /var/www/FlaskApp/FlaskApp/catalog/static>
                        Order allow,deny
                        Allow from all
                </Directory>
                ErrorLog ${APACHE_LOG_DIR}/error.log
                LogLevel warn
                CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

grader@ip-172-26-3-24:/var/www/FlaskApp/FlaskApp/catalog$ sudo tail -f /var/log/apache2/error.log
[Tue Feb 13 01:49:28.083348 2018] [wsgi:warn] [pid 2083:tid 139809771669376] mod_wsgi: Compiled for Python/2.7.11.
[Tue Feb 13 01:49:28.083358 2018] [wsgi:warn] [pid 2083:tid 139809771669376] mod_wsgi: Runtime using Python/2.7.12.
[Tue Feb 13 01:49:28.083388 2018] [mpm_event:notice] [pid 2083:tid 139809771669376] AH00489: Apache/2.4.18 (Ubuntu) mod_wsgi/4.3.0 Python/2.7.12 configured -- resuming normal operations
[Tue Feb 13 01:49:28.083392 2018] [core:notice] [pid 2083:tid 139809771669376] AH00094: Command line: '/usr/sbin/apache2'
[Tue Feb 13 01:49:59.203306 2018] [wsgi:error] [pid 2304:tid 139809498556160] [client 146.74.94.82:58900] mod_wsgi (pid=2304): Target WSGI script '/var/www/catalog/catalog.wsgi' cannot be loaded as Python module.
[Tue Feb 13 01:49:59.203333 2018] [wsgi:error] [pid 2304:tid 139809498556160] [client 146.74.94.82:58900] mod_wsgi (pid=2304): Exception occurred processing WSGI script '/var/www/catalog/catalog.wsgi'.
[Tue Feb 13 01:49:59.203347 2018] [wsgi:error] [pid 2304:tid 139809498556160] [client 146.74.94.82:58900] Traceback (most recent call last):
[Tue Feb 13 01:49:59.203360 2018] [wsgi:error] [pid 2304:tid 139809498556160] [client 146.74.94.82:58900]   File "/var/www/catalog/catalog.wsgi", line 7, in <module>
[Tue Feb 13 01:49:59.203397 2018] [wsgi:error] [pid 2304:tid 139809498556160] [client 146.74.94.82:58900]     from FlaskApp import app as application
[Tue Feb 13 01:49:59.203411 2018] [wsgi:error] [pid 2304:tid 139809498556160] [client 146.74.94.82:58900] ImportError: No module named FlaskApp
^C

FIX:
grader@ip-172-26-3-24:/var/www/catalog$ sudo nano catalog.wsgi

Note:
you need to change the following line in catalog.wsgi
this line "from FlaskApp import app as application"
instead of FlaskApp change it to catalog

from catalog import app as application

because it refers to the directory which is now named catalog

- restart apache2 and check error logs




Resources:
https://discussions.udacity.com/t/importerror-no-module-named-flaskapp/304285/3



Internal Server Error
The server encountered an internal error or misconfiguration and was unable to complete your request.
Please contact the server administrator at admin@52.33.99.231 to inform them of the time this error occurred, and the actions you performed just before this error.
More information about this error may be available in the server error log.
Apache/2.4.18 (Ubuntu) Server at 52.33.99.231 Port 80


sudo tail -10 /var/log/apache2/error.log 


[Tue Jun 19 14:22:57.101781 2018] [wsgi:error] [pid 620:tid 140212856399616] [client 173.184.76.138:50570]   File "/var/www/catalog/catalog.wsgi", line 1
[Tue Jun 19 14:22:57.101788 2018] [wsgi:error] [pid 620:tid 140212856399616] [client 173.184.76.138:50570]     <VirtualHost *:80>
[Tue Jun 19 14:22:57.101790 2018] [wsgi:error] [pid 620:tid 140212856399616] [client 173.184.76.138:50570]     ^
[Tue Jun 19 14:22:57.101793 2018] [wsgi:error] [pid 620:tid 140212856399616] [client 173.184.76.138:50570] SyntaxError: invalid syntax
[Tue Jun 19 14:22:57.410814 2018] [wsgi:error] [pid 620:tid 140212848006912] [client 173.184.76.138:50571] mod_wsgi (pid=620, process='', application='52.33.99.231|'): Failed to parse WSGI script file '/var/www/catalog/catalog.wsgi'.
[Tue Jun 19 14:22:57.410827 2018] [wsgi:error] [pid 620:tid 140212848006912] [client 173.184.76.138:50571] mod_wsgi (pid=620): Exception occurred processing WSGI script '/var/www/catalog/catalog.wsgi'.
[Tue Jun 19 14:22:57.410865 2018] [wsgi:error] [pid 620:tid 140212848006912] [client 173.184.76.138:50571]   File "/var/www/catalog/catalog.wsgi", line 1
[Tue Jun 19 14:22:57.410871 2018] [wsgi:error] [pid 620:tid 140212848006912] [client 173.184.76.138:50571]     <VirtualHost *:80>
[Tue Jun 19 14:22:57.410874 2018] [wsgi:error] [pid 620:tid 140212848006912] [client 173.184.76.138:50571]     ^
[Tue Jun 19 14:22:57.410877 2018] [wsgi:error] [pid 620:tid 140212848006912] [client 173.184.76.138:50571] SyntaxError: invalid syntax


<VirtualHost *:80>
        ServerName 52.33.99.231
        ServerAdmin admin@52.33.99.231
        ServerAlias ec2-52-33-99-231.us-west-2.compute.amazonaws.com
        WSGIScriptAlias / /var/www/catalog/catalog.wsgi
        <Directory /var/www/catalog/catalog.wsgi>
            Order allow,deny
            Allow from all
        </Directory>
        Alias /static /var/www/catalog/catalog.wsgi
        <Directory /var/www/catalog/catalog.wsgi>
            Order allow,deny
            Allow from all
        </Directory>
        ErrorLog ${APACHE_LOG_DIR}/error.log
        LogLevel warn
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

#!/usr/bin/python
  import sys
  import logging
  logging.basicConfig(stream=sys.stderr)
  sys.path.insert(0,"/var/www/catalog/")

  from catalog import app as application
  application.secret_key = 'Add your secret key'




 
 
  
      
      
 


    
    





 







