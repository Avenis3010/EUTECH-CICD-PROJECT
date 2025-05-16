
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
# Store credentials in jenkins
## Go to Manage Jenkins --> credentials and add DockerHub and EC2-SSH-KEY credentials

![image](https://github.com/user-attachments/assets/e76e2970-972e-46e2-b34a-a4238fd8ee55)

![image](https://github.com/user-attachments/assets/032c2674-f3c3-4e49-a83e-d6763d74fc35)

Go to Jenkins dashboard --> click on New Item --> Give a name to project --> select pipeline --> create 

![image](https://github.com/user-attachments/assets/e850cd82-93b6-46d0-a764-cd412d83d3b4)

![image](https://github.com/user-attachments/assets/a1e70ff4-c607-4462-ad66-9f183c5f7617)

### click on build now

![image](https://github.com/user-attachments/assets/a6f6ad67-a3d1-458c-98e9-60cd1172e2fa)

### you can see our application will be running on port 80, access it using your public ip on broswer using

 http://{Public-ip}:80 

 ### we have created simple flask-app so it will look like

 ![image](https://github.com/user-attachments/assets/848d85a3-cb8f-4e4f-ab03-d5ca33aaee1b)


 








