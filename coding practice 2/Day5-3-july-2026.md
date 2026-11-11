Day 05 — Advanced CI/CD and Infrastructure Provisioning
🔹 Jenkinsfile — With Git Integration and Testing

pipeline {
    agent any

    stages {
        stage ('Clone repository') {
            steps {
                git 'https://github.com/pravin/app-java-repo.git'
            }           
        }
        stage('Build') {
            steps {
                echo 'Build project from  repo'
            }
        }
        stage('Test') {
            steps {
                echo 'Test project '
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploy project'
            }
        }
    }
}

.............................................

 GitLab CI/CD — With Docker Build


 stages:
   - build
   - push
   - deploy

build:
  stage: build
  script:
    - docker build -t pathnex-app .

push:
  stage: push
  script: 
    - docker push pathnex-app

deploy:
  stage: Deploy
  script: 
    - kubectl apply -f kubernetes/deployment.yaml

>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>


Example 1: GitHub Actions — Git Integration, Build, Test, Deploy

Create:

.github/workflows/ci.yml

name: Java CI Pipeline

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

jobs:
  build-test-deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Clone Repository
        uses: actions/checkout@v4

      - name: Set up Java
        uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: '17'

      - name: Build Project
        run: |
          echo "Building project..."
          ./mvnw clean package
          # or
          # gradle build

      - name: Run Tests
        run: |
          echo "Running tests..."
          ./mvnw test

      - name: Deploy Project
        run: |
          echo "Deploying project..."

  >>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>


Example 2: GitHub Actions — Docker Build, Push & Kubernetes Deploy

Create:

.github/workflows/docker-deploy.yml

name: Docker CI/CD Pipeline

on:
  push:
    branches:
      - main

jobs:
  docker-build-push-deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Source
        uses: actions/checkout@v4

      - name: Log in to Docker Hub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKER_USERNAME }}
          password: ${{ secrets.DOCKER_PASSWORD }}

      - name: Build Docker Image
        run: |
          docker build -t ${{ secrets.DOCKER_USERNAME }}/pathnex-app:latest .

      - name: Push Docker Image
        run: |
          docker push ${{ secrets.DOCKER_USERNAME }}/pathnex-app:latest

      - name: Configure kubectl
        uses: azure/setup-kubectl@v4

      - name: Deploy to Kubernetes
        run: |
          kubectl apply -f kubernetes/deployment.yaml

          >>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

Add Docker Hub Secrets to GitHub Actions (Short Steps)
Open your GitHub repository.
Go to Settings → Secrets and variables → Actions.
Click New repository secret.
Add:
Name: DOCKER_USERNAME
Secret: Your Docker Hub username
Click Add secret.
Click New repository secret again.
Add:
Name: DOCKER_PASSWORD
Secret: Your Docker Hub password or (recommended) Access Token
Click Add secret.

Your workflow can now use:

username: ${{ secrets.DOCKER_USERNAME }}
password: ${{ secrets.DOCKER_PASSWORD }}


>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>


Ansible — Create User and Set Permissions

---
- name: Create user and set permission
  hosts: all
  become: yes

  tasks:
    - name: Create User
      user: 
        name: pravin
        state: present
        shell: /bin/bash

    - name: set permission for user
      file:
        path: /home/pathnex
        owner: pravin
        group: pravin
        mode: '755' 

.............................................................

Terraform — EC2 with Tags and Volume

resource "aws_instance" "PravinEC2" {
    ami           = "ami-0abcde1234abcd1234"
    instance.type = "c5.xlarge"

    tags {
        Name = "PravinEC2"
    }

    ebs_block_device {
        device_name = "/dev/sdh"
        volume_size = 50
    }
}




>>>>>>>>>>>>>>>>>>>>>


🔹 Docker
# Python Application
print("Hello Pathnex")

# Docker File
FROM python:3.11
WORKDIR /opt/pathnex/python-app
COPY app.py /opt/pathnex/python-app/
CMD ["python", "/opt/pathnex/python-app/app.py"]

FROM: python:3.11
WORKDIR /opt/pathnex/python-app
COPY app.py /opt/pathnex/python-app/
CMd ["python", "/opt/pathnex/python-app/app.py"]

# Real Path
/opt/pathnex/python-app

