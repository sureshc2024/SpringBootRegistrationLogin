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

git clone https://github.com/your-username/springboot-mysql-docker-network.git
cd springboot-mysql-docker-network

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

6. Dockerize the Spring Boot Application

Create a Dockerfile for the Spring Boot application in the project root directory:

# Start with a base image containing Java runtime
FROM openjdk:12
# working directory
WORKDIR /app
# Add the application's jar to the container
COPY target/SpringBootRegistrationLogin-1.0.jar .
# Exposing container on port 8080
EXPOSE 8080
# Run the jar file
ENTRYPOINT ["java","-jar","/app/SpringBootRegistrationLogin-1.0.jar"]


7. Build the Docker Image for Spring Boot

Build the Docker image for the Spring Boot application:

docker build -t web .

8. Run the Spring Boot Application in a Docker Container

Now, run the Spring Boot application in a Docker container, attaching it to the custom Docker network:

docker run --name webapp --network myapp -p 8080:8080 -d web

This command will:

Run the Spring Boot application in a container named webapp.

Attach it to the springboot-network to communicate with the MySQL container.

Expose the application on port 8080.


9. Access the Application

The application will be available at http://localhost:8080.

<img width="953" alt="image" src="https://github.com/user-attachments/assets/b91b5a62-f0d7-404e-b113-e74b443dc3e5">




10. API Endpoints

Get all users:

curl http://localhost:8080/api/users

Create a new user:

curl -X POST http://localhost:8080/api/users -H "Content-Type: application/json" -d '{"name": "John Doe", "email": "john@example.com"}'


11. Stop and Remove Containers

To stop and remove the containers, run:

docker stop springboot-container mysql-container
docker rm springboot-container mysql-container

12. Remove the Custom Docker Network

To remove the custom Docker network, run:

docker network rm springboot-network

Summary of Docker Commands

Here’s a summary of the Docker commands you will use in this project:

Create a custom Docker network:

docker network create springboot-network

Run MySQL:

docker run --name mysql-container --network springboot-network -e MYSQL_ROOT_PASSWORD=root -e MYSQL_DATABASE=springbootdb -p 3306:3306 -d mysql:8.0

Build the Spring Boot Docker image:

docker build -t springboot-app .

Run the Spring Boot application:

docker run --name springboot-container --network springboot-network -p 8080:8080 -d springboot-app

Stop and remove containers:

docker stop springboot-container mysql-container
docker rm springboot-container mysql-container

Remove the Docker network:

docker network rm myapp
