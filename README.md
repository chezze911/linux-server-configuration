# linux-server-configuration
Udacity FSND project

Get your server.
1. Start a new Ubuntu Linux server instance on Amazon Lightsail. There are full details on setting up your Lightsail instance on the next page.
2. Follow the instructions provided to SSH into your server.

Public IP
34.214.197.23

User name
ubuntu

SSH into your server
Download default private key from AWS 
Do you want to add the certificate from the life "LightsaleDefaultPrivateKey-us-west-2.pem" to a keychain?
keychain login

- an error occured.  unable to import the item.

i.      mv ~/Downloads/LightsailDefaultPrivateKey-us-west-2.pem ~/.ssh/
ii.     chmod 600 ~/.ssh/LightsailDefaultPrivateKey-us-west-2.pem
iii.    ssh -i ~/.ssh/LightsailDefaultPrivateKey-us-west-2.pem ubuntu@34.214.197.23

ssh -i ~/.ssh/LightsailDefaultPrivateKey-us-west-2.pem ubuntu@34.214.197.23 

private server:
ubuntu@ip-172-26-3-24

Give grader access.
In order for your project to be reviewed, the grader needs to be able to log in to your server.

1. Create a new user account named grader.
    1.  sudo adduser grader
    i.  To check the User "grader" information:
        sudo apt-get install finger
        finger grader
        (provides additional information(login , directory, name, shell, etc) of the user named grader)
     

2. Give grader the permission to sudo.
    1.  sudo visudo (edit the sudoers file . To save, use sudo visudo to edit the sudoers file otherwise file will not be saved)
    i.  add the below line of code after root ALL=(ALL:ALL) ALL grader ALL=(ALL:ALL) ALL and save it (ctrl-X , then Y and Enter) Note: tab space between grader and ALL.
    Your new user(grader) is able to execute commands with administrative privileges. ( for example - sudo anycommand)
    ii.  You can check the grader entry by entering the command: sudo cat /etc/sudoers
    iii.  Reload SSH to apply new settings
          $ reload ssh
          (use command "sudo apt get upstart" if needed)

    
3. Create an SSH key pair for grader using the ssh-keygen tool on your local machine.
    1.  generate key-pair with "ssh-keygen"

    2.  Save keygen file into (/home/ubuntu/.ssh/udacity_key.rsa) and fill in the password. 2 keys will be generated, public key (udacity_key.rsa.pub) and identification key(udacity_key.rsa).
  

    3.  Login into the grader account using on your virtual machine:
        i. to switch to user "grader", 
        $ su - grader
        enter password
        on successful login, you will see 
        grader@ip-172-26-3-24
        In the grader account:
        ii.  create .ssh foler in /home/grader
        $ mkdir /home/grader/.ssh
        iii.  create an empty authorized_keys file
        $ touch /home/grader/.ssh/authorized_keys
        iv.  paste the public keys into authroized_keys from udacity_key.rsa.pub
        $ nano /home/grader/.ssh/authorized_keys
        
        copy and paste identification key from local machine .ssh/udacity_key.rsa.pub file
        to grader's .ssh/authorized_keys file and save it.
        
        Next, set file permissions using the following commands:
        $ chmod 700 .ssh
        $ chmod 644 .ssh/authorized_keys
        
        Next, do
        $ sudo nano /etc/ssh/sshd_config 
        and change PasswordAuthentication to no
        $ sudo service ssh restart
        
        Next, login to grader from another terminal by entering the following commands:
        
        $ ssh -i ~/.ssh/udacity_key.rsa grader@54.201.205.236
        
        on successful login, you will see:  grader@ip-172-26-5-246:~$ 


Secure your server.
4. Update all currently installed packages.
    1.  sudo apt-get update - This command will update list of packages and their versions
    2.  sudo apt-get upgrade - This command will install the packages
        i.  SELECT by highlighting "keep the local version currently installed" and hit ENTER


5.  Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123).
    1.  check the firewall status using sudo ufw status.
    2.  block all incoming connections on all ports using sudo ufw default deny incoming
    3.  allow outgoing connections on all ports using sudo ufw default allow outgoing
    4.  allow ssh connection using sudo ufw allow ssh
    4.  allow incoming connection for SSH(port 2200) using sudo ufw allow 2200/tcp
    5.  allow incoming connection for HTTP(port 80) using sudo ufw allow 80/tcp
    6.  allow incoming connection for NTP(port 123) using sudo ufw allow 123/udp
    7.  check the added rules using sudo ufw show added
    8.  enable the firewall using sudo ufw enable
    9.  check whether firewall is enable or not using sudo ufw status
    
6.  Change the SSH port from 22 to 2200. Make sure to configure the Lightsail firewall to allow it.
    1.  grader@ip-172-26-3-24:~$ sudo nano /etc/ssh/sshd_config 
    and enter Yes to confirm editing by root
        i.   Change port 22 to port 2200
        ii.  Change "PermitRootLogin prohibit-password" to "PermitRootLogin no" to disable root login.
        iii. Change "PasswordAuthentication no" to "PasswordAuthentication yes".
        iv.  Add "AllowUsers grader" at end of the file so that we will login through grader and save
        v.   Confirm editing was saved by checking the file with:
            sudo cat /etc/ssh/sshd_config
    2.  Restart the SSH service : sudo service ssh restart

Warning: When changing the SSH port, make sure that the firewall is open for port 2200 first, so that you don't lock yourself out of the server. Review this video for details! When you change the SSH port, the Lightsail instance will no longer be accessible through the web app 'Connect using SSH' button. The button assumes the default port is being used. There are instructions on the same page for connecting from your terminal to the instance. Connect using those instructions and then follow the rest of the steps.

login to grader account to see if port change from 22 to 2200 worked,

$ ssh -i ~/.ssh/udacity_key.rsa grader@54.201.205.236 -p 2200




Prepare to deploy your project.
9. Configure the local timezone to UTC.
    Use command sudo dpkg-reconfigure tzdata
    and choose UTC from the options
    
10. Install and configure Apache to serve a Python mod_wsgi application.
    i.  Install Apache using  
        sudo apt-get install apache2
        note:  you will see the apache ubuntu default page when you type in the public ip address 
            34.214.197.23 into a url page.  
    ii.  Install mod_wsgi using 
        sudo apt-get install python-setuptools libapache2-mod-wsgi
        
    iii.  Configure apache to handle requests using the WSGI module by editng the /etc/apache2/sites-enabled/000-default.conf file.  
    iv.  add "WSGIScriptAlias / /var/www/html/myapp.wsgi" before the closing black of  Virtual Host.
        if you get an error, use command "echo "ServerName localhost" | sudo tee /etc/apache2/conf-available/fqdn.conf && sudo a2enconf fqdn" in your terminal
    
    v.  Restart with sudo service apache2 restart
    
    vi.  Test to verify Apache configurations work by writing a basic WSGI application.
         1.  Edit the wsgi file
             sudo nano /var/www/html/myapp.wsgi
         2.  write a simple appliation
         
    def application(environ, start_response):
       status = '200 OK'
       output = 'Hello World!'

       response_headers = [('Content-type', 'text/plain'), ('Content-Length', str(len(output)))]
       start_response(status, response_headers)

       return [output]
       
  Note:  Check http://http://34.214.197.23/ to see the Hello World! application run.  
  
  
 
 Install Git
    1.  Install git 
        sudo apt-get install git
    2. Setup git
        git config --global user.name "username"
        git config --global user.email "email@domain.com"
    Note:  Check configurations with "git config --list"
    
    
 
 Deploy Flask Application (6 steps)
    1.  
        i. Install python-dev because mod-wsgi is already installed
            sudo apt-get install python-dev
        ii. enable mod_wsgi
            sudo a2enmod wsgi
    2.  
        i.  move into the /var/www directory
            1.  create the app directory structure
                sudo mkdir FlaskApp
            2.  move into that directory
                cd FlaskApp
            3.  create another directory
                sudo mkdir FlaskApp
            4.  make 2 subdirectories within the current directory named static and templates
                cd catalog
                sudo mkdir static templates
            5.  make the init.py file
                sudo nano __init__.py
            6.  Add this content to the file and close and save
                from flask import Flask
                app = Flask(__name__)
                @app.route("/")
                def hello():
                    return "Hello, I love Digital Ocean!"
                if __name__ == "__main__":
                app.run()
    3.  
        i.  Create a virtual environment for our flask app
            sudo apt-get install python-pip
        ii.  Install virtualenv
            sudo pip install virtualenv
            note: run "export LC_ALL=C" if you receive an unsupported locale setting issue
        iii.  Set the environment name
            sudo virtualenv venv
            note: "sudo /usr/bin/easy_install virtualenv" if virutalenv command does not work. 
        iv.  install Flask by activating virtual environment:
            source venv/bin/activate
        v.  install Flask
            sudo pip install Flask
        vi.  test to see if installation was successful
            sudo python __init__.py
            Note:  should display "* Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)"
            deactivate virtual environment with "deactivate"
            
            
   4.  
        i.  sudo nano /etc/apache2/sites-available/FlaskApp.conf
        ii.  Configure the virtual host by adding your servername
            <VirtualHost *:80>
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
            </VirtualHost>
            *Save and close file
         iii.  enable virtual host
                sudo a2ensite FlaskApp
                NOTE: iff you ge an error for FlaskApp.conf file, so edit the FlaskApp.conf file by setting your Servername, ServerAdmin as:

<VirtualHost *:80>
         ServerName 34.214.197.23
         ServerAdmin admin@34.214.197.23
         ServerAlias ec2-34-214-197-23.us-west-2.compute.amazonaws.com
         
   5.  
        i.  Create a wsgi file
            cd /var/www/catalog
            sudo nano catalog.wsgi
        ii. add the below content to the file
            #!/usr/bin/python
            import sys
            import logging
            logging.basicConfig(stream=sys.stderr)
            sys.path.insert(0,"/var/www/FlaskApp/")
            from FlaskApp import app as application
            application.secret_key = 'Add your secret key'
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
            sudo service apache2 restart
  
  
  Clone the Github Repository
    1.  install Git using sudo apt-get install git
    2.  Use cd /var/www to move into the directory
    3.  Create the app directory 
        sudo mkdir FlaskApp
    5.  Clone the Catalog App to the virtual machine
        grader@ip-172-26-3-24:~$ git clone https://github.com/chezze911/FSND-Virtual-Machine-2.git
    6.  Move catalog project to /var/www/FlaskApp/FlaskApp directory
        sudo mv FSND-Virtual-Machine-2/vagrant/catalog /var/www/FlaskApp/FlaskApp
    7.  Move to the inner FlaskApp directory
        cd FlaskApp
    8. folder structure should be:
        grader@ip-172-26-3-24: /var/www/FlaskApp/FlaskApp$ ls
        within catalog(catalog_client_secrets.json     fb_client_secrets.json      catalog_lotsofitems.py      templates
        catalog_database_setup.py)    __init__.py      static      venv
    10.  Install other Packages
        i. sudo apt-get install python-pip
        ii.  source venv/bin/activate
        iii.  sudo pip install httplib2
        iv.  sudo pip install requests
        v.  sudo pip install --upgrade oauth2client
        vi. sudo pip install sqlalchemy
        vii.  sudo pip install Flask-SQLAlchemy
        viii.  sudo pip install flask-seasurf
        
 
        
    sudo mv FSND-Virtual-Machine-2/vagrant/catalog /var/www/catalog/catalog/
    
    
    
   
Install PostgreSQL
    1.  Intall Python PostgreSQL adapter psycopg
        sudo apt-get install python-psycopg2
    2.  Install PostgreSQL
        sudo apt-get install postgresql postgresql-contrib
    3.  update the create_engine line in catalog_database_setup.py, catalog_project.py and catalog_lotsofitems.py
        engine = create_engine('postgresql://catalog:password@localhost/catalog')
    4.  move the catalog_project.py file into init.py
        mv catalog_project.py init.py
    5.  Change to default user postgres
        sudo su - postgres
    6.  Connect to the system
        psql
    7.  Create user catalog
        CREATE USER catalog WITH PASSWORD 'password';
    8. check lists of roles
        \du
    9.  Allow user to create database
        ALTER USER catalog CREATEDB;
    10. create database
        CREATE DATABASE catalog WITH OWNER catalog;
    11. connect to the database
        \c catalog
    12. Revoke all rights
        REVOKE ALL ON SCHEMA public FROM public;
    13.  Grant access to the catalog
        GRANT ALL ON SCHEMA public TO catalog;
    14.  When catalog_database_setup.py is executed, you can login to psql and check the tables as followed
        i.  Connect to the database
            \c catalog
        ii.  To see the tables in the schema
            \dt
        iii.  To see a single table
            \d [tablename]
         iv.  To see entries/data in a table
            select * from [tablename];
         v.  To drop the table
            drop table [tablename];
         vi.  exit PostgreSQL:
            \q
            exit
         vii.  restart
              sudo service postgresql restart
      
        
If you built your project with Python 3, you will need to install the Python 3 mod_wsgi package on your server: sudo apt-get install libapache2-mod-wsgi-py3.
11. Install and configure PostgreSQL:
    i.  sudo apt-get install postgresql
    ii. login as user postgres
        sudo su - postgres
    iii. Get into the postgreSQL shell with the command psql
    iv.  create a new database named catalog and create a new user named catalog in a postgreSQL shell
        postgre=# CREATE DATABASE catalog;
        postgre=# CREATE USER catalog;
        
    v.  Create a password for the user catalog
        postgres=# ALTER ROLE catalog WITH PASSWORD 'password';
        
    vi.  Give user catalog permission to catalog the appliation database
            postgres=# GRANT ALL PRIVILEDGES ON DATABASE catalog TO catalog;
    
    vii.  Quit and exit postgreSQL 
            postgre=# \q
            postgre=# exit
       
  
  Run the Application
  1.  Create the database schema
        python catalog_database_setup.py
        python catalog_lotsofitems.py
        error in catalog_lotsofitems.py
        sqlalchemy.exc.DataError: (psycopg2.DataError) value too long for type character varying(250)
 [SQL: 'INSERT INTO catalog_item (name, description, price, catalog_id, user_id) VALUES (%(name)s, %(description)s, %(price)s, %(catalog_id)s, %(user_id)s) RETURNING catalog_item.id'] [parameters: {'price': '$200', 'description': 'This project lets you experience multiple 3D environments from the Udacity VR program. You will download the Udacity VR app and run it inside of your ... (35 characters truncated) ...  the headset, you will walkaround an apartment and island from the VR Nanodegree and see demonstrations of various VR techniques you will soon learn.', 'user_id': 1, 'catalog_id': 6, 'name': 'Project 0: What is the Code?'}] (Background on this error at: http://sqlalche.me/e/9h9h)
 
 NOTE:
 Go into psql and change TABLE catalog_item COLUMN description TYPE character varying(250)
 and increase the number of characters to 2500.
 
 FIX:
 ALTER TABLE public.catalog_item ALTER COLUMN description TYPE varchar (2500);
 
 resources:
 https://discussions.udacity.com/t/reset-postgresql-database/230592/3
 
 
        Note: if you run into ImportError: No module named sqlalchemy issue,
              pip install Flask-SQLAlchemy
              check installed versions with: pip freeze
  2.  Restart Apache
        sudo service apache2 restart
  3.  in the directory /var/www/FlaskApp/FlaskApp/catalog
        python __init__.py
        
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

on http://34.214.197.23/,
output:
Hello World again!

Resources:
https://discussions.udacity.com/t/importerror-no-module-named-flaskapp/304285/3


  OAUTH Login
  1.  Go to http://www.hcidata.info/host2ip.cgi to get the host name of your public IP address (34.214.197.23).
      IP Address (34.214.197.23) = Host Name (ec2-34-214-197-23.us-west-2.compute.amazonaws.com)
  2.  Add this hostname below ServerAdmin (ServerAlias ec2-34-214-197-23.us-west-2.compute.amazonaws.com)
      sudo nano /etc/apache2/sites-available/catalog.conf
      
      <VirtualHost *:80>
        ServerName 34.214.197.23
        ServerAdmin admin@34.214.197.23
        ServerAlias ec2-34-214-197-23.us-west-2.compute.amazonaws.com  
        WSGIScriptAlias / /var/www/FlaskApp/flaskapp.wsgi
        <Directory /var/www/FlaskApp/FlaskApp/catalog>
            Order allow,deny
            Allow from all
        </Directory>
        Alias /static /var/www/FlaskApp/FlaskApp/catalog/static
        <Directory /var/www/FlaskApp/FlaskApp/catalog/static/>
            Order allow,deny
            Allow from all
        </Directory>
        ErrorLog ${APACHE_LOG_DIR}/error.log
        LogLevel warn
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>



  3.  enable the virtual host
      sudo a2ensite catalog
  4.  restart apacheserver
      sudo service apache2 restart
  5.  Google Authorization Steps:
        i.  Go to https://console.developer.google.com
        ii.  Choose your item catalog app and then click on Credentials, and then edit
        iii.  Add your hostname (http://ec2-34-214-197-23.us-west-2.compute.amazonaws.com) and public IP address
              (http://34.214.197.23) to Authorized JavaScript origins.
        iv.  Add hostname (http://ec2-34-214-197-23.us-west-2.compute.amazonaws.com/oauth2callback) 
             to Authorized redirect URIs.
        v.  Update catalog_client_secrets.json file by adding hostname and public IP address

grader@ip-172-26-3-24:/var/www/FlaskApp/FlaskApp/catalog$ python init.py
Traceback (most recent call last):
  File "init.py", line 18, in <module>
    open('catalog_client_secrets.json', 'r').read())['web']['client_id']
  File "/usr/lib/python2.7/json/__init__.py", line 339, in loads
    return _default_decoder.decode(s)
  File "/usr/lib/python2.7/json/decoder.py", line 364, in decode
    obj, end = self.raw_decode(s, idx=_w(s, 0).end())
  File "/usr/lib/python2.7/json/decoder.py", line 380, in raw_decode
    obj, end = self.scan_once(s, idx)
ValueError: Expecting property name: line 1 column 445 (char 444)


 6.  Facebook Authorizaiton Steps:
        i.  Go to https://developers.facebook.com/
        ii.  Open your item catalog application from the drop down menu of my apps and click on Facebook Login 
             and then settings
        iii.  Add your hostname and public IP address to Valid OAuth redirect URIs and Save Changes
        iv.  Update your fb_client_secret.json file by adding hostname and public IP address
        
        file: fb_client_secrets.json
        {
  "web": {
    "app_id": "1673077866333201",
    "app_secret": "f20f65de14ce19d39de107e6edf953e1",
    "hostname": "http://ec2-35-165-147-241.us-west-2.compute.amazonaws.com",
    "public_ip_address": "http://35.165.147.241"
  }
}

grader@ip-172-26-3-24:/var/www/FlaskApp/FlaskApp/catalog$ python init.py
After running python __init__.py:

Traceback (most recent call last):
  File "init.py", line 18, in <module>
    open('catalog_client_secrets.json', 'r').read())['web']['client_id']
  File "/usr/lib/python2.7/json/__init__.py", line 339, in loads
    return _default_decoder.decode(s)
  File "/usr/lib/python2.7/json/decoder.py", line 364, in decode
    obj, end = self.raw_decode(s, idx=_w(s, 0).end())
  File "/usr/lib/python2.7/json/decoder.py", line 380, in raw_decode
    obj, end = self.scan_once(s, idx)
ValueError: Expecting property name: line 1 column 492 (char 491)
 
  
      
      
 


    
    





 







