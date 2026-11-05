Day 06 — Introduction to CloudFormation and Kubernetes Advanced Concepts

Ansible — Install Docker

---
- name: Install Docker
  hosts: all
  become: yes

  tasks: 
    - name: Install Docker
      yum: 
        name: docker
        state: present
    - name: Start Docker
      service: 
        name: docker
        state: started
        enabled: yes

>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

🔹 Terraform — Provisioning EC2 with VPC

resource "aws_vpc"  "pravinVpc" {
    cidr_block = "[10.0.0.0/16]"
}

resource "aws_subnet" "main" {
  vpc_id     = aws_vpc.pravinVpc.id
  cidr_block = "10.0.1.0/24"
}


resource "aws_instance" "pravinInstance" {
    ami           = "ami-0abcde12345abcde12345"
    instance_type = "c5.xlarge"
    subnet_id    = aws_subnet.main.id

    tags = {
        Name = "Pravin-EC2"
    }
}


Visual representation
aws_vpc.pravinVpc
        │
        │ vpc_id
        ▼
aws_subnet.main
        │
        │ subnet_id
        ▼
aws_instance.pravinInstance


>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
\

🔹 Kubernetes — Pod with Volume Mount


apiVersion: v1
kind: Pod
metadata:
  name: pravin-pod
spec:
  containers:
    - name: nginx
      image: nginx
      volumeMounts:
        - mountPath: /usr/share/nnginx/html
          name: html-vol
   volumes: 
     - name: html-volume
       hostPath:
         path: /data
         type: Directory


         >>>>>>>>>>>>>>>>\


         | Scenario                      | Recommended Volume        |
| ----------------------------- | ------------------------- |
| Temporary cache               | `emptyDir`                |
| Configuration files           | `configMap`               |
| Passwords/API keys            | `secret`                  |
| Database storage              | `persistentVolumeClaim`   |
| Shared network storage        | `nfs`                     |
| Cloud disks (AWS, Azure, GCP) | `csi` (usually via a PVC) |
| Local development             | `hostPath`                |


>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

Shell Script — Check for Running Services
#!/bin/bash
services=("nginx" "docker" "httpd")
for service in "${services[@]}"
do
  if systemctl is-active --quiet $service; then
    echo "$service is running"
  else
    echo "$service is not running"
  fi
done

>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

#!/bin/bash

services=("nginx" "docker" "httpd")
for service in "${services[@]}" 
do 
  if systemctl is-active --quiet $service; then
    echo "$service is running"
  else
    echo "$service is not running"
  fi
done


>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

systemctl is-active --quiet "$service"

This checks whether the service is active (running).

systemctl → Command to manage systemd services.
is-active → Checks if the service is active.
--quiet → Suppresses output and only returns an exit status.

Exit status:

0 → Service is running.
Non-zero → Service is not running (or doesn't exist).

>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>


Docker
🔹 Docker
# Node.js Application (JavaScript)

console.log("Hello Pravin !")

#Dockerfile

FROM node:18
WORKDIR /opt/pravin/node-app
COPY app.js /opt/pravin/node-app/
CMD ["node", "/opt/pravin/node-app/app.js"]

#real path

/opt/pravin/node-app

