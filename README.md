# Project_Linux_Server_Configuration
Project 7 Udacity Full Stack Developer Nanodegree

Make baseline installation of a Linux distribution on a virtual machine and
prepare it to host my web applications.



**Amazon Lightsail Start**

 1. Log in!  Create an Amazon Web Services account if you do not have one.

 2. Create an instance.

 3. Choose an instance image (OS only): Ubuntu

 4. Choose your instance plan.

 5. Give your instance a hostname(this is flexible): alumni

 6. Wait for it to start up and then connect using SSH.

 7. Record your public IP address: Public IP address 54.210.72.218

**Key generation**


 1. Local terminal

    $ssh-keygen

    The key was created in a default location /Users/panpan/.ssh/

    $cat /Users/panpan/.ssh/id_rsa.pub

    copy the content

 2. remote terminal connected through the account.

    $vi /home/ubuntu/.ssh/authorized_keys

    paste the content of id_rsa.pub into authorized_keys. Make sure you do not have
    extra space for the content. Double check id_rsa.pub and authorized have exactly the same content.

 3. Go back to local terminal:

    $ ssh ubuntu@54.210.72.218

    Message show up:
    The authenticity of host '54.210.72.218 (54.210.72.218)' can't be established.
    ECDSA key fingerprint is SHA256:......
    Are you sure you want to continue connecting (yes/no)?

    type yes. Now you log in from local terminal to remote terminal using ubuntu.

**Update Software**

  1. $sudo apt-get update

  2. $sudo apt-get upgrade
**Install Finger**
  1. $sudo apt-get install finger

  2. $finger ubuntu

      Display the followings information about user ubuntu.

        ```
          Login: ubuntu         			Name: Ubuntu
          Directory: /home/ubuntu     Shell: /bin/bash
          On since Thu Mar  2 18:10 (UTC) on pts/0 from 72.21.217.69
          24 minutes 55 seconds idle
          On since Thu Mar  2 18:10 (UTC) on pts/1 from 72.21.217.143
          23 minutes 48 seconds idle
          On since Thu Mar  2 18:13 (UTC) on pts/2 from 209.129.244.192
          4 seconds idle
          No mail.
            No Plan.
            ```
  3. $finger root

      ```
      Login: root           			Name: root
      Directory: /root                    	Shell: /bin/bash
      Never logged in.
      No mail.
      No Plan.
      ```

  4. $finger grader

      finger: grader: no such user.

**Create a new user grader**

      If you exit the remote terminal, log in as ubuntu.

      $ssh ubuntu@54.210.72.218

  1. $sudo adduser grader

   ```
    Adding user `grader' ...
    Adding new group `grader' (1001) ...
    Adding new user `grader' (1001) with group `grader' ...
    Creating home directory `/home/grader' ...
    Copying files from `/etc/skel' ...
    Enter new UNIX password:
    Retype new UNIX password:
    passwd: password updated successfully
    Changing the user information for grader
    Enter the new value, or press ENTER for the default
  	Full Name []: grader
  	Room Number []:
  	Work Phone []:
  	Home Phone []:
  	Other []:
    Is the information correct? [Y/n] Y

    Note: passwd cannot be empty!

    ```

  2. $finger grader

  ```
  Login: grader         			Name: grader
  Directory: /home/grader             	Shell: /bin/bash
  Never logged in.
  No mail.
  No Plan.

  This means the grader user has been created.

  ```

  3. $exit

  ```
    logout
    Connection to 54.210.72.218 closed.

  ```

  4. $ssh grader@54.210.72.218

    ```
    The authenticity of host '54.210.218 (54.210.0.218)' can't be established.
    ECDSA key fingerprint is SHA256:.......
    Are you sure you want to continue connecting (yes/no)? yes
    Warning: Permanently added '54.210.218,54.210.0.218' (ECDSA) to the list of known hosts.
    Permission denied (publickey).
    ```
    The publickey has not been set right.

    check the key generation section. copy content of id_rsa.pub again and paste

    it for user grader as followings:

      a. log in as ubuntu again: $ssh ubuntu@54.210.72.218

      b. $sudo mkdir /home/grader/.ssh

      c. $sudo vi /home/grader/.ssh/authorized_keys

          Paste the content of id_rsa.pub into this file. Double check there are no
          missiong characters or extra spaces.

        Since We might want to log in as root, I will set this key to root too.

      d. $sudo vi /root/.ssh/authorized_keys

          Paste the content of id_rsa.pub for user root.

      e. $exit

      f. log in as grader: $ssh grader@54.210.72.218

      g. log out and try log in as root:

          $exit

          $ssh root@54.210.72.218

      Note: if you followed my instructions and still cannot log in for the users, probably
      you have a mistake for the key file. Go back to check whether your authorized_keys file
      has exactly the same content as that of id_rsa.pub.

**Give grader the permission to sudo**

    1. Log in as grader: $ssh grader@54.210.72.218

    2. $sudo ls /etc/sudoers.d

        [sudo] password for grader:
        grader is not in the sudoers file.  This incident will be reported.

        This means grader is not in the sudo list.
    3. $exit
       $ssh root@54.210.72.218
       $sudo vi /etc/sudoers.d/grader
       Add:
       grader ALL=(ALL) NOPASSWD:ALL

       Now the grader user can use sudo.

**UFW configuration**

Configure the Uncomplicated Firewall (UFW) to only allow incoming connections
for SSH (port 2200, default 22), HTTP (port 80), www.
    $sudo ufw status
        interactive
    $sudo ufw allow ssh
    $sudo ufw allow www
    $sudo ufw allow 2200/tcp
    $sudo ufw allow 22/tcp
    $sudo ufw enable
    $sudo ufw status

    $ sudo  ufw status

```
      Status: active

To                         Action      From
--                         ------      ----
22                         ALLOW       Anywhere
2200/tcp                   ALLOW       Anywhere
22/tcp                     ALLOW       Anywhere
80/tcp                     ALLOW       Anywhere
22 (v6)                    ALLOW       Anywhere (v6)
2200/tcp (v6)              ALLOW       Anywhere (v6)
22/tcp (v6)                ALLOW       Anywhere (v6)
80/tcp (v6)                ALLOW       Anywhere (v6)

```

**Change port from default 22 to 2200**

  $sudo vi /etc/ssh/sshd_config

    change port 22 to port 2200

  $sudo service ssh restart

  check if the port has been changed successfully.

  $ exit

  $ssh grader@54.210.72.218

    using default port 22 will not work anymore

  ssh: connect to host 54.210.72.218 port 22: Connection refused

  $ssh grader@54.210.72.218 -p 2200

    This command gives a time out error because you did not set your account firewall

    Go to your amazon lightsail and set the networking. Add a custome TCP 2200

   then run $ssh grader@54.210.72.218 -p 2200

   It works now!!!

**Install Apache**

  $sudo apt-get install apache2

  $ls /var/www/html

   check url: 54.210.72.218

   you will see the apache default index.html content

**Configure Apache**

    Configure Apache to hand-off certain requests to an application handler - mod_wsgi

    **install mod_wsgi**

    $sudo apt-get install libapache2-mod-wsgi

    You then need to configure Apache to handle requests using the WSGI module.
    You’ll do this by editing the /etc/apache2/sites-enabled/000-default.conf file.
    This file tells Apache how to respond to requests, where to find the files
    for a particular site and much more.

    $sudo vi /etc/apache2/sites-enabled/000-default.conf

    add the following line at the end of the <VirtualHost * : 80> block, right
    before the closing </VirtualHost> line:
    WSGIScriptAlias / /var/www/html/myapp.wsgi

    restart Apache: $sudo apache2ctl restart

    Check URL: 54.210.72.218  No Found Do not feel panic!! Continue~~~

    You just defined the name of the file you need to write within your Apache
    configuration by using the WSGIScriptAlias directive. Despite having the
     extension .wsgi, these are just Python applications.
     Create the /var/www/html/myapp.wsgi file using the command
     sudo vi /var/www/html/myapp.wsgi. Within this file, write the following application:

     ```
     def application(environ, start_response):
          status = '200 OK'
          output = 'Hello World!'

          response_headers = [('Content-type', 'text/plain'), ('Content-Length', str(len(output)))]
    start_response(status, response_headers)

          return [output]
    ```

    Check URL: 54.210.72.218

    You now see Hello World!

**Install postgresql**

    $sudo apt-get install postgresql

    Update /var/www/html/myapp.wsgi application so that it successfully connects
     to your database

     Switching Over to the postgres Account

     $sudo -i -u postgres

     Access a Postgres prompt:

     $psql

     Exit
     # \q

     Create new user:

     $createuser --interactive

     Enter name of role to add: test
     Shall the new role be a superuser? (y/n) y

     reference:
     https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-16-04
