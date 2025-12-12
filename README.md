# 🚀 Dockerized CI/CD Pipeline Using Multi-Node Jenkins

This project demonstrates a complete **Dockerized CI/CD pipeline** implemented using a **Multi-Node Jenkins architecture**.  
The setup includes:

- **Jenkins Master (Instance 1)**  
- **Jenkins Agent / Deploy Node (Instance 2)**  
- **Docker**
- **GitHub**
- **Freestyle Jenkins Job**

The pipeline automatically builds a Docker image, pushes it to Docker Hub, and deploys a running container on the agent node.

---

## 📌 1. Project Overview

This CI/CD pipeline follows DevOps best practices and automates the entire workflow:

1. Developer pushes code to **GitHub**  
2. **Jenkins Master** pulls the latest code  
3. Build & Docker operations execute on the **Deploy Node (Agent Node)**  
4. Docker image is **built** and **pushed to Docker Hub**  
5. Docker container is **deployed** and exposed on the server  

This ensures:
- Faster deployments  
- No manual errors  
- Consistent environment using Docker  
- Multi-node scalability  

---

## 🏗️ 2. Architecture

#Developer → GitHub → Jenkins Master → Jenkins Agent/Deploy Node → Docker Hub → Running Container

### 🔹 Jenkins Master (Instance 1)
- Installed Jenkins
- Coordinates CI/CD tasks
- Does **not** perform build or deployment

### 🔹 Jenkins Agent / Deploy Node (Instance 2)
- Runs all heavy load tasks:
  - Maven build (optional)
  - Docker build
  - Docker push
  - Docker container deployment
- Jenkins agent connected via SSH/JNLP

---

## 🛠️ 3. Tech Stack

- **Jenkins** (Master + Build/Deploy Agent)
- **Docker** (Build, push, deploy)
- **GitHub** (Source code repository)
- **HTTPD Web Server** (Application)
- **Docker Hub** (Image storage)

---

## 📁 4. Repository Structure

# Developer → GitHub → Jenkins Master → Jenkins Agent/Deploy Node → Docker Hub → Running Container

### ▶ Dockerfile
Builds a containerized Apache HTTPD app.

### ▶ index.html
Simple static webpage deployed via Docker.

---

## 5. Jenkins Freestyle Job Workflow (Detailed)
This CI/CD pipeline is created using a Jenkins Freestyle Job.
All major build and deployment operations run on the Deploy Node (Agent Node).

The job performs these actions:
	1.	Pulls latest code from GitHub
	2.	Builds a Docker image from the Dockerfile
	3.	Authenticates with Docker Hub
	4.	Pushes the image to Docker Hub
	5.	Deploys the updated container on the Deploy Node

The job uses Jenkins credentials binding for secure authentication.

---
## 6. Freestyle Job Configuration Steps
#1️⃣ Source Code Management
	•	Select: Git
	•	Enter Repository URL: 
	•	Branch: */main
  
#2️⃣ Restrict Job to Agent Node
 • Enable: Restrict where this project can be run
 • Label:deploy-node

#3️⃣ Build Environment
 • Enable: Use secret text(s) or file(s)
 Add your Docker Hub credentials:
	•	DOCKER_USERNAME
	•	DOCKER_PASSWORD

---
##🛠️ 8. Build Steps (Docker Build + Push)
Add a Build Step → Execute Shell with the following script:
-
echo "Building Docker Image..."
docker build -t $DOCKER_USERNAME/jenkins-cicd:$BUILD_NUMBER .

echo "Logging into Docker Hub..."
echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin

echo "Pushing Docker Image to Docker Hub..."
docker push $DOCKER_USERNAME/jenkins-cicd:$BUILD_NUMBER
-
This will:
	•	Build the image
	•	Log in to Docker Hub using Jenkins credentials
	•	Push the latest versioned image
---
##🚀 9. Post-Build Deployment (Run Container on Deploy Node)
Add a Post-Build Action → Execute Shell:
-
echo "Stopping old container if exists..."
docker rm -f myapp || true

echo "Pulling new image..."
docker pull $DOCKER_USERNAME/jenkins-cicd:$BUILD_NUMBER

echo "Starting new container..."
docker run -d --name myapp -p 9090:80 $DOCKER_USERNAME/jenkins-cicd:$BUILD_NUMBER
-
This ensures:
	•	Old container is removed
	•	Latest image is pulled
	•	Fresh deployment runs on port 9090
---

##🌐 10. Accessing the Deployed Application
Once the Jenkins job completes successfully, your application will be available at:

http://<Deploy-Node-Public-IP>:9090

You should see the web page from your index.html file hosted via Dockerized Apache HTTPD.

