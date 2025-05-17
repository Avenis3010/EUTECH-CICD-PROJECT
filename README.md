
# FLASK-APP

## Deploy a simple flask-app web application using AWS.

## Create the infrastructure using Terraform

### Set up the following in AWS :
 One VPC or equivalent network configuration
 
 2 subnets (1 public, 1 private)
 
 One EC2 instance (or virtual machine) to host the application
 
 A security group or firewall rule that allows:
 
 HTTP (port 80) and SSH (port 22) from your IP only
 
 Optional: Set up an Application Load Balancer for the instance


## Installation

Install Terraform on ubuntu

```bash
- sudo apt-get update && sudo apt-get install -y gnupg software-properties-common
- wget -O- https://apt.releases.hashicorp.com/gpg | \
  gpg --dearmor | \
- sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg > /dev/null
- gpg --no-default-keyring \
   --keyring /usr/share/keyrings/hashicorp-archive-keyring.gpg \
   --fingerprint
- echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] 
  https://apt.releases.hashicorp.com $(grep -oP '(?<=UBUNTU_CODENAME=).*' /etc/os-release || lsb_release -cs) main" | sudo tee 
  /etc/apt/sources.list.d/hashicorp.list
- sudo apt-get install terraform
```
Install AWS CLI and configure it
```bash
- curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
- unzip awscliv2.zip
- sudo ./aws/install
- aws --version
- aws configure
```

Run Terraform commands to create the infrastructure
```
- terraform init
- terraform plan
- terraform apply
```
### Connect to EC2 machine 

#### Install docker on EC2 Machine
```bash
 sudo apt install docker.io -y
 sudo usermod -aG docker ubuntu && newgrp docker
```
#### Install jenkins on EC2 Machine
```bash
  sudo apt update -y
  sudo apt install fontconfig openjdk-17-jre -y

  sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
  
  echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc]" \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
  
  sudo apt-get update -y
  sudo apt-get install jenkins -y
```

#### Clone the Repository
```bash
  git clone https://github.com/Avenis3010/EUTECH-CICD-PROJECT.git
```

## Deployment

#### Create a Dockerfile
```bash
  FROM python:3.9-slim
  WORKDIR /app
  COPY app.py requirements.txt ./
  RUN pip install -r requirements.txt
  EXPOSE 80
  CMD ["python", "app.py"]
```


 #### Build the image with docker
```bash
  docker build -t eu-tech .
```

##### Run the application with docker
```bash
  docker run -d -p 8000:8000 --name eutech-proj eu-tech:latest
```

#### Push the image to DockerHub

```bash
 docker login -u dockerHub_Username
 docker push {dockerHub_Username}/{image_name}
```
### Store credentials in jenkins
#### Go to Manage Jenkins --> credentials and add DockerHub and EC2-SSH-KEY credentials

![image](https://github.com/user-attachments/assets/e76e2970-972e-46e2-b34a-a4238fd8ee55)

![image](https://github.com/user-attachments/assets/032c2674-f3c3-4e49-a83e-d6763d74fc35)

Go to Jenkins dashboard --> click on New Item --> Give a name to project --> select pipeline --> create 

![image](https://github.com/user-attachments/assets/e850cd82-93b6-46d0-a764-cd412d83d3b4)

![image](https://github.com/user-attachments/assets/a1e70ff4-c607-4462-ad66-9f183c5f7617)

#### click on build now

![image](https://github.com/user-attachments/assets/a6f6ad67-a3d1-458c-98e9-60cd1172e2fa)

#### you can see our application will be running on port 80, access it using your public ip on broswer using

 http://{Public-ip}:80 

 #### we have created simple flask-app so it will look like

 ![image](https://github.com/user-attachments/assets/848d85a3-cb8f-4e4f-ab03-d5ca33aaee1b)


 ##  Monitoring with Zabbix
###  Install the Zabbix Agent on the virtual machine
###  Install Zabbix Server (it can be hosted separately or on the same instance)
#### 1. Install Docker and Docker Compose (if not already installed)
On Ubuntu:
```bash
sudo apt update
sudo apt install docker.io docker-compose -y
sudo systemctl enable docker
sudo systemctl start docker
```
###  Create a Docker Compose File
Create a directory for Zabbix:

```bash
mkdir zabbix-docker && cd zabbix-docker
```
Create a file named docker-compose.yml:
```bash
nano docker-compose.yml
```
### basic setup for PostgreSQL + Zabbix

```bash

version: '3.5'

services:
  postgres:
    image: postgres:13
    environment:
      POSTGRES_USER: zabbix
      POSTGRES_PASSWORD: zabbixpass
      POSTGRES_DB: zabbix
    volumes:
      - ./pgdata:/var/lib/postgresql/data

  zabbix-server:
    image: zabbix/zabbix-server-pgsql:alpine-6.0-latest
    environment:
      DB_SERVER_HOST: postgres
      POSTGRES_USER: zabbix
      POSTGRES_PASSWORD: zabbixpass
    depends_on:
      - postgres
    ports:
      - "10051:10051"

  zabbix-web:
    image: zabbix/zabbix-web-nginx-pgsql:alpine-6.0-latest
    environment:
      DB_SERVER_HOST: postgres
      POSTGRES_USER: zabbix
      POSTGRES_PASSWORD: zabbixpass
      ZBX_SERVER_HOST: zabbix-server
      PHP_TZ: UTC
    depends_on:
      - zabbix-server
    ports:
      - "9090:8080"
```
### start zabbix
```bash
  sudo docker-compose up -d
```
### Access Zabbix UI
  Open your browser and go to:
  ```bash
  http://<your-server-ip>:9090
```
  
### STEP 1: Login to Zabbix Web UI
Default login: Admin
Password: zabbix

### STEP 2: Add the Host to Monitor
If you want to monitor the local host (Zabbix server itself):
Go to Configuration → Hosts
Click Create host
Set:

Host name: Zabbix Server

Groups: Select or create Linux servers

Agent interface: Add instance ip

Templates tab:

   Click Select → Find and add:
   
Template OS Linux by Zabbix agent

This template includes monitoring for CPU, memory, disk, and other basics.

Click Add

### STEP 3: View Monitoring Data
Go to Monitoring → Hosts

Click on your host

Go to Latest Data to view:

CPU load/usage

Available memory

Disk space usage

### STEP 4: Create a Zabbix Dashboard
Go to Monitoring → Dashboards

Click Create dashboard

Add widgets such as:

Data overview → for quick CPU/mem/disk view

Graphs → visualize CPU load, memory, disk

### STEP 5 :  Set Up an Email Alert (Optional)
📬 1. Configure Media Type (Email)
Go to Administration → Media types

Click Email (built-in one)

Set SMTP server details (e.g., for Gmail):

SMTP server: smtp.gmail.com

SMTP port: 587

SMTP HELO: yourdomain.com

SMTP Email: your-email@gmail.com

SMTP authentication: username / password

You may need to enable App Passwords if using Gmail.

### STEP 6: Enable Alert Trigger
Zabbix already has default triggers, but you can fine-tune:

Go to Configuration → Hosts → Your Host

Click Triggers tab

Add or edit:

Example:

Name: High CPU usage

Expression: {your-host:system.cpu.util[,user].last()}>80

Severity: High



## ubuntu disk/pace usage dashboard

![image](https://github.com/user-attachments/assets/156e27b5-7ac5-4490-a7a9-0fb0ce75d4a4)

![image](https://github.com/user-attachments/assets/8f5baee2-e554-4a41-95df-96919d7fcffd)

![image](https://github.com/user-attachments/assets/221d94ce-19fe-4fda-addc-500d4f9196af)




 








