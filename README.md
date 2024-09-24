# SpringBootRegistrationLogin
This project demonstrates a Spring Boot REST API application connected to a MySQL database, both running in Docker containers with a custom Docker network to enable container-to-container communication.

Features

Spring Boot application with RESTful APIs.

MySQL database integration using Spring Data JPA.

Custom Docker network for communication between Spring Boot and MySQL containers.


Prerequisites

Make sure you have the following installed:

Docker

Maven (for building the application)


Getting Started

1. Clone the Repository

git clone [https://github.com/your-username/springboot-mysql-docker-network.git](https://github.com/sureshc2024/SpringBootRegistrationLogin.git)
cd SpringBootRegistrationLogin

2. Build the Spring Boot Application

First, package your Spring Boot application into a JAR file using Maven:

mvn install -DskipTests

This will generate a springboot-mysql-0.0.1-SNAPSHOT.jar in the target/ directory.

3. Create a Custom Docker Network

Create a custom Docker network for communication between the containers:

docker network create myapp

This network will allow the MySQL and Spring Boot containers to communicate with each other.

4. Run MySQL in a Docker Container

Run the MySQL container and attach it to the custom Docker network:

docker run --name mysqldb --network myapp -e MYSQL_ROOT_PASSWORD=root -p 3306:3306 -d mysqldb

This command will:

Run a MySQL container named mysql-container.

Set the root password to root.

Expose MySQL on port 3306.

Attach the container to the myapp network.


5. Update Application Configuration

Update the src/main/resources/application.properties file to point to the MySQL container in the custom Docker network:

spring.datasource.url=jdbc:mysql://mysqldb:3306/**
spring.datasource.username=root
spring.datasource.password=root

Instead of using localhost, the spring.datasource.url points to mysql-container, which is the container name on the custom Docker network.

Create a custom Docker network:

docker network create springboot-network

**6. Creat a Dockerfile for Mysql container in the database sub directory**.

#Start with a base image containing mysql
FROM mysql:latest
#Copy table.sql into the container for creating database
COPY table.sql /docker-entrypoint-initdb.d/
#Exposing  mysql container on port 8080
EXPOSE 3306

**Build the Docker image for the Spring Boot application:**

docker build -t mysqldb target/.

Run MySQL:

docker run --name mysqldb --network myapp -e MYSQL_ROOT_PASSWORD=root -e  -p 3306:3306 -d mysqldb


**7. Dockerize the Spring Boot Application**

Create a Dockerfile for the Spring Boot application in the project root directory:

#Start with a base image containing Java runtime
FROM openjdk:12
#working directory
WORKDIR /app
#Add the application's jar to the container
COPY target/SpringBootRegistrationLogin-1.0.jar .
#Exposing container on port 8080
EXPOSE 8080
#Run the jar file
ENTRYPOINT ["java","-jar","/app/SpringBootRegistrationLogin-1.0.jar"]


**8. Build the Docker Image for Spring Boot**

Build the Docker image for the Spring Boot application:

docker build -t web .

**9. Run the Spring Boot Application in a Docker Container**

Now, run the Spring Boot application in a Docker container, attaching it to the custom Docker network:

docker run --name webapp --network myapp -p 8080:8080 -d web

This command will:

Run the Spring Boot application in a container named webapp.

Attach it to the myapp network to communicate with the MySQL container.

Expose the application on port 8080.


**10. Access the Application**

The application will be available at http://localhost:8080.

The login page: http://localhost:8080

<img width="953" alt="image" src="https://github.com/user-attachments/assets/b91b5a62-f0d7-404e-b113-e74b443dc3e5">


Registration page: http://localhost:8080/register

<img width="955" alt="springbootappregisterpage" src="https://github.com/user-attachments/assets/4fefcb37-b772-4855-9b51-ba09b1c0d707">


Users page: http://localhost:8080/users

<img width="944" alt="springbootlistofuserspage" src="https://github.com/user-attachments/assets/7202cea6-cc3a-4417-927d-a19743baef93">



**11. Stop and Remove Containers**

To stop and remove the containers, run:

docker stop webapp mysqld
docker rm webapp mysqld

**12. Remove the Docker network:**

docker network rm myapp
