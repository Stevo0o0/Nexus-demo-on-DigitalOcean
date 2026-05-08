# Nexus-demo-on-DigitalOcean
This project demonstrates how to run Nexus on a DigitalOcean server and publish an artifact to Nexus.

## Overview
This project shows the fundamental steps for publishing an artifact to Nexus, which will be running on a DigitalOcean server:

* Install and configure Nexus from scratch on a cloud server
* Create a new User on Nexus with relevant permissions
* Java Gradle Project: Build Jar & Upload to Nexus
* Java Maven Project: Build Jar & Upload to Nexus
* Publish the artifact on Nexus

Note: The cloud server used for this project has been destroyed, and this repo only contains the project documentation

## Tech Stack
* Nexus
* DigitalOcean
* Linux
* Java
* Gradle
* Maven


## Application Source Code
The application was built from this repository:
https://gitlab.com/twn-devops-bootcamp/latest/06-nexus/java-app.git

## Architecture
The project follows this simple architecture:

Local Machine
       │ 
       │ SSH 
       ▼ 
  DigitalOcean Droplet (Ubuntu Server) 
       │ 
       │ Java Runtime 
       ▼ 
  Java Application (Gradle Build)

## Deployment
1. Create the droplet
   * Log in to DigitalOcean
   * Create a new droplet (Ubuntu is recommended)ded)
   * Select the regular disk plan
   * Nexus uses a large storage capacity, and we need to have a lot of resources on the server so it doesn't run out of storage; select the plan that has 8 GB of RAM.
   * Add your SSH key (generate one with `ssh-keygen` if needed)
   * Copy your public IP address

2. Create a firewall and add an inbound rule that allows you to access the server using SSH.
   Set the type to SSH and add the IP address of your local machine. After this, connect the droplet you created to your firewall.

3. Connect via SSH
   `ssh root@your_server_ip`

4. Install Java (nexus requires version 8 or above) and move to the opt directory
   `apt install openjdk-17-jre-headless -y`
   `cd /opt`
   In the opt directory after doing `ls` you will find the Digital Ocean folder there because the opt directory is designed specifically for "optional" or add-on software packages that are not part of the core operating system, keeping them separate from standard system directories like /usr/bin or /etc

5. Install Nexus using the Linux link provided on the Sonatype website
   `wget https://download.sonatype.com/nexus/3/nexus-3.91.1-04-linux-x86_64.tar.gz`

6. Use `ls` to see the downloaded zip file then unzip the file
   `tar -zxvf nexus-3.91.1-04-linux-x86_64.tar.gz`
   After unzipping the file, you should get two new directories, nexus-<version>/ and sonatype-work/. The nexus-<version> directory is the application installation directory that contains the binaries, startup scripts, libraries, etc. While the sonatype-work directory is the data/work directory. It stores the repositories, configuration, logs, databases, blob storage, etc. You can also use this folder to back up all Nexus data.

7. Create a non-root user (Security best practices)
`adduser nexus`  (or any username of your choice)
`usermod -aG sudo nexus`    (This command gives your new user sudo privileges)

8. Change ownership of the nexus folders so the new nexus user can access them
   `chown -R nexus:nexus nexus-3.91.1-04`
   `chown -R nexus:nexus sonatype-work`

9. Set up the Nexus configuration so it will run with the nexus user
   `vim nexus-3.91.1-04/bin/nexus.rc`
   Type i to go into insert mode, then type `run_as_user="nexus"`
   save by pressing esc then typing `:wq`

   
10. Switch to the non-root user
    `su - nexus`

11. Start the Nexus service
    `/opt/nexus-3.91.1-04/bin/nexus start`
    You can use `ps aux | grep nexus` to see that the nexus process is running

12. Open the Nexus service on your browser
    Nexus runs on port 8081, and to use it, you need to add another inbound rule of custom type to your firewall that allows port 8081.
    To connect to the Nexus server on your browser, open a new tab and go `143.198.183.151:8081`

13. Log into Nexus
    Use admin as the username, a password was created by default for you to log in, and this is found in the /opt directory
    To get your admin password:
    `cat /opt/sonatype-work/nexus3/admin.password`
    After logging in, you will be prompted to change the password; you can set whatever you want.

14. Create a new role for your new user
    Before creating a new user, create the role the user will use. The best practice is to give users the least permissions necessary for their role
    Go to settings -> security -> roles
    Set the role ID and role name as nx-java
    Click on Applied Privileges, search "maven" and select "nx-repository-view-maven2-*-*". Click confirm and save.

15. Create a Nexus user: Go to settings -> security -> users
    Use your personal information to create the user
    For roles, select the role you already created, "nx-java".

16. Build Jar file using Gradle
    Using an IDE preferablly IntelliJ create a projetc using version comntrol and use this link:
     `https://gitlab.com/twn-devops-bootcamp/latest/06-nexus/java-app.git`
    In the project folder go into build.gradle, in line 30 where you have the url of your repository like this; url "http://xx.xx.xx.xx:xxxx/repository/maven-snapshots/", replace xx.xx.xx.xx:xxxx with your nexus ip: your nexus port, that is your digital ocean server ip:8081

    In the gradle.properties file set the repoUser and repoPassword to the one you created on the nexus site.
    To build the jar file, go into the terminal on the IDE and use `gradle build`

17. Upload Gradle Jar file to Nexus
    To upload the file use the comman: `gradle publish`.
    After it is succesfull. Go back to the nexus repository and go to browse, maven-snapshots and you will see the application there. You will see the jar file and some metatdat file hoch get genrated as additional information for jar file.

18. Build Jar file using Maven
    In IntelliJ create a new project using version control and use this link:
    `https://gitlab.com/twn-devops-bootcamp/latest/06-nexus/java-maven-app.git`

    In the project folder go to the pom.xml file, in line 49 replace the url with the url of maven-snapshots gotten form the repositories page on the nexus site.

cd .m2
    $ vim settings.xml

`<settings>
  <servers>
    <server>
     <id>nexus-snapshots</id>
     <username>username</username>
     <password>xxxxxx</password>
    </server>
  </servers>
</settings>`
 mvn package

 mvn deploy
6. Update system packages
`sudo apt update`

7. Install Java
`sudo apt install openjdk-17-jdk -y`
`java -version `  (This checks the Java version)

8. Clone the application repository
   * Using IntelliJ or an IDE of your choice (to fully view the repository), open its terminal and clone the application
`git clone <your-repo-link>`
`cd <repo-folder>`

9. Install Gradle
`sudo apt install gradle -y`
`gradle -v`

10. Build the Application
gradle build

11. Copy your application to your droplet server: 
`scp build/libs/java-react-example.jar root@your_server_ip:/root`

12. Deploy the application on the droplet server
The application would be running on port 7071. To access it from your droplet, you have to go to your firewall and add an inbound rule with a custom type and TCP protocol that allows port 7071.
To deploy the application from your droplet, go to a new tab on your browser and use your_server_ip:7071


## Key Learnings

* How to provision and access cloud infrastructure
* Basic Linux server configuration
* Secure user management on remote systems
