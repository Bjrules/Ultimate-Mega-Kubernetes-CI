# Ultimate-Mega-Kubernetes-CI
## Project Description:
>This Project implements an End-2-End DevSecOps Automated CI/CD(jenkins) which Deploys a Java Application(Bank App) to kubernetes. It clones, compiles, tests, Performs Security Scanning(trivy) as well as generates reports. It also Scan for bugs and vulnerability using SonarQube and performs QualityGate Check for the purpose of Quality of the Software. It further packages and Deploy the artifacts to nexus server which has been preconfigured in jenkins. furthermore, it builds and push to dockerhub with dynamic tagging. Another Interesting part is that, this project also updates the CD Repository [https://github.com/Bjrules/Ultimate-Mega-Kubernetes-CD.git] with the latest build tag Number on github for latest version for the next Deployment .Finally it sends a Email on each build whether SUCCESSFUL or FAILED BUILDS.

![alt text](Mega-Project-CICD.drawio.png)
---

## Step1.  Setup Jenkins-Server for (CI Pipeline) and Infrastructure-Server (for Kubernetes Deployments) 
### Infra-Server

1. Install AWS CLI
```
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
sudo apt install unzip
unzip awscliv2.zip
sudo ./aws/install
aws configure
```
---
2. Install Terraform

``` 
sudo apt-get update && sudo apt-get install -y gnupg software-properties-common curl

curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg

echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list

sudo apt-get update && sudo apt-get install terraform -y

terraform -version 

```
---
3. Install kubectl
```
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"

curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256"
 
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl

kubectl version --client
```
#chmod +x kubectl
#mkdir -p ~/.local/bin
#mv ./kubectl ~/.local/bin/kubectl
# and then append (or prepend) ~/.local/bin to $PATH
---
4. Install Ekctl
```
curl -sLO "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz"

tar -xzf eksctl_$(uname -s)_amd64.tar.gz

sudo mv eksctl /usr/local/bin

eksctl version
```
---
5. Install HELM
```
sudo apt update && sudo apt upgrade -y
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash

wget https://get.helm.sh/helm-v3.14.0-linux-amd64.tar.gz
tar -zxvf helm-v3.14.0-linux-amd64.tar.gz
sudo mv linux-amd64/helm /usr/local/bin/helm
helm version
```





get your app password here.
https://myaccount.google.com/apppasswords


Configuring webhook trigger for gitjub in jenkins.
http://JENKINS_URL/generic-webhook-trigger/invoke?token=TOKEN_HERE
http://54.234.122.198:8080/generic-webhook-trigger/invoke?token=jenkinswebhook


docker run -d --name sonar -p 9000:9000 sonarqube:lts-community
sudo usermod -aG docker $USER
newgrp docker

docker run -d --name nexus3 -p 8081:8081 sonatype/nexus3
sudo usermod -aG docker $USER
newgrp docker

### On Jenkins, after installing docker on the jenkins machine ensure you are runing commands as jenkins user and not as ubuntu user. 
sudo usermod -aG docker jenkins
newgrp docker
Restart jenkins server using http://jenkins-ip:8080/restart to effect changes

### plugins required in jenkins for this project
1. Generic Webhook Trigger
2. Blue ocean
3. Pipeline Stage view
4. SonarQube Scanner
5. Docker Pipeline
6. Kubernetes CLI
7. Config File Provider
8. Maven Integration
9. Pipeline Maven integration



Full Documentation coming soon..