# Ultimate-Mega-Kubernetes-CI
> Project Description:
End to End Project , kind see the Ultimate-Mega-Kubernetes-CD for the Deployment phase


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