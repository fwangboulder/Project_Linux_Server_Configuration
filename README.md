# Project_Linux_Server_Configuration
Project 7 Udacity Full Stack Developer Nanodegree

In this project, I installed and configured a web and database server and host my
web application alumni. This README file will give a step-by-step how to access,
secure and perform the initial configuration of a bare-bones Linux server.

I have deployed my Item Catalog web applications to a publicly accessible
server and properly secured my application to ensures my application remains
stable and that my user’s data is safe.

Public IP address: 54.210.72.218

SSH port: 2200

Application URL: http://ec2-54-210-72-218.compute-1.amazonaws.com/

**What I have Done?**

1. Start a new Ubuntu Linux server instance on Amazon Lightsail (Section I).
2. Configure the timezone to UTC (Section I).
3. Update all currently installed packages (Section II).
4. Create an SSH key pair for Ubuntu using the ssh-keygen tool and check user information (Setction III).
5. Create a new user account named grader and set the SSH key pair (Section IV).
6. Give grader the permission to sudo (Section V).
7. Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80) (Section VI).
8. Change the SSH port from 22 to 2200. Make sure to configure the Lightsail firewall to allow it (Section VI).
9. Install and configure Apache to serve a Python mod_wsgi application (Section VII).
10. Clone and setup Item Catalog project from the Github repository created earlier in this Nanodegree program. Set it up in my server so that it functions correctly when visiting my server’s IP address in a browser (Section VIII): Configure apache, install and configure PostgreSQL, update google sign-in and facebook sign-in URIs.


##Section I: Get the Server

  1. Log in!  Create an Amazon Web Services account if you do not have one.

  2. Create an instance.

  3. Choose an instance image (OS only): Ubuntu

  4. Choose your instance plan.

  5. Give your instance a hostname(this is flexible): alumni

  6. Wait for it to start up and then connect using SSH.

  7. Record your public IP address: Public IP address 54.210.72.218

  8. $sudo dpkg-reconfigure tzdata

      Select:

      None of above

      UTC

      Current default time zone: 'Etc/UTC'

      Local time is now:      Fri Mar  3 02:52:36 UTC 2017.

      Universal Time is now:  Fri Mar  3 02:52:36 UTC 2017.

##Section II: Update Software

  1. $sudo apt-get update

  2. $sudo apt-get upgrade


##Section III: Key generation


  1. Local terminal

    $ssh-keygen

    The key was created in a default location /Users/panpan/.ssh/

    $cat /Users/panpan/.ssh/id_rsa.pub

    copy the content

  2. Remote terminal connected through the web account.

    $vi /home/ubuntu/.ssh/authorized_keys

    Paste the content of id_rsa.pub into authorized_keys.

    Double check id_rsa.pub and authorized have exactly the same content.

    If you failed in step 3 with timeout or other error, you might have a not matching
    key.

  3. Back to local terminal:

    Login ubuntu from local terminal:

    $ ssh ubuntu@54.210.72.218

    Message show up:
    The authenticity of host '54.210.72.218 (54.210.72.218)' can't be established.
    ECDSA key fingerprint is SHA256:......
    Are you sure you want to continue connecting (yes/no)?

    type yes. Now you log in from local terminal to remote terminal using ubuntu.

    **If failed, go back to step 1 and 2. You must have a mistake there!!!**

  4. $sudo chmod 700 ~/.ssh

  5. $sudo chmod 644 ~/.ssh/authorized_keys


    Now I will install Finger to check my user information easily.

  6. $sudo apt-get install finger

  7. $finger ubuntu

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
  8. $finger root

      ```
      Login: root           			Name: root
      Directory: /root                    	Shell: /bin/bash
      Never logged in.
      No mail.
      No Plan.
      ```

  9. $finger grader

      finger: grader: no such user.

##Section IV: Create a new user grader

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
    What happened? The publickey has not been set right.

    Check the key generation section. copy content of id_rsa.pub again and paste it for user grader as followings:

      ```

      a. log in as ubuntu again: $ssh ubuntu@54.210.72.218

      b. $sudo mkdir /home/grader/.ssh

      c. $sudo vi /home/grader/.ssh/authorized_keys

          Paste the content of id_rsa.pub into this file.

        Since We might want to log in as root, I will set this key to root too.

      d. $sudo vi /root/.ssh/authorized_keys

          Paste the content of id_rsa.pub for user root.


      e. $exit

      f. Log in as root:

          $ssh root@54.210.72.218


      g. log in as grader: $ssh grader@54.210.72.218

      h. Log out and try log in as grader:

          $exit

          $ssh grader@54.210.72.218

      Note: if you followed my instructions and still cannot log in for the users, probably
      you have a mistake for the key file. Go back to check whether your authorized_keys file
      has exactly the same content as that of id_rsa.pub.

      ```

##Section V: Give grader the permission to sudo

  1. Log in as grader from local terminal: $ssh grader@54.210.72.218

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

       $ exit

       $ssh grader@54.210.72.218

       $sudo chmod 700 ~/.ssh

       $sudo chmod 644 ~/.ssh/authorized_keys

##Section VI: UFW configuration and change port to 2200

Warning: When changing the SSH port, make sure that the firewall is open for port 2200 first, so that you don't lock yourself out of the server.
Give grader access. The grader needs to be able to log in to your server.
Configure the Uncomplicated Firewall (UFW) to only allow incoming connections
for SSH (port 2200, default 22), HTTP (port 80), www.

 ```
      $sudo ufw status
      $sudo ufw allow ssh
      $sudo ufw allow www
      $sudo ufw allow 2200/tcp
      $sudo ufw allow 22/tcp
      $sudo ufw enable
      $sudo ufw status


      Status: active
 ```
Result:

 ```
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

      ```

      $sudo vi /etc/ssh/sshd_config

        change port 22 to port 2200

      $sudo service ssh restart

        check if the port has been changed successfully.

      $ exit

      $ssh grader@54.210.72.218

        Default port 22 will not work anymore for log in

        ssh: connect to host 54.210.72.218 port 22: Connection refused

      $ssh grader@54.210.72.218 -p 2200

        This command gives a time out error because you did not set your account firewall

        Log in amazon lightsail acount and set the networking: Add a custome TCP 2200

      $ssh grader@54.210.72.218 -p 2200

        It works now!!!

      $sudo ufw status

        Delete the allow port 22.

      $sudo ufw delete allow 22/tcp

      Reference:
      https://www.digitalocean.com/community/tutorials/how-to-setup-a-firewall-with-ufw-on-an-ubuntu-and-debian-cloud-server

      You should have the following display after you run $sudo ufw status, delete extra permission.


      To                         Action      From
      --                         ------      ----
      2200/tcp                   ALLOW       Anywhere
      80/tcp                     ALLOW       Anywhere
      2200/tcp (v6)              ALLOW       Anywhere (v6)
      80/tcp (v6)                ALLOW       Anywhere (v6)


      ```

##Section VII: Apache

**A. Install Apache**

    ```
      $sudo apt-get install apache2

      $ls /var/www/html

      check url: 54.210.72.218

      you will see the apache default index.html content

    ```
**B. Configure Apache**

  1. Configure Apache to hand-off certain requests to an application handler - mod_wsgi

      Install mod_wsgi



      $sudo apt-get install libapache2-mod-wsgi

      You then need to configure Apache to handle requests using the WSGI module.
      You’ll do this by editing the /etc/apache2/sites-enabled/000-default.conf file.
      This file tells Apache how to respond to requests, where to find the files for a particular site and much more.

      $sudo vi /etc/apache2/sites-enabled/000-default.conf

      Add the following line at the end of the <VirtualHost * : 80> block, right before the closing </VirtualHost> line:
      WSGIScriptAlias / /var/www/html/myapp.wsgi

  2. Restart Apache:

    $sudo apache2ctl restart

    Check URL: 54.210.72.218  No Found

  3. What happened?!!

    You just defined the name of the file you need to write within your Apache
    configuration by using the WSGIScriptAlias directive. Despite having the
    extension .wsgi, these are just Python applications.
    Create the /var/www/html/myapp.wsgi file:

    $sudo vi /var/www/html/myapp.wsgi. Within this file, write the following application:

      ```
        def application(environ, start_response):
          status = '200 OK'
          output = 'Hello World!'

          response_headers = [('Content-type', 'text/plain'), ('Content-Length', str(len(output)))]
          start_response(status, response_headers)

          return [output]
      ```

    Check URL: 54.210.72.218

    Hello World!   This means it works.

##Section VIII: Deploy Flask Application in Ubuntu

Reference:
    https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps



**A. Install Git**

  $sudo apt-get install git

  Configure git:

   ```
      git config --global user.name <Your User Name>
      git config --global user.email <youruseremail@domain.com>
      git config --list

   ```

**B. Git Clone Project Item Catalog to this server**

  $sudo mkdir /var/www/catalog

  $cd /var/www/catalog

  git clone the project and name the folder as catalog:

  $sudo git clone https://github.com/fwangboulder/Project_Item_Catalog.git catalog

  $ls


**C. Install Flask**


  1. $sudo apt-get install python-pip

  2. Install virtuall Environment:

    $sudo pip install virtualenv

  3. Create a new virtual environment named virtualenv:

    $sudo virtualenv virtualenv

  4. Activate the virtual environment:

    $source virtualenv/bin/activate

  5. Install Flask:

    $sudo pip install Flask

  6. Install other dependencies related to the projects:

    $sudo pip install httplib2 oauth2client sqlalchemy psycopg2 sqlalchemy_utils requests

    $cd catalog

    $sudo cp project.py __init__.py

    $python __init__.py

      ```
        (virtualenv) grader@ip-172-26-6-138:/var/www/catalog/catalog$ sudo python __init__.py
          university
          * Running on http://0.0.0.0:9000/ (Press CTRL+C to quit)
          * Restarting with stat
          university
          * Debugger is active!
          * Debugger pin code: 119-102-102
      ```
  7. Deactivate the virtual environment:

    $deactivate

**D. Config and Enable a New Virtual Host**

  1. $sudo vi /etc/apache2/sites-available/catalog.conf

    Paste:

     ```

     <VirtualHost *: 80>
        ServerName 54.210.72.218
        ServerAlias ec2-54-210-72-218.compute-1.amazonaws.com
        ServerAdmin admin@54.210.72.218
        WSGIDaemonProcess catalog python-path=/var/www/catalog:/var/www/catalog/virtualenv/lib/python2.7/site-packages
        WSGIProcessGroup catalog
        WSGIScriptAlias / /var/www/catalog/catalog.wsgi
        <Directory /var/www/catalog/catalog/>
          Order allow,deny
          Allow from all
        </Directory>
        Alias /static /var/www/catalog/catalog/static
        <Directory /var/www/catalog/catalog/static/>
          Order allow,deny
          Allow from all
        </Directory>
        ErrorLog ${APACHE_LOG_DIR}/error.log
        LogLevel warn
        CustomLog ${APACHE_LOG_DIR}/access.log combined
      </VirtualHost>

      ```
  2. $sudo a2ensite catalog

      This is to enable the virtual host


  3. Correct the file paths of client_secrets.json and fbclientsecrets.json in __init__.py

        $sudo vi __init__.py

        Change all client_secrets.json to /var/www/catalog/catalog/client_secrets.json

        Change all fbclientsecrets.json to /var/www/catalog/catalog/fbclientsecrets.json


**E. Create catalog.wsgi File**

  1. $sudo vi /var/www/catalog/catalog.wsgi

    Paste the following content into it:

    ```
      import sys
      import logging
      logging.basicConfig(stream=sys.stderr)
      sys.path.insert(0, "/var/www/catalog/")

      from catalog import app as application
      application.secret_key = 'super_secret_key'

    ```

**F. Install and configure PostgreSQL**

  Reference:
     https://github.com/rrjoson/udacity-linux-server-configuration
     https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-16-04

  1. $sudo apt-get install libpq-dev python-dev

  2. $sudo apt-get install postgresql postgresql-contrib

  3. $sudo su - postgres

    ```
    psql
    CREATE USER catalog WITH PASSWORD 'password';
    ALTER USER catalog CREATEDB;
    CREATE DATABASE catalog WITH OWNER catalog;
    \c catalog
    REVOKE ALL ON SCHEMA public FROM public;
    GRANT ALL ON SCHEMA public TO catalog;
    \q
    ```

  4. Change engine in __init__.py, database_setup.py and alumni.py to :

        engine = create_engine('postgresql://catalog:password@localhost/catalog')

  5. $python /var/www/catalog/catalog/database_setup.py

       $python /var/www/catalog/catalog/alumni.py

  6. Make sure no remote connections to the database are allowed.

      $sudo nano /etc/postgresql/9.5/main/pg_hba.conf

      ```
      You will see the file looks like:
      local   all             postgres                                peer
      local   all             all                                     peer
      host    all             all             127.0.0.1/32            md5
      host    all             all             ::1/128                 md5

      ```

      Check URL: http://54.210.72.218

      It works great!!!

**G. Fix third party login problem**

  1. Google Sign In:

  Go to Google API console: https://accounts.google.com/ServiceLogin?service=cloudconsole&ltmpl=api&osid=1&passive=true&continue=https://console.developers.google.com/

  Add JavaScript origins and redirect URIs:

  Authorized JavaScript origins:

    http://localhost:9000

    http://54.210.72.218

    http://ec2-54-210-72-218.compute-1.amazonaws.com

  Authorized redirect URIs:

    http://localhost:9000/university

    http://ec2-54-210-72-218.compute-1.amazonaws.com/university/oauth2callback

  Changed the client_serects.json file to make sure add the extra URIs. This
  is super important!!!

  Restart apache server and enabled virtual host:

    $sudo service apache2 restart

    $sudo a2ensite catalog

  Visit http://ec2-54-210-72-218.compute-1.amazonaws.com/

  If not working:

    $sudo tail -f /var/log/apache2/error.log

    check the error. one possible reason is that  you forget the step 3 of
    "Config and Enable a New Virtual Host" part of this Section.

  2. Facebook sign in:

  Log in Facebook:
    https://www.facebook.com/login.php?next=https%3A%2F%2Fdevelopers.facebook.com%2Fapps

  Find your app and on the left panel of the web page, you can see add product option.
  Click +Add Product;
  Then click GetStarted of Facebook Login

  In Valid OAuth redirect URIs, add the following:

    http://ec2-54-210-72-218.compute-1.amazonaws.com

    http://54.210.72.218


  Restart apache server and enabled virtual host:

    $sudo service apache2 restart

    $sudo a2ensite catalog

    Visit http://ec2-54-210-72-218.compute-1.amazonaws.com/

  If not working:

    $sudo tail -f /var/log/apache2/error.log

    check the error. one possible reason is that  you forget the step 3 of
    "D. Config and Enable a New Virtual Host" part of this Section.

**An awesome free book The Linux® Command Line as a cheatsheet.*
It is [here](http://linuxcommand.org/tlcl.php).
