# devsecops-maven-owasp-zapproxy-ecr

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/078b7346-d95a-4a51-a506-a4349b188f47" />


sudo apt update
sudo apt upgrade -y

# Common tools
sudo apt install -y bash-completion wget git zip unzip curl jq net-tools build-essential ca-certificates apt-transport-https gnupg fontconfig

source /etc/bash_completion

# Install git
sudo add-apt-repository ppa:git-core/ppa
sudo apt update
sudo apt install git -y

# Install java
# OpenJDK 17
sudo apt install -y openjdk-17-jdk

# OR OpenJDK 21
sudo apt install -y openjdk-21-jdk

# Install Jenkins
sudo apt update
sudo apt install fontconfig openjdk-21-jre
java -version

Latest installer  https://www.jenkins.io/doc/book/installing/linux/


Long Term support
sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2026.key
echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc]" \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt update
sudo apt install jenkins

sudo cat /var/lib/jenkins/secrets/initialAdminPassword

# Docker
offical link
https://docs.docker.com/engine/install/ubuntu/

# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update

sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Add user to docker group (log out / in or newgrp to apply)
sudo usermod -aG docker $USER
newgrp docker
docker ps

# for access
sudo usermod -aG docker jenkins
sudo systemctl restart jenkins

# Trivy scanner
sudo apt-get install wget apt-transport-https gnupg lsb-release
wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | sudo apt-key add -
echo deb https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main | sudo tee -a /etc/apt/sources.list.d/trivy.list
sudo apt-get update
sudo apt-get install -y trivy


trivy --version

#  Nexus....
install on new VM

#!/bin/bash

# Update package list
sudo apt-get update

# Install Java 17 and wget
sudo apt-get install -y wget apt-transport-https

# Add Corretto repository and install Java 17
sudo apt-get install -y software-properties-common


sudo apt-get update
sudo apt-get install -y openjdk-17-jdk

# Create directories
sudo mkdir -p /opt/nexus/
sudo mkdir -p /tmp/nexus/
cd /tmp/nexus/

# Download and extract Nexus
wget -LO "https://download.sonatype.com/nexus/3/nexus-3.85.0-03-linux-x86_64.tar.gz"

sudo tar xzvf nexus-3.85.0-03-linux-x86_64.tar.gz

sudo rm -rf /tmp/nexus/nexus.tar.gz
sudo cp -r /tmp/nexus/* /opt/nexus/


# Create nexus user and set permissions
sudo useradd --system --no-create-home --shell /bin/false nexus
sudo chown -R nexus:nexus /opt/nexus

# Create systemd service file
sudo cat <<EOT>> /etc/systemd/system/nexus.service
[Unit]
Description=nexus service
After=network.target

[Service]
Type=forking
LimitNOFILE=65536
ExecStart=/opt/nexus/nexus-3.85.0-03/bin/nexus start
ExecStop=/opt/nexus/nexus-3.85.0-03/bin/nexus stop
User=nexus
Restart=on-abort

[Install]
WantedBy=multi-user.target
EOT

# Configure Nexus to run as nexus user
sudo echo 'run_as_user="nexus"' > /opt/nexus/nexus-3.85.0-03/bin/nexus.rc

# Reload systemd and start Nexus
sudo systemctl daemon-reload
sudo systemctl start nexus
sudo systemctl enable nexus

# Clean up
sudo rm -rf /tmp/nexus/

Defaul username admin
password at this path  /opt/nexus/sonatype-work/nexus3/admin.password

http://ip:8081

# Jenkins plungins

Eclipse Temurin installer Plugin
Email Extension Plugin
OWASP Dependency-Check Plugin
Pipeline: Stage View Plugin
SonarQube Scanner for Jenkins
Prometheus metrics plugin
Nodejs
Nexus Artifact Uploader
Pipeline Maven Integration
Pipeline Utility Steps
Slack Notification
Amazon Web Services SDK :: All
Amazon ECR
Pipeline: AWS Steps
Docker Pipeline
CloudBees Docker Build and Publish
OWASP ZAP

#  SonarQube Docker Container Run for Analysis

docker run -d --name sonarqube \
  -p 9000:9000 \
  -v sonarqube_data:/opt/sonarqube/data \
  -v sonarqube_logs:/opt/sonarqube/logs \
  -v sonarqube_extensions:/opt/sonarqube/extensions \
  sonarqube:25.9.0.112764-community

  # Create the AWS Normal Account

IAM account [jenkins]
Policy [AmazonEC2ContainerRegistorFullaccess]
Create the Security credentials. [note it down one of the notepad]

# Jenkins Credentials to Store

Purpose	ID	Type	Notes
Email	mail-cred	Username/app password	
SonarQube	sonar-token	Secret text	From SonarQube application
Docker Hub	docker-cred	Secret text	From your Docker Hub profile
Nexus	nexuslogin	Username/app password	
aws-cred	awscreds	Username/app password	secret-key/access-key
Slack	slackcred	Secret text	From slack marketplace
Webhook example:
http://<jenkins-ip>:8080/sonarqube-webhook/


# Jenkins Tools Configuration

JDK [JDK17 , JDK21 ]
SonarQube Scanner installations [sonar-scanner]
Node node16
Dependency-Check installations [dp-check]
Maven [MAVEN3]

# Jenkins System Configuration

SonarQube servers:

Name: sonar-server
URL: http://sonar-ip-address:9000
Credentials: Add from Jenkins credentials
Extended E-mail Notification:

SMTP server: smtp.gmail.com
SMTP Port: 465
Use SSL
Default user e-mail suffix: @gmail.com
E-mail Notification:

SMTP server: smtp.gmail.com
Default user e-mail suffix: @gmail.com
Use SMTP Authentication: Yes
User Name: example@gmail.com
Password: Use credentials
Use TLS: Yes
SMTP Port: 587
Reply-To Address: example@gmail.com
Slack Notification:

workspace name
channel name [devsecopscicd]
Now See the configuration pipeline of the Jenkins
update the nexus Details in the jenkins pipeline
ip-address
port
artifact-repo-id [ vprofile-release ]
go to the Nexus Server [ Settings --> Repositories --> create Repositories --> maven2 (hosted) --> vprofile-repo --> create Repositories ]

*********

