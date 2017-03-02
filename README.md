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
