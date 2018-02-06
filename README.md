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
        
    iii.  Configure apache to handle requests using the WSGI module by ending the /etc/apache2/sites-enabled/000-default.conf file.  
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
                    return "Hello, everyone!"
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
    4.  Move inside that directory
        cd FlaskApp
    5.  Clone the Catalog App to the virtual machine
        git clone https://github.com/chezze911/FSND-Virtual-Machine-2.git
    6.  Rename the project name 
        sudo mv ./FSND-Virtual-Machine-2 ./FlaskApp
    7.  Move to the inner FlaskApp directory
        cd FlaskApp
    8.  Rename catalog_project.py to __init__.py
        sudo mv catalog_project.py __init__.py
    9. Edit catalog_database_setup.py, __init__.py, and catalog_lotsofitems.py and change
        engine = create_engine('sqlite://toyshop.db') to
        engine = create_engine('postgresql:///catalog:password@localhost/catalog')
    10.  Install pip
        sudo apt-get install python-pip
    11. Install dependencies
        sudo pip install -r requirements.txt
    12. Install psycopg2
        sudo apt-get -qqy install postgresql python-psycopg2
    13.  Create a database schema
        sudo python database_setup.py
 
        
    sudo mv FSND-Virtual-Machine-2/vagrant/catalog /var/www/catalog/catalog/
    
   

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
       
 

Do not allow remote connections
Create a new database user named catalog that has limited permissions to your catalog application database.
12. Install git.

    v.  From grader@ip-172-26-3-24:~$ 
        Clone the Catalog App to the virtual machine
        git clone https://github.com/chezze911/FSND-Virtual-Machine-2
    vi.  rename the project with the command sudo mv ./FSND-Virtual-Machine-2 ./FlaskApp
    vii.  Get into the FlaskApp directory with cd FlaskApp/vagrant/catalog
    
    viii.  rename website.py to __init__.py with sudo mv website.py __init__.py
    
    viiii. edit database_setup.py, website.py and functions_helper.py and change
            engine = create_engine('sqlite:///toyshop.db') to 
            engine = create_engine('postgresql://catalog:password@localhost/catalog')
    x.  Install pip with sudo apt-get install python-pip
    xi.  sudo pip install -r requirements.txt to install dependencies
    xii.  sudo apt-get -qqy install postgresql python-psycopg2 to install psycopg2
    xiii.  Create a database schema with 
            sudo python database_setup.py
    
    
Configure and Enable a New Virtual Host




 







