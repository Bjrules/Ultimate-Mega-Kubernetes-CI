# Ultimate-Mega-Kubernetes-CI
## Project Description:
>This Project implements an End-2-End DevSecOps Automated CI/CD(jenkins) which Deploys a Java Application(Bank App) to kubernetes. It clones, compiles, tests, Performs Security Scanning(trivy) as well as generates reports. It also Scan for bugs and vulnerability using SonarQube and performs QualityGate Check for the purpose of Quality of the Software. It further packages and Deploy the artifacts to nexus server which has been preconfigured in jenkins. furthermore, it builds and push to dockerhub with dynamic tagging. Another Interesting part is that, this project also updates the CD Repository [https://github.com/Bjrules/Ultimate-Mega-Kubernetes-CD.git] with the latest build tag Number on github for latest version for the next Deployment .Finally it sends a Email on each build whether SUCCESSFUL or FAILED BUILDS.


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