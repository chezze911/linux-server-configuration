# linux-server-configuration
Udacity FSND project

Tasks
1.  Launch your Virtual Machine with your Udacity account
2.  Follow the instructions provided to SSH into your server
3.  Create a new user named grader
4.  Give the grader the permission to sudo
5.  Update all currently installed packages
6.  Change the SSH port from 22 to 2200
7.  Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200),
HTTP (port 80), and NTP (port 123)
8.  Configure the local timezone to UTC
9.  Install and configure Apache to serve a Python mod_wsgi application
10.  Install and configure PostegreSQL:
    i.  Do not allow remote connections
    ii.  Create a new user named catalot that has limited permissions to your catalog application database
11. Install git, clone and setup your Catalog App project (from your GitHub repository from earlier in the Nanodegree program)
    so that it functions correctly when visiting your server's IP address in a browser.  Remember to set this up
    appropriately so that your .git directory is not publicly accessible via a browser.

Launch Virtual Machine

Instructions for SSH access to the instance

Configuration Steps:
Step 1: Launch your Virtual Machine with your Udacity account
Development Environment Information:
Public IP Address - 34.215.252.142
Private Key - (not provided here)


Step 2: Follow the instructions provided to SSH into your server
mv ~/Downloads/udacity_key.rsa ~/.ssh/
chmod 600 ~/.ssh/udacity_key.rsa
ssh -i ~/.ssh/udacity_key.rsa ubuntu@34.215.252.142


Step 3: Create a new user named grader
1.  sudo adduser grader
    i.  To check the User(grader) information:
        sudo apt-get install finger
        finger grader
        (It will give you additional information(login , name , shell, directory, phone number etc) of User-grader)


Step 4: Give the grader the permission to sudo
1.  sudo visudo (edit the sudoers file . To save, use sudo visudo to edit the sudoers file otherwise file will not be saved)
    i.  add the below line of code after root ALL=(ALL:ALL) ALL grader ALL=(ALL:ALL) ALL and save it (ctrl-X , then Y and Enter)
    Your new user(grader) is able to execute commands with administrative privileges. ( for example - sudo anycommand)
    ii.  You can check the grader entry by entering the command: sudo cat /etc/sudoers


Step-5: Update all currently installed packages
1.  sudo apt-get update - command will update list of packages and their versions on your machine.
2.  sudo apt-get upgrade - command will install the packages
   i.  SELECT by highlighting "keep the local version currently installed" and hit ENTER


Step 6: Change the SSH port from 22 to 2200
1.  ubuntu@54.218.76.136:~$ sudo nano /etc/ssh/sshd_config 
    and enter Yes to confirm editing by root
    i.   Change port from 22 to 2200
    ii.  Change "PermitRootLogin prohibit-password" to "PermitRootLogin no". It will disable root login.
    iii. Change "PasswordAuthentication no" to "PasswordAuthentication yes".
    iv.  Add "AllowUsers grader" at end of the file so that we will login through grader and save it (ctrl-X , then Y and Enter)
    v.   Confirm editing was saved by checking the file with:
         sudo cat /etc/ssh/sshd_config
2.  Restart the SSH service : sudo service ssh restart

Step 7: SSH- Keys
1.  generate key-pair with "ssh-keygen"

2.  Save keygen file into (/home/vagrant/.ssh/linux_server_config) and fill in the password. 2 keys will be generated, public key (linux_server_config.pub) and identification key(linux_server_config).

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
    
8.  ssh grader@54.218.76.136 -p 2200 -i ~/.ssh/linux_server_config in new terminal .A pop-up window will open for authentication. 
    i.  enter created password for grader
    

    here
 







