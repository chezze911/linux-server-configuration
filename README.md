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

ssh -i ~/.ssh/LightsailDefaultPrivateKey-us-west-2.pem ubuntu@34.214.197.23 -o ServerAliveInterval=5 -v
-o ServerAliveInternal=5

Note:  
The authenticity of host '34.209.213.255 (34.209.213.255)' can't be established.
ECDSA key fingerprint is SHA256:OhWe+pdUrlI5hXa2YD1cFCNHM2ycjekeyMmyNOme1lw.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '34.209.213.255' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 16.04.2 LTS (GNU/Linux 4.4.0-1013-aws x86_64)

private server:
ubuntu@ip-172-26-2-21


Secure your server.
3. Update all currently installed packages.
    1.  sudo apt-get update - This command will update list of packages and their versions
    2.  sudo apt-get upgrade - This command will install the packages
        i.  SELECT by highlighting "keep the local version currently installed" and hit ENTER


4.  Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123).
    1.  check the firewall status using sudo ufw status.
    2.  block all incoming connections on all ports using sudo ufw default deny incoming
    3.  allow outgoing connections on all ports using sudo ufw default allow outgoing
    4.  allow incoming connection for SSH(port 2200) using sudo ufw allow 2200/tcp
    5.  allow incoming connection for HTTP(port 80) using sudo ufw allow 80/tcp
    6.  allow incoming connection for NTP(port 123) using sudo ufw allow 123/udp
    7.  check the added rules using sudo ufw show added
    8.  enable the firewall using sudo ufw enable
    9.  check whether firewall is enable or not using sudo ufw status
    
5.  Change the SSH port from 22 to 2200. Make sure to configure the Lightsail firewall to allow it.
    1.  ubuntu@ip-172-13-120:~$ sudo nano /etc/ssh/sshd_config 
    and enter Yes to confirm editing by root
        i.   Change port 22 to port 2200
        ii.  Change "PermitRootLogin prohibit-password" to "PermitRootLogin no" to disable root login.
        iii. Change "PasswordAuthentication no" to "PasswordAuthentication yes".
        iv.  Add "AllowUsers grader" at end of the file so that we will login through grader and save
        v.   Confirm editing was saved by checking the file with:
            sudo cat /etc/ssh/sshd_config
    2.  Restart the SSH service : sudo service ssh restart

Warning: When changing the SSH port, make sure that the firewall is open for port 2200 first, so that you don't lock yourself out of the server. Review this video for details! When you change the SSH port, the Lightsail instance will no longer be accessible through the web app 'Connect using SSH' button. The button assumes the default port is being used. There are instructions on the same page for connecting from your terminal to the instance. Connect using those instructions and then follow the rest of the steps.

Give grader access.
In order for your project to be reviewed, the grader needs to be able to log in to your server.

6. Create a new user account named grader.
    1.  sudo adduser grader
    i.  To check the User "grader" information:
        sudo apt-get install finger
        finger grader
        (provides additional information(login , directory, name, shell, etc) of the user named grader)
     

7. Give grader the permission to sudo.
    1.  sudo visudo (edit the sudoers file . To save, use sudo visudo to edit the sudoers file otherwise file will not be saved)
    i.  add the below line of code after root ALL=(ALL:ALL) ALL grader ALL=(ALL:ALL) ALL and save it (ctrl-X , then Y and Enter)
    Your new user(grader) is able to execute commands with administrative privileges. ( for example - sudo anycommand)
    ii.  You can check the grader entry by entering the command: sudo cat /etc/sudoers
    iii.  Reload SSH to apply new settings
          $ reload ssh
          (use "sudo apt get upstart" if needed)

    
8. Create an SSH key pair for grader using the ssh-keygen tool on your local machine.
    1.  generate key-pair with "ssh-keygen"

    2.  Save keygen file into (/home/ubuntu/.ssh/udacity_key.rsa) and fill in the password. 2 keys will be generated, public key (udacity_key.rsa.pub) and identification key(udacity_key.rsa).
  

    3.  Login into the grader account using on your virtual machine:
        i. to switch to user "grader", 
        $ su - grader
        enter password
        on successful login, you will see 
        grader@ip-172-26-15-205
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
        
        Next, login to grader by entering the following commands:
        
        $ ssh -i ~/.ssh/udacity_key.rsa -p 2200 grader@54.201.205.236
        
        on successful login, you will see:  grader@ip-172-26-5-246:~$ 
        
 

        
        
        ssh -i ~/Downloads/LightsailDefaultPrivateKey-us-west-2.pem grader@34.213.177.211
        
        ssh -i ~/.ssh/linux_security_config -p 2200 grader@54.218.76.136 
        ssh -v grader@"public-IP-address" -p 2200.
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


 







