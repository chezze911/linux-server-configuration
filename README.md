# linux-server-configuration
Udacity FSND project

Get your server.
1. Start a new Ubuntu Linux server instance on Amazon Lightsail. There are full details on setting up your Lightsail instance on the next page.
2. Follow the instructions provided to SSH into your server.

Secure your server.
3. Update all currently installed packages.
    1.  sudo apt-get update - command will update list of packages and their versions on your machine.
    2.  sudo apt-get upgrade - command will install the packages
        i.  SELECT by highlighting "keep the local version currently installed" and hit ENTER


4. Change the SSH port from 22 to 2200. Make sure to configure the Lightsail firewall to allow it.
    1.  ubuntu@54.218.76.136:~$ sudo nano /etc/ssh/sshd_config 
    and enter Yes to confirm editing by root
        i.   Change port from 22 to 2200
        ii.  Change "PermitRootLogin prohibit-password" to "PermitRootLogin no". It will disable root login.
        iii. Change "PasswordAuthentication no" to "PasswordAuthentication yes".
        iv.  Add "AllowUsers grader" at end of the file so that we will login through grader and save it (ctrl-X , then Y and Enter)
        v.   Confirm editing was saved by checking the file with:
            sudo cat /etc/ssh/sshd_config
    2.  Restart the SSH service : sudo service ssh restart
    
5. Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123).
    1.  check the firewall status using sudo ufw status.
    2.  block all incoming connections on all ports using sudo ufw default deny incoming.
    3.  allow outgoing connections on all ports using sudo ufw default allow outgoing.
    4.  allow incoming connection for SSH(port 2200) using sudo ufw allow 2200/tcp.
    5.  allow incoming connection for HTTP(port 80) using sudo ufw allow 80/tcp.
    6.  allow incoming connection for NTP(port 123) using sudo ufw allow 123/udp.
    7.  check the added rules using sudo ufw show added.
    8.  enable the firewall using sudo ufw enable.
    9.  check whether firewall is enable or not using sudo ufw status.


Warning: When changing the SSH port, make sure that the firewall is open for port 2200 first, so that you don't lock yourself out of the server. Review this video for details! When you change the SSH port, the Lightsail instance will no longer be accessible through the web app 'Connect using SSH' button. The button assumes the default port is being used. There are instructions on the same page for connecting from your terminal to the instance. Connect using those instructions and then follow the rest of the steps.

Give grader access.
In order for your project to be reviewed, the grader needs to be able to log in to your server.

6. Create a new user account named grader.
    1.  sudo adduser grader
    i.  To check the User(grader) information:
        sudo apt-get install finger
        finger grader
        (It will give you additional information(login , name , shell, directory, phone number etc) of User-grader)
        
7. Give grader the permission to sudo.
    1.  sudo visudo (edit the sudoers file . To save, use sudo visudo to edit the sudoers file otherwise file will not be saved)
    i.  add the below line of code after root ALL=(ALL:ALL) ALL grader ALL=(ALL:ALL) ALL and save it (ctrl-X , then Y and Enter)
    Your new user(grader) is able to execute commands with administrative privileges. ( for example - sudo anycommand)
    ii.  You can check the grader entry by entering the command: sudo cat /etc/sudoers
    
    
8. Create an SSH key pair for grader using the ssh-keygen tool.
    1.  generate key-pair with "ssh-keygen"

    2.  Save keygen file into (/home/ubuntu/.ssh/linux_server_config) and fill in the password. 2 keys will be generated, public key (linux_server_config.pub) and identification key(linux_server_config).

    3.  Login into the grader account using ssh -v grader@"public-IP-address" -p 2200.
        ii.  vagrant@vagrant-ubuntu-trusty-64:~$: ssh -v grader@54.218.76.136 -p 2200
        iii.  enter password you chose during initial setup of grader.  
        iv.  on successful login, you will see:  grader@ip-54.218.76.136:~$
    
    4.  make a directory in grader account : mkdir .ssh

    5.  make a authorized_keys file using touch .ssh/authorized_keys

    6.  from your local machine,copy the contents of public key(linux_server_config.pub).
        i.  sudo cat /home/vagrant/.ssh/linux_server_config.pub
        ii.  copy and paste the contents into authorized_keys and save with control+X.

    7.  give the permissions : chmod 700 .ssh and chmod 644 .ssh/authorized_keys.
        i.  do nano /etc/ssh/sshd_config and change "PasswordAuthentication yes" to "PasswordAuthentication" no.
        ii.  sudo service ssh restart.
    
    8.  ssh grader@54.218.76.136 -p 2200 -i ~/.ssh/linux_server_config in new terminal .A pop-up window will open for       authentication. 
        i.  enter created password for grader

Prepare to deploy your project.
9. Configure the local timezone to UTC.
10. Install and configure Apache to serve a Python mod_wsgi application.

If you built your project with Python 3, you will need to install the Python 3 mod_wsgi package on your server: sudo apt-get install libapache2-mod-wsgi-py3.
11. Install and configure PostgreSQL:

Do not allow remote connections
Create a new database user named catalog that has limited permissions to your catalog application database.
12. Install git.


 







