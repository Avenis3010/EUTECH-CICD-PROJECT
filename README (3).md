
# FLASK-APP

Deploy a simple flask-app web application using AWS.


## Installation

Install my-project with npm

Install docker on EC2 Machine
```bash
 sudo apt install docker.io -y
 sudo usermod -aG docker ubuntu && newgrp docker
```
Install jenkins on EC2 Machine
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

Clone the Repository
```bash
  git clone https://github.com/Avenis3010/EUTECH-CICD-PROJECT.git
```

## Deployment

Create a Dockerfile
```bash
  FROM python:3.9-slim
  WORKDIR /app
  COPY app.py requirements.txt ./
  RUN pip install -r requirements.txt
  EXPOSE 80
  CMD ["python", "app.py"]
```


 Build the image with docker
```bash
  docker build -t eu-tech .
```

Run the application with docker
```bash
  docker run -d -p 8000:8000 --name eutech-proj eu-tech:latest
```

Push the image to DockerHub

```bash
 docker login -u dockerHub_Username
 docker push {dockerHub_Username}/{image_name}
```
Create a Jenkinsfile




