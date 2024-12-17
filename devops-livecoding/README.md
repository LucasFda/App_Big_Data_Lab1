# devops-livecoding

# GITHUB REPOSITORY LINK:
https://github.com/LucasFda/App_Of_Big_Data

# DevOps in Action: CI/CD Pipeline and Automated Deployment

This project, organized by **Takima**, focuses on sefocuses on setting CD pipeline using **GitHub Actions** and automating the deployment process with **Ansible**. The application consists of a **Spring Boot API** and its infrastructure, containerized using Docker and deployed on a remote server accessible via SSH.

## Table of Contents
- [Project Structure](#project-structure)
- [Technologies Used](#technologies-used)
- [Goals](#goals)
- [Setup and Configuration](#setup-and-configuration)
  - [CI with GitHub Actions](#ci-with-github-actions)
  - [Docker Images and DockerHub](#docker-images-and-dockerhub)
  - [Deployment with Ansible](#deployment-with-ansible)
- [How to Access the Application](#how-to-access-the-application)
- [Usage](#usage)
- [Results](#results)
- [Future Improvements](#future-improvements)

## Project Structure
```
.
├── .github
│   └── workflows
│       ├── test-backend.yml         # CI workflow for testing backend
│       └── build-and-push-docker-image.yml # Build and push Docker images
├── ansible
│   ├── inventories
│   │   └── setup.yml                # Inventory file with server configuration
│   ├── roles                       # Roles for deployment
│   │   ├── application
│   │   │   └── tasks
│   │   │       └── main.yml
│   │   ├── database
│   │   │   └── tasks
│   │   │       └── main.yml
│   │   ├── docker
│   │   │   └── tasks
│   │   │       └── main.yml
│   │   ├── network
│   │   │   └── tasks
│   │   │       └── main.yml
│   │   └── proxy
│   │       └── tasks
│   │           └── main.yml
│   └── playbook.yml                 # Main playbook for orchestrating roles
├── simple-api
│   ├── src                         # Source code for Spring Boot application
│   ├── pom.xml                     # Maven configuration
│   ├── Dockerfile                  # Docker configuration for the backend
│   └── docker-compose.yml          # Compose file for local testing
├── README.md                       # Project documentation
└── http-server                     # HTTP server for proxy
```

## Technologies Used
- **GitHub Actions**: CI/CD workflows to test, build, and push Docker images.
- **Docker**: Containerize the application and its services.
- **DockerHub**: Host Docker images for the backend and database.
- **Ansible**: Automate deployment on the remote server.
- **Maven**: Build and test the Spring Boot backend.
- **SonarCloud**: Analyze code quality and maintainability.
- **MySQL**: Database for the backend API.
- **NGINX**: Reverse proxy for the application.

## Goals
### Lab 1: CI with GitHub Actions
- Build and test the Spring Boot API automatically using Maven.
- Push Docker images for the backend and database to DockerHub.
- Ensure code quality with SonarCloud.

### Lab 2: Deployment with Ansible
- Automate the deployment of the application on a remote server using Ansible roles.
- Start and orchestrate the services:
  - **Backend API**
  - **MySQL Database**
  - **NGINX Proxy**
  - **Docker Network**
  - **Docker Installation**

## Setup and Configuration

### CI with GitHub Actions
#### Test Backend Workflow (`test-backend.yml`)
- Runs on branches `main` and `dev`.
- Steps include:
  - Checkout the code.
  - Set up JDK 17.
  - Build and test with Maven:
    ```yaml
    mvn -B verify sonar:sonar -Dsonar.projectKey=<project_key> \
    -Dsonar.organization=<organization> -Dsonar.host.url=https://sonarcloud.io \
    -Dsonar.login=${{ secrets.SONAR_TOKEN }} --file ./simple-api/pom.xml
    ```

#### Build and Push Docker Image Workflow (`build-and-push-docker-image.yml`)
- Builds Docker images for backend and database.
- Pushes images to DockerHub.
- Docker images are only pushed on the `main` branch:
    ```yaml
    tags: ${{ secrets.DOCKERHUB_USERNAME }}/<image_name>:latest
    push: ${{ github.ref == 'refs/heads/main' }}
    ```

### Docker Images and DockerHub
- **Backend Image**: Built from `simple-api/Dockerfile`.
- **Database Image**: MySQL official image.

Images are stored on DockerHub using credentials stored in GitHub Secrets:
```yaml
docker login -u ${{ secrets.DOCKERHUB_USERNAME }} -p ${{ secrets.DOCKERHUB_PASSWORD }}
```

### Deployment with Ansible
1. **Inventory Configuration** (`setup.yml`):
   ```yaml
   all:
     vars:
       ansible_user: admin
       ansible_ssh_private_key_file: /path/to/id_rsa
     children:
       prod:
         hosts:
           lucas.fdida.takima.cloud
   ```
2. **Playbook** (`playbook.yml`):
   - Installs Docker and required packages.
   - Configures Docker network, database, application, and proxy roles.
   - Includes the **docker** role to install and configure Docker on the server.

3. **Roles**:
   - **docker**: Installs Docker and ensures the service is running.
   - **network**: Creates a Docker network.
   - **database**: Runs MySQL container.
   - **application**: Runs the Spring Boot API container.
   - **proxy**: Deploys NGINX as a reverse proxy.

4. **Deploy Application**:
   ```bash
   ansible-playbook -i ansible/inventories/setup.yml ansible/playbook.yml
   ```

## How to Access the Application
- **Server URL**: [http://lucas.fdida.takima.cloud](http://lucas.fdida.takima.cloud)
- The message **"It works!"** confirms successful deployment.

## Usage
### Local Testing
To run the application locally with Docker Compose:
```bash
docker-compose up
```

### Remote Deployment
To SSH into the remote server:
```bash
ssh -i ~/path/to/id_rsa admin@lucas.fdida.takima.cloud
```

Run the Ansible playbook to deploy:
```bash
ansible-playbook -i ansible/inventories/setup.yml ansible/playbook.yml
```

## Results
1. **GitHub Actions Workflow**:
   - CI and CD workflows running successfully.

2. **Application Deployment**:
   - Remote server displaying "It works!".

---
**Author**: Lucas FDIDA  
**Project Supervisor**: Takima
**Subject**: Application of Big Data
