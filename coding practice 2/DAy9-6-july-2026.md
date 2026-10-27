Day 09 — Docker Integration with Jenkins & GitLab
🔹 Ansible — Setup Docker Container for Nginx


---
- name: Setup Docker Container for nginx
  hosts: all
  become: yes

  tasks:
    - name: Pull nginx image
      community.docker.docker_image:
        name: nginx
        source: pull
    
    - name: run nginx container
      community.docker.docker_container:
        name: nginx-container
        image: nginx
        state: started
        restart_policy: always
        published_ports:
          - "8080:80"

    >>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>



What is FQCN?

FQCN stands for Fully Qualified Collection Name.

The format is:

collection_name.module_name

Examples:

FQCN	Purpose
ansible.builtin.copy	Copy files
ansible.builtin.service	Manage services
community.docker.docker_image	Pull/build Docker images
community.docker.docker_container	Run/manage Docker containers

>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>


 Kubernetes — Horizontal Pod Autoscaler (HPA)

 apiVersion: apps/v1
 kind: Deployment
 metadata:
   name: pravin-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: pravin-app
  template:
    metadata:
      labels:
        app: pravin-app
    spec:
      containers:
        - name: nginx
          image: nginx
          ports:
            - containerPort: 80

---

apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata: 
  name: pravin-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment  
    name: pravin-deployment
  minReplicas: 1
  maxReplicas: 10

  metrics:
    - type: Resource
      resource: 
        name: cpu
        target:
          type: Utilization
          averageUtilization: 50

>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>


Terraform Terraform — EC2 with Auto Scaling Group

For current DevOps/SRE interviews and real environments, use:

VPC → Subnets → Security Groups → Launch Template → Auto Scaling Group → Target Tracking Policy → Load Balancer → CloudWatch alarms

with aws_launch_template instead of aws_launch_configuration.

--------------------------------------------------------------------------

provider "aws" {
  region = "us-east-1"
}

# Get latest Amazon Linux 2023 AMI
data "aws_ami" "amazon_linux" {
  most_recent = true

  owners = ["amazon"]

  filter {
    name   = "name"
    values = ["al2023-ami-*-x86_64"]
  }

  filter {
    name   = "state"
    values = ["available"]
  }
}



# Security Group
resource "aws_security_group" "example" {
  name = "example-sg"

  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["YOUR_IP/32"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}



# Launch Template
resource "aws_launch_template" "example" {

  name_prefix = "example-template-"

  image_id = data.aws_ami.amazon_linux.id

  instance_type = "t3.medium"

  vpc_security_group_ids = [
    aws_security_group.example.id
  ]


  # Enforce IMDSv2
  metadata_options {
    http_tokens = "required"
  }


  tag_specifications {

    resource_type = "instance"

    tags = {
      Name = "autoscaling-instance"
    }
  }
}



# Auto Scaling Group
resource "aws_autoscaling_group" "example" {

  name = "example-asg"

  min_size         = 1
  desired_capacity = 2
  max_size         = 5


  # Use multiple subnets/AZs in production
  vpc_zone_identifier = [
    "subnet-12345678",
    "subnet-87654321"
  ]


  launch_template {

    id = aws_launch_template.example.id

    version = "$Latest"
  }


  health_check_type = "EC2"


  tag {

    key                 = "Name"

    value               = "example-asg-instance"

    propagate_at_launch = true
  }
}


# Auto Scaling Policy
resource "aws_autoscaling_policy" "cpu_scaling" {

  name = "cpu-target-tracking"

  autoscaling_group_name = aws_autoscaling_group.example.name


  policy_type = "TargetTrackingScaling"


  target_tracking_configuration {

    predefined_metric_specification {

      predefined_metric_type = "ASGAverageCPUUtilization"
    }


    target_value = 60
  }
}

>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>


Terraform — EC2 with Auto Scaling Group
resource "aws_launch_configuration" "example" {
  name = "example-config"
  image_id = "ami-0abcd1234abcd1234"
  instance_type = "t3.medium"
}

resource "aws_autoscaling_group" "example" {
  desired_capacity     = 2
  max_size             = 3
  min_size             = 1
  vpc_zone_identifier  = ["subnet-12345678"]
  launch_configuration = aws_launch_configuration.example.id
}

>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>


Jenkinsfile — Deploy to Kubernetes
pipeline {
    agent any
    stages {
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    sh 'kubectl apply -f deployment.yaml'
                }
            }
        }
    }
}



🔹 GitLab CI/CD — Deploy to Kubernetes
stages:
  - deploy

deploy:
  stage: deploy
  script:
    - kubectl apply -f kubernetes/deployment.yaml


>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>



🔹 Docker
# Environment Variables
FROM ubuntu:22.04
ENV INSTITUTE=Pathnex
ENV COURSE=DevOps
CMD echo "$INSTITUTE - $COURSE"

>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

name: Deploy to Kubernetes

on:
  push:
    branches:
      - main

jobs:
  deploy:
    name: Deploy Application
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4


      - name: Configure Kubernetes credentials
        uses: azure/k8s-set-context@v4
        with:
          method: kubeconfig
          kubeconfig: ${{ secrets.KUBE_CONFIG }}


      - name: Deploy to Kubernetes
        uses: azure/k8s-deploy@v5
        with:
          manifests: |
            kubernetes/deployment.yaml
            kubernetes/service.yaml
