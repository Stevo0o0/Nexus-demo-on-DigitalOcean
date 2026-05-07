# Nexus-demo-on-DigitalOcean
This project demonstrates how to run Nexus on a DigitalOcean server and publish an artifact to Nexus.

## Overview
This project shows the fundamental steps for deploying an application to cloud infrastructure:

* Set up and configure a virtual server (Droplet)
* Accessing the droplet securely using the best practices
* Configuring a Linux environment
* Building and running a Java application using Gradle
* Deploying the application on the droplet

Note: The cloud server used for this project has been destroyed, and this repo only contains the project documentation

## Tech Stack
* Cloud Provider: DigitalOcean
* OS: Ubuntu (Linux)
* Language: Java
* Build Tool: Gradle
* Access Method: SSH

## Application Source Code
The application was built from this repository:
https://gitlab.com/twn-devops-bootcamp/latest/05-cloud/java-react-example

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
   * Add your SSH key (generate one with `ssh-keygen` if needed)
   * Copy your public IP address

2. Connect via SSH
   `ssh root@your_server_ip`

3. Create a non-root user (Security best practices)
`adduser myuser`  (or any username of your choice)
`usermod -aG sudo myuser`    (This command gives your new user sudo privileges)

4. Switch to the non-root user
`su - myuser`

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
