| Component               | Purpose                                                    |
| ----------------------- | ---------------------------------------------------------- |
| kube-apiserver          | Entry point for all cluster operations and communication   |
| etcd                    | Stores the cluster's desired and current state             |
| kube-scheduler          | Assigns Pods to suitable worker nodes                      |
| kube-controller-manager | Runs controllers to maintain the desired state             |
| kubelet                 | Node agent that manages Pods on each worker node           |
| kube-proxy              | Implements Service networking and load balancing           |
| Container Runtime       | Pulls images and runs containers                           |
| Pod                     | Smallest deployable unit containing one or more containers |
| ReplicaSet              | Maintains the desired number of Pod replicas               |
| Deployment              | Manages ReplicaSets, updates, scaling, and rollbacks       |
| Service                 | Provides stable networking and load balancing for Pods     |
| ConfigMap               | Stores non-sensitive configuration                         |
| Secret                  | Stores sensitive configuration securely                    |
| Namespace               | Logically isolates resources within a cluster              |
| Ingress                 | Routes external HTTP/HTTPS traffic to Services             |

.>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

Developer
      ↓
kubectl apply deployment.yaml
      ↓
API Server
      ↓
etcd stores desired state
      ↓
Scheduler selects a Worker Node
      ↓
kubelet receives the Pod assignment
      ↓
Container Runtime pulls the image and starts the container
      ↓
Pod becomes Running
      ↓
Service provides a stable endpoint
      ↓
kube-proxy routes traffic to healthy Pods
      ↓
Users access the application


>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>444

Day 08 — Introduction to Docker and CI/CD Pipelines '\
🔹 Ansible — Install Docker Compose

---
- name: Install Docker Compose
  hosts: all
  become: yes

  tasks:
    - name: Install dependencies 
      yum:
        name: "python3-pip"
        state: present

    - name: Install Docker Compose
      pip: 
        name: docker-compose

>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>



---
- name: Install Docker Engine and Docker Compose v2
  hosts: all
  become: true

  tasks:

    - name: Install Docker
      yum:
        name: docker
        state: present

    - name: Start and enable Docker
      service:
        name: docker
        state: started
        enabled: yes

    - name: Create Docker CLI plugins directory
      file:
        path: /usr/local/lib/docker/cli-plugins
        state: directory
        owner: root
        group: root
        mode: '0755'

    - name: Download Docker Compose plugin
      get_url:
        url: https://github.com/docker/compose/releases/latest/download/docker-compose-linux-x86_64
        dest: /usr/local/lib/docker/cli-plugins/docker-compose
        owner: root
        group: root
        mode: '0755'

    - name: Verify Docker Compose installation
      command: docker compose version
      register: compose_version
      changed_when: false

    - name: Display Docker Compose version
      debug:
        var: compose_version.stdout


        >>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

Terraform — EC2 with Security Group and EBS Volume

data "aws_vpc" "default" {
  default = true
}

resource "aws_security_group" "allow_ssh_http" {
    name        = "all ssh http"
    description = "Allow SSH and HTTP access"
    vpc_id      = data.aws_vpc.default.id

    ingress {
        from_port = 22
        to_port   = 22 
        protocol  = "tcp"
        cidr_blocks = ["0.0.0.0/0"]
    } 

    egress {
        from_port = 0
        to_port   = 0
        protocol  = -1
        cidr_blocks = ["0.0.0.0/0"]
    }   
}

resource "aws_key_pair" "tcs_key" {
  key_name   = "tcs_keyname"
  public_key = file("~/.ssh/id_rsa.pub")
}

resource "aws_instance" "pravin_ec2" {
    ami           = "ami-0abcde1234acde1234"
    instance_type = "c5.xlarge"
    vpc_security_group_ids = [aws_security_group.allow_ssh_http.id]
    key_name = aws_key_pair.tcs_key.key_name

    ebs_block_device {
        device_name           = "/dev/sdh"
        volume_size           = 30
        volume_type           = "gp3"
        delete_on_termination = true
        encrypted             = true
    }
}


||||||||||||||||||||||||||||||||||||||||||||||||||44

data "aws_vpc" "default" {
  default = true
}

resource "aws_security_group" "allow_ssh_http" {

  name        = "allow-ssh-http"
  description = "Allow SSH and HTTP access"
  vpc_id      = data.aws_vpc.default.id

  ingress {

    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]

  }

  ingress {

    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]

  }

  egress {

    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]

  }
}

resource "aws_key_pair" "tcs_key" {

  key_name   = "tcs-keyname"
  public_key = file("~/.ssh/id_rsa.pub")

}

resource "aws_instance" "pravin_ec2" {

  ami           = "ami-xxxxxxxxxxxx"
  instance_type = "c5.xlarge"

  vpc_security_group_ids = [
    aws_security_group.allow_ssh_http.id
  ]

  key_name = aws_key_pair.tcs_key.key_name

  ebs_block_device {

    device_name           = "/dev/sdh"
    volume_size           = 30
    volume_type           = "gp3"
    delete_on_termination = true
    encrypted             = true

  }

  tags = {

    Name = "pravin-ec2"

  }
}

>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

 ----Kubernetes — Service with NodePort


apiVersion: v1
kind: Service
metadata:
  name: pathnex-service
  labels:
    app: pathnex-app

spec:
  type: NodePort
  selector:
    app: pathnex-app


  ports:
    - name: http
      protocol: TCP
      port: 80
      targetPort: 80
      nodePort: 30007


>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>



Option 1: ClusterIP + Ingress (Preferred in Companies)

This is the most common production pattern.

Why preferred?
Service is internal only (secure)
Ingress handles external traffic
Centralized routing (path-based / host-based)
Works well with AWS ALB / NGINX Ingress
1. Deployment (sample app)
apiVersion: apps/v1
kind: Deployment
metadata:
  name: pathnex-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: pathnex-app
  template:
    metadata:
      labels:
        app: pathnex-app
    spec:
      containers:
        - name: app
          image: nginx
          ports:
            - containerPort: 80
2. ClusterIP Service (internal only)
apiVersion: v1
kind: Service
metadata:
  name: pathnex-service
spec:
  type: ClusterIP
  selector:
    app: pathnex-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
3. Ingress (external access)
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: pathnex-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
    - host: pathnex.local
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: pathnex-service
                port:
                  number: 80
🔥 Flow
User
  ↓
Ingress Controller (NGINX / ALB)
  ↓
ClusterIP Service
  ↓
Pod (Deployment)


>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>


2: LoadBalancer (Cloud Native - AWS/GCP)

This is simpler but less flexible.

Why used?
Direct external access
No Ingress required
Good for simple apps / demos
LoadBalancer Service Example
apiVersion: v1
kind: Service
metadata:
  name: pathnex-lb-service
spec:
  type: LoadBalancer
  selector:
    app: pathnex-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
🔥 Flow
User
  ↓
AWS Load Balancer (ELB/ALB/NLB)
  ↓
Service (LoadBalancer type)
  ↓
Pod


>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>


Jenkinsfile — Docker Build and Push
pipeline {
    agent any
    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    docker.build('pathnex-app')
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://docker.io', 'docker-credentials') {
                        docker.image('pathnex-app').push('latest')
                    }
                }
            }
        }
    }
}


🔹 GitLab CI/CD — Build & Push Docker Image
stages:
  - build
  - push

build:
  stage: build
  script:
    - docker build -t pathnex-app .

push:
  stage: push
  script:
    - docker login -u "$CI_REGISTRY_USER" -p "$CI_REGISTRY_PASSWORD"
    - docker push pathnex-app


🔹 Docker
# Alpine Linux
FROM alpine:latest
WORKDIR /opt/pathnex/alpine-app
CMD ["echo", "Hello Pathnex"]

