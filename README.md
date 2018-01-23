# linux-server-configuration
Udacity FSND project

Configuration Steps:
Step 1 : Launch your Virtual Machine with your Udacity account
Development Environment Information Details:-
Public IP Address - 54.213.90.68
Private Key - Can't be shared

Step-2: Follow the instructions provided to SSH into your server
mv ~/Downloads/udacity_key.rsa ~/.ssh/
chmod 600 ~/.ssh/udacity_key.rsa
ssh -i ~/.ssh/udacity_key.rsa ubuntu@54.213.90.68

Step-3 : Create a new user named grader
sudo adduser grader
To check the User(grader) information :
    sudo apt-get install finger
    finger grader
It will give you additional information(login , name , shell, directory, phone number etc) of User-grader

Step-4 : Give the grader the permission to sudo
sudo visudo (edit the sudoers file . To save, use sudo visudo to edit the sudoers file otherwise file will not be saved)
add the below line of code after root ALL=(ALL:ALL) ALL grader ALL=(ALL:ALL) ALL and save it (ctrl-X , then Y and Enter)
Your new user(grader) is able to execute commands with administrative privileges. ( for example - sudo anycommand)
You can check the grader entry by below command: sudo cat /etc/sudoers
Resources - Add User and give permissions

Step-5 : Update all currently installed packages
sudo apt-get update - command will update list of packages and their versions on your machine.
sudo apt-get upgrade - command will install the packages
Resources - update and upgrade the packages

Step-6 : Change the SSH port from 22 to 2200
ubuntu@54.213.90.68:~$ sudo nano /etc/ssh/sshd_config and enter Yes to confirm editing by root
change port from 22 to 2200
change PermitRootLogin prohibit-password to PermitRootLogin no . It will disable root login.
change PasswordAuthentication from no to yes.
add AllowUsers grader at end of the file so that we will login through grader and save it (ctrl-X , then Y and Enter)
restart the SSH service : sudo service ssh restart




