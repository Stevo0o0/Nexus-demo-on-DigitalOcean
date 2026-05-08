# Nexus-demo-on-DigitalOcean
This project demonstrates how to install and configure a Nexus Repository Manager on a DigitalOcean Droplet and publish Java artifacts using both Gradle and Maven.

## Overview
This project covers:

* Provisioning a DigitalOcean Ubuntu server
* Installing and configuring Nexus Repository Manager
* Creating Nexus users and roles
* Publishing Java artifacts with Gradle
* Publishing Java artifacts with Maven
* Managing Maven snapshot repositories

Note: The DigitalOcean server used for this demo has been destroyed, and this repo only contains the project documentation

## Tech Stack
* Nexus Repository Manager
* DigitalOcean
* Ubuntu Linux
* Java 17
* Gradle
* Maven
* IntelliJ IDEA
* Git Bash


## Application Source Code
The sample applications used in this project were built from:
https://gitlab.com/twn-devops-bootcamp/latest/06-nexus/java-app.git
https://gitlab.com/twn-devops-bootcamp/latest/06-nexus/java-maven-app.git

## Architecture
The project follows this simple architecture:

Local Machine
      │
      │ SSH
      ▼
DigitalOcean Droplet (Ubuntu)
      │
      │ Java Runtime
      ▼
Nexus Repository Manager
      │
      ├── Gradle Publish
      └── Maven Deploy

## Development Workflow

- Server provisioning and Nexus configuration were performed from Git Bash using SSH.
- Java projects were built and published using IntelliJ IDEA.
- Gradle and Maven were used to publish artifacts to the Nexus repository running on the DigitalOcean server.

## Deployment Guide
1. Create the DigitalOcean Droplet
   * Log in to DigitalOcean
   * Create a new Ubuntu Droplet
   * Select the Regular Disk plan
   * Choose a plan with at least 8 GB RAM
   * Add your SSH key
   * Copy the server IP address

2. Configure Firewall Rules
   Create firewall rules for:
   Port 22 for SSH access
   Port 8081 for Nexus Repository Manager

3. Connect to the server from Git Bash using SSH
   `ssh root@your_server_ip`

4. Install Java
   `apt update`
   `apt install openjdk-17-jre-headless -y`

   Verify installation:
   `java -version`

5. Download and install Nexus
   Move into the /opt directory:
   `cd /opt`

   Download Nexus:
   `wget https://download.sonatype.com/nexus/3/nexus-3.91.1-04-linux-x86_64.tar.gz`

   Extract the archive:
   `tar -zxvf nexus-3.91.1-04-linux-x86_64.tar.gz`

   This creates:
   * nexus-<version>/ (Nexus installation files)
   * sonatype-work/ (Nexus data and configuration)

6. Create a Dedicated Nexus User
   `adduser nexus`  (or any username of your choice)
   `usermod -aG sudo nexus`    (This command gives your new user sudo privileges)

7. Change ownership of the nexus folders so the new nexus user can access them
   `chown -R nexus:nexus nexus-3.91.1-04`
   `chown -R nexus:nexus sonatype-work`

8. Configure Nexus to Run as Non-Root User
   `vim nexus-3.91.1-04/bin/nexus.rc`
   Add:
   `run_as_user="nexus"`
   Save and exit.

   
9. Start Nexus
   Switch users:
   `su - nexus`
   Start Nexus:
   `/opt/nexus-3.91.1-04/bin/nexus start`
   Verify the process:
   `ps aux | grep nexus` (You will see the nexus process running)

10. Access Nexus in the Browser
    Open:
    `http://your_server_ip:8081`

11. Retrieve the Admin Password and Login
    `cat /opt/sonatype-work/nexus3/admin.password`
    Login credentials:
    Username = admin
    Password = contents of admin.password

    You will be prompted to change your password after you login


## Nexus Configuration

1. CREATE A ROLE
   Navigate to:
   Settings → Security → Roles

   Create a new role and set:
   Role ID = nx-java
   Role Name = nx-java
   
   Assign privilege:
   nx-repository-view-maven2-*-*

   Click confirm and save.

2. CREATE A NEXUS USER
   Navigate to:
   Settings → Security → Users
   
   Create a user and assign the "nx-java" role.

## Publish with Gradle
1. Clone the Gradle Project
    Open IntelliJ IDEA and create a project using the repository::
     `https://gitlab.com/twn-devops-bootcamp/latest/06-nexus/java-app.git`

2. Configure Repository URL
   Inside build.gradle, update the repository URL to this:
   `url "http://your_server_ip:8081/repository/maven-snapshots/"`
    
3. Configure Credentials
   Inside gradle.properties:
   repoUser=your_username
   repoPassword=your_password

4. Build the Application
   In the terminal of IntelliJ:
   `gradle build`

5. Publish the Application
   `gradle publish`

   Verify the artifact under:
   Browse → maven-snapshots

## Publish with Maven
1. Clone the Maven Project
   Create a new IntelliJ project using this repository:
   `https://gitlab.com/twn-devops-bootcamp/latest/06-nexus/java-maven-app.git`
2. Configure Nexus Repository
   Update the pom.xml repository URL to this:
   <url>http://your_server_ip:8081/repository/maven-snapshots/</url>

3. Configure Maven Credentials
   Edit:
   ~/.m2/settings.xml

   Add:
   `<settings>
      <servers>
        <server>
          <id>nexus-snapshots</id>
          <username>username</username>
          <password>xxxxxx</password>
        </server>
      </servers>
    </settings>`

4. Build the Application
   In the IntelliJ terminal:
   `mvn package`

 5. Deploy the Artifact
    `mvn deploy`
 
    Verify the artifact in Nexus under:
    Browse → maven-snapshots


## Key Learnings

* Provisioning cloud infrastructure on DigitalOcean
* Linux server administration
* Installing and configuring Nexus Repository Manager
* Managing repository permissions and users
* Publishing Java artifacts using Gradle and Maven
* Working with Maven snapshot repositories
