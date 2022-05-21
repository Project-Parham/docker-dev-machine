# Dev Machine Instructions
## Install Docker and Docker compose
[Windows](https://docs.docker.com/desktop/windows/install/)  
[Mac](https://docs.docker.com/desktop/mac/install/)  
[Linux](https://computingforgeeks.com/how-to-install-docker-on-ubuntu/)  
  
[Docker Compose](https://docs.docker.com/compose/install/)

[Things to do after installing docker on linux](https://docs.docker.com/engine/install/linux-postinstall/)  

Do the the following steps from the post install steps on linux

 - Manage Docker as a non-root user
 - Configure Docker to start on boot

## Create and configure .env file
1. Make a copy of the .env.example and call it .env
2. Set your group and user id you can find your userid and groupid by doing the following in terminal

```sh
$ whoami
curtis
$ id curtis
uid=1000(curtis) gid=1000(curtis)
```

 - copy uid into the .env file for WWWUSER  
 - copy gid into the .env file for WWWGROUP

3. Configure your ports  
 - Your application will run on localhost if you would like to run multiple applications as once you will need to set a different port for each application. Set APP_PORT=8081 or your desired port for the application  
 - Phpmyadmin will run by default on localhost:8080 if you would like it to run on another port set it with PMA_PORT option in the .env file. Please note that phpmyadmin must run on a different port than your application.

 4. Set your desired php and mysql versions.
  - PHP_VERSION can be either 7.4 | 8.1
  - MYSQL can be any version you would like, it defaults to latest

  5. Set your database credentials with 
   - DB_NAME this will be the name of the database for your application
   - DB_USER this is the user for your database
   - DB_PASSWORD this will be the password for your database user
   - leave DB_HOST set to mysql docker will handle routing of the dynamic host based on the container name.

   ## Configure Apache

   You can update the apache configuration files in /dev/php-7.4/000-default.conf or /dev/php-8.1/000-default.conf for php versions 7.4 and 8.1 respectively. If you do not configure these files it will use the default apache configuration files.

   ## Docker Compose UP
   Now it is time to bring our dev machine up. Open a terminal and navigate to the root of this directory

   ```sh
   $ docker-compose up -d
   ```
   This will take a while to run for the first time. Subsequent runs will boot up very quickly. If this is successful you should see three containers finish building at the end .  
    - mysql
    - pma 
    - web

  You can verify the containers are running by entering this command in the terminal  
  ```sh
  $ docker ps
  ```
  You should see three containers with running. Their names should be web, pma, and mysql

  ## Test it out 
  navigate to the src directory and create an index.php file 
  ```php 
  <?php 
    phpinfo();
  ?>
  ```
  Open a browser and navigate to http://localhost don't forget that if you changed the APP_PORT to navigate to http://localhost:80 replace :80 with your chosen port number.  
  If this is successful you should see the output from the index.php file

  Open a browser tab and navigate to http://localhost:8080 and you should see phpmyadmin. It should be autologged in and you should see your database has been created for you. Replace :8080 with your chosen PMA_PORT if you changed that value in the .env

  ## Add your project files. 
  
  Your source code for your project should go in the src directory. I recommend that you open that directory directly in your code editor of choice from now on so that you don't accidently mess something up.

  Be sure to git init inside of the src directory and not in the root of this directory that has your docker-compose.yml file. I recommend this be stored in a separate repo so that you can easily spin up new machines in the future.

  ## Shut down your dev machine
  ```sh 
  $ docker-compose down
  ``` 

  ## Start your dev machine 
  ```sh 
  $ docker-compose up -d 
  ``` 

  ## ssh into your container
  sometimes you will need to ssh into your container follow these instructions to do that.

  ```sh 
  $ docker ps
  ``` 
  You will see a list of your running containers. 
  Copy the Container ID or Name of the container you want to ssh into

  ```sh 
  docker exec -it <CONTAINER_ID> bash
  ``` 
  replace <CONTAINER_ID> with the container id that you copied in the previous container. 

  ```sh 
  dev@cd028a95ac08:/var/www/html$ pwd
  /var/www/html
  ```
  When you are done doing your work in the container you can exit the ssh
  ```sh 
  dev@cd028a95ac08:/var/www/html$ exit
  goodbye
  ``` 