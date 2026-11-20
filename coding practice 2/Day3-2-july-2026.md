Day 03 — Ansible, Terraform & Kubernetes Basics 🔹 Ansible — Install Apache and Start Service

name: Install and Start Apache hosts: all become: yes tasks:
name: Install apache yum: name: httpd state: present

name: Start apache service: name: httpd state: started enabled: yes

>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>....

---
- name: Ansible- Install Apache and Start Service
  hosts: all
  become: yes

  tasks:
    - name: Install Apache
      yum: 
        name: httpd
        state: present

    - name: Start Service
      service:
        name: httpd
        state: started
        enabled: yes    

>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>..

Terraform — EC2 Instance (t3.medium) provider "aws" { region = "us-east-1" }

resource "aws_instance" "PathnexEC2" { ami = "ami-0abcd1234abcd1234" instance_type = "t3.medium" tags = { Name = "Pathnex-T3" } } 


provider "aws" {
    region = "us-east-1"
}

resource "aws_instance" "PravinEC2" {
    ami         = "ami-0abcd1234abcd1234"
    instance_type = "c5.xlarge"

    tags = {
        Name = "Pravin-T3"
    }
}


>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>..
🔹 Kubernetes — Create a ReplicaSet with 3 Replicas 
apiVersion: apps/v1 kind: ReplicaSet metadata: name: pathnex-replicaset spec: replicas: 3 selector: matchLabels: app: pathnex-app template: metadata: labels: app: pathnex-app spec: containers: - name: app image: nginx ports: - containerPort: 80 


apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: Pravin-ReplicaSet
spec:
  replicas: 3
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





>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>.>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>.

🔹 Shell Script — User Info #!/bin/bash echo "Current user: 

#!/bin/bash

echo "Current user: $(whoami)"
echo "Current user group: $(groups)"

>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>..


 Docker

Docker File for Nginx Web Server


FROM nginx:latest

COPY index.html /usr/share/nginx/html/index.html


#HTML

<h1>Hello Pravin </h1>

# BAsh 
# Real Path

/usr/share/nginx/html

#BAsh
docker build -t pathnex-nginx .
docker run -d -p 80:80 pathnex-nginx

>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>..

