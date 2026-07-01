Day 02 — Services & Tags

🔹 Ansible — Install & Enable Nginx


---
- name: Install and Enable Nginx
  hosts: all
  become: yes

  tasks:
    - name: Install nginx
      yum:
        name: nginx
        state: present
        
    - name: Enable nginx
      service:
        name: nginx
        state: started
        enabled: true

>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>.


    Terraform — EC2 with Tags (r5.2xlarge) provider "aws" { region = "us-east-1" }

    provider "aws" {
  region = "us-east-1"
}

resource "aws_instance" "PathnexEC2Pravin" {
  ami           = "ami-0abcd1234abcd1234"
  instance_type = "r5.2xlarge"

  tags = {
    Name        = "Pathnex-Server-Pravin"
    Environment = "Training"
    Owner       = "PathnexStudentPravin"
  }
}



    >>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>


Kubernetes — Deployment with 2 Replicas
apiVersion: apps/v1 kind: Deployment metadata: name: pathnex-deployment spec: replicas: 2 selector: matchLabels: app: pathnex-app template: metadata: labels: app: pathnex-app spec: containers: - name: app image: nginx ports: - containerPort: 80

apiVersion: apps/v1
kind: Deployment
metadata:
  name: pravin-deployment
spec:
  replicas: 2

  selector:
    matchLabels:
      app: nginx
  
  template:
    metadata:
      labels:
        app: nginx
    
    spec:
      containers:
        - name: nginx
          image: nginx
          ports:
            - containerPort: 80


>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>


 Shell Script — Disk Usage #!/bin/bash df -h

 #!/bin/bash

 df -h

 >>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

 Docker Images & Containers

 docker pull ubuntu 

 docker image 

 ps -a

 >>>>>>>>>>>>>>>>>>>>>

 Docker File
FROM ubuntu:22.04 RUN apt update CMD ["echo", "Hello Pathnex"]

FROM ubuntu:22.04 

RUN apt update

CMD ["echo", "Hello Pathnex"]
