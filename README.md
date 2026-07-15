# Ultimate-Mega-Kubernetes-CI
## Project Description:
>This Project implements an End-2-End DevSecOps Automation CI/CD(jenkins) which Deploys a Java Application(Bank App) to kubernetes(Encompassing all major DevOps Tools). It clones, compiles, tests, Performs Security Scanning(trivy) as well as generates reports. It also Scan for bugs and vulnerability using SonarQube and performs QualityGate Check for the purpose of Quality of the Software. It further packages and Deploy the artifacts to Nexus Server which has been pre-configured in jenkins. furthermore, it builds and push to dockerhub with dynamic tagging. Another Interesting part is that, this project also updates the CD Repository [https://github.com/Bjrules/Ultimate-Mega-Kubernetes-CD.git] with the latest build tag Number from jenkins on togithub for latest version for the next Deployment .It also sends a Email on each build whether SUCCESSFUL or FAILED BUILDS. Finally it also deploys Prometheus and Grafana and Exporters for Monitoring

![alt text](MEGA-PROJECT-MANY-TOOLS-CICD.drawio.png)
---

## Step1.  Setup Jenkins-Server for (CI Pipeline) and Infrastructure-Server (for Kubernetes Deployments) 
### Infra-Server Set-Up

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
##### OR
- chmod +x kubectl
- mkdir -p ~/.local/bin
- mv ./kubectl ~/.local/bin/kubectl
- and then append (or prepend) ~/.local/bin to $PATH
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
---
### Jenkins-Server SetUp
All these can be gotten via their official web portal
- [x] install JDK as a prerequisite to installing jenkins
- [x] Install Jenkins
- [x] Install Docker [install docker on ubuntu](https://docs.docker.com/engine/install/ubuntu)
- [x] Install Trivy [Install trivy ](https://trivy.dev/docs/latest/getting-started/installation)
- [x] Install kubectl as provided above

 On Jenkins Machine, after installing docker on the jenkins machine ensure you are runing commands as jenkins user and not as ubuntu user.
 ```

sudo usermod -aG docker jenkins
newgrp docker
Restart jenkins server using http://jenkins-ip:8080/restart to effect changes
```

> plugins required in jenkins for this project
1. Generic Webhook Trigger
2. Blue ocean
3. Pipeline Stage view
4. SonarQube Scanner
5. Docker Pipeline
6. Kubernetes CLI
7. Config File Provider
8. Maven Integration
9. Pipeline Maven integration
---
### SonarQube-Server Set-up
- [x] Install Docker
 Then Run SonarQube as a docker container....
```
docker run -d --name sonar -p 9000:9000 sonarqube:lts-community
sudo usermod -aG docker $USER
newgrp docker
```
Then Configuration begins see Screenshots..
---
### Nexus-Server Set-up
- [x] Install Docker
Then run Nexus Image as a container...

```
docker run -d --name nexus3 -p 8081:8081 sonatype/nexus3
sudo usermod -aG docker $USER
newgrp docker
```
See Screenshots
> EC2 Instances
![alt text](IMG_SCREENSHOTS/Screenshot_20260220_195549.png)
>Opened ports in Inboud Rules of Security Group
![alt text](IMG_SCREENSHOTS/Screenshot_20260220_193603.png)

 #### Create an IAM Access key for aws configure on infra-server
![alt text](IMG_SCREENSHOTS/Screenshot_20260220_202542.png)

![alt text](IMG_SCREENSHOTS/Screenshot_20260220_202958.png)

 #### Installed Docker and pull/run Docker Image/Container of both Nexus and Sonarqube on their respective Machines.
![alt text](IMG_SCREENSHOTS/Screenshot_20260220_210915.png)
![alt text](IMG_SCREENSHOTS/Screenshot_20260220_211309.png)
> Getting the password for Nexus by logging into the container 
``` docker exex -it container_id /bin/bash 
```
![alt text](IMG_SCREENSHOTS/Screenshot_20260220_212018.png)

#### Nexus and SonarQube Interfaces (sonarqube default username and password admin:admin)
![alt text](IMG_SCREENSHOTS/Screenshot_20260220_212144.png)
![alt text](IMG_SCREENSHOTS/Screenshot_20260220_212249.png)

#### installation of Docker on jenkins Machine
![alt text](IMG_SCREENSHOTS/Screenshot_20260220_205725.png)
![alt text](IMG_SCREENSHOTS/Screenshot_20260220_213126.png)
![alt text](IMG_SCREENSHOTS/Screenshot_20260220_213126.png)

> Ensure that jenkins is added to the docker group
![alt text](IMG_SCREENSHOTS/Screenshot_20260220_214002.png)
> Restart Jenkins
![alt text](IMG_SCREENSHOTS/Screenshot_20260220_213742.png)
![alt text](IMG_SCREENSHOTS/Screenshot_20260220_205725.png)

``` terraform init ``` On infra-server, so as to provision kubernetes using the Terraform EKS Module
![alt text](IMG_SCREENSHOTS/Screenshot_20260220_221018.png)
![alt text](IMG_SCREENSHOTS/Screenshot_20260220_222237.png)

#### create a Service Account that will enable us to able create resources such as EBS volumes using ekctl which uses aws Cloud formation.
```
eksctl create iamserviceaccount \
  --region us-east-1 \
  --name ebs-csi-controller-sa \
  --namespace kube-system \
  --cluster bb-cluster \
  --attach-policy-arn arn:aws:iam::aws:policy/service-role/AmazonEBSCSIDriverPolicy \
  --approve \
  --override-existing-serviceaccounts

  ```
  > This command  creates  pods for the ebs-csi-driver which will be needed for creating volumes and should able to attach the volumes to the worker nodes when it is needed
  ```
   kubectl apply -k "github.com/kubernetes-sigs/aws-ebs-csi-driver/deploy/kubernetes/overlays/stable/ecr/?ref=release-1.11"
   ```
  #### use 
   ```
    kubectl get pods -n kube-system
   ```
   #### to confirm
   # update Kubeconfig so as to be able to connect to my cluster
 ```
aws eks --region us-east-1 update-kubeconfig --name bb-cluster
```
#### Then
```
kubectl get nodes
```
#### to confirm that I am now able to connect to the Cluster
![alt text](IMG_SCREENSHOTS/Screenshot_20260220_231548.png)
![alt text](IMG_SCREENSHOTS/Screenshot_20260220_231723.png)

#### Study the screenshot below and refer to the RBAC (Role Based Access Control) RBAC Directory in this Repo
> This creates service account, role, role0binding, cluster-role, and cluster-rolebinding as well as "sceret" which will be used for authentication for the Service account.(more like password to the service account)
![alt text](IMG_SCREENSHOTS/Screenshot_20260221_000709.png)
> get the secret so as to copy it for use in jenkins
```
kubectl get secret secret-name -n webapps
```
![alt text](IMG_SCREENSHOTS/Screenshot_20260221_003229.png)

#### Configure SonarQube so as to get token to use for authentication inside jenkins
![alt text](IMG_SCREENSHOTS/Screenshot_20260221_004407.png)
![alt text](IMG_SCREENSHOTS/Screenshot_20260221_004448.png)
![alt text](IMG_SCREENSHOTS/Screenshot_20260221_004851.png)
![alt text](IMG_SCREENSHOTS/Screenshot_20260221_004942.png)
![alt text](IMG_SCREENSHOTS/Screenshot_20260221_005251.png)


#### Configure SonarQube webhook so as to Implement SonarQube QualityGate on the pipeline [Sonarqube Token] squ_583c4f8db698e2a14aeb17d54da7d00b413a9e52
> kindly see jenkinsfile
![alt text](IMG_SCREENSHOTS/Screenshot_20260221_015427.png)
![alt text](IMG_SCREENSHOTS/Screenshot_20260221_020907.png)
![alt text](IMG_SCREENSHOTS/Screenshot_20260221_021003.png)

#### Configure maven-releases and maven-snapshots in Nexus NB snapshots are for lower Environment while Releases is for Higher Environment (Prod)
![alt text](IMG_SCREENSHOTS/Screenshot_20260221_021140.png)
![alt text](IMG_SCREENSHOTS/Screenshot_20260221_021314.png)
![alt text](IMG_SCREENSHOTS/Screenshot_20260221_021337.png)
![alt text](IMG_SCREENSHOTS/Screenshot_20260221_021433.png)
![alt text](IMG_SCREENSHOTS/Screenshot_20260221_021455.png)

#### Configure Maven settings inside Jenkins as well
![alt text](IMG_SCREENSHOTS/Screenshot_20260221_021636.png)
![alt text](IMG_SCREENSHOTS/Screenshot_20260221_021703.png)
![alt text](IMG_SCREENSHOTS/Screenshot_20260221_021728.png)
![alt text](IMG_SCREENSHOTS/Screenshot_20260221_022024.png)
![alt text](IMG_SCREENSHOTS/Screenshot_20260221_022233.png)
![alt text](IMG_SCREENSHOTS/Screenshot_20260221_022352.png)

#### Docker Configuration with jenkins
![alt text](IMG_SCREENSHOTS/Screenshot_20260221_022648.png)
![alt text](IMG_SCREENSHOTS/Screenshot_20260221_023008.png)

#### This Screenshots shows the pipeline syntax with Credentials which binds credentials to variables for use so ae to use the variables for authentication into Github so as to "Update Manifest File in Ultimate Mega-Kubernetes-CD" with the latest tagging. Also kindly see the jenkinsfile for clariry
![alt text](IMG_SCREENSHOTS/Screenshot_20260221_023940.png)
![alt text](IMG_SCREENSHOTS/Screenshot_20260221_024057.png)

#### Configure for Email alert Notification for every Build Execution whether FAIL or SUCCESS. Also app password was generated for Authentication so that Gmail SMTP can be used to send the Emails.
![alt text](IMG_SCREENSHOTS/Screenshot_20260221_025924.png)
![alt text](IMG_SCREENSHOTS/Screenshot_20260221_030016.png)

[ Use this link to get your app password here.](https://myaccount.google.com/apppasswords)
![alt text](IMG_SCREENSHOTS/Screenshot_20260221_030745.png)
![alt text](IMG_SCREENSHOTS/Screenshot_20260221_031055.png)
![alt text](IMG_SCREENSHOTS/Screenshot_20260221_031507.png)
![alt text](IMG_SCREENSHOTS/Screenshot_20260221_031553.png)
![alt text](IMG_SCREENSHOTS/Screenshot_20260221_031629.png)

> Test Email succesfully sent
![alt text](IMG_SCREENSHOTS/Screenshot_20260221_031650.png)

> Pipeline Progress
![alt text](IMG_SCREENSHOTS/Screenshot_20260221_050832.png)
![alt text](IMG_SCREENSHOTS/Screenshot_20260221_050859.png)
![alt text](IMG_SCREENSHOTS/Screenshot_20260221_051036.png)
>At this point I was Sipping my Gin&Juice.
![alt text](IMG_SCREENSHOTS/Screenshot_20260221_051300.png)

#### Configuring Generic WebHook Trigger so that the pipeline is triggered upon commit to the main Branch
![alt text](IMG_SCREENSHOTS/Screenshot_20260221_053130.png)
![alt text](IMG_SCREENSHOTS/Screenshot_20260221_053339.png)
![alt text](IMG_SCREENSHOTS/Screenshot_20260221_053622.png)
![alt text](IMG_SCREENSHOTS/Screenshot_20260221_053759.png)
> configure webhooks in Github by setting the payload URL
![alt text](IMG_SCREENSHOTS/Screenshot_20260221_054809.png)
> ping
![alt text](IMG_SCREENSHOTS/Screenshot_20260221_055150.png)
>Webhook  trigger works when a commit is made to the main branch
![alt text](IMG_SCREENSHOTS/Screenshot_20260221_055551.png)

Configuring webhook trigger for github in jenkins (Use the Format Below)
http://JENKINS_URL/generic-webhook-trigger/invoke?token=TOKEN_HERE

http://54.234.122.198:8080/generic-webhook-trigger/invoke?token=jenkinswebhook
---

### SonarQube Server Showing The Bugs, Code Smells , Vulnerability etc of the Application to be Deployed.
![alt text](IMG_SCREENSHOTS/Screenshot_20260221_062333.png)

---

## Continous Delivery/Deployment Configuration in jenkins

![alt text](IMG_SCREENSHOTS/Screenshot_20260221_065923.png)
>Kubernetes secret text  "k8-token"  was that which I created alongside the service account see RBAC/rbac.md for details. Also Server Enpoint can be gitten from Aws Account. webapps namespace was also configured
![alt text](IMG_SCREENSHOTS/Screenshot_20260221_070005.png)
![alt text](IMG_SCREENSHOTS/Screenshot_20260221_070704.png)
![alt text](IMG_SCREENSHOTS/Screenshot_20260221_070736.png)

>  Install Nginx Ingress Controller to handle traffic into your cluster
```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/cloud/deploy.yaml
```
> jenkins pipeline for the Deployment
![alt text](IMG_SCREENSHOTS/Screenshot_20260221_132754.png)

>   Check for the success of ingress controller installation
```
kubectl get pods -n ingress-nginx
```
![alt text](IMG_SCREENSHOTS/Screenshot_20260221_132905.png)

> Install cert manager and check if it's running
```
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.12.0/cert-manager.yaml

kubectl get pods -n cert-manager
```
![alt text](IMG_SCREENSHOTS/Screenshot_20260221_133048.png)

> CD Pipeline for App Deployments..... SUCCESSFUL

![alt text](IMG_SCREENSHOTS/Screenshot_20260221_142037.png)
![alt text](IMG_SCREENSHOTS/Screenshot_20260221_152649.png)

> Applying ingress.yaml seperatly from the terminal and not from the pipeline
![alt text](IMG_SCREENSHOTS/Screenshot_20260221_153512.png)

> Get ingress so as to get the LB public address
```
kubectl get ingress -n webapps
```
![alt text](IMG_SCREENSHOTS/Screenshot_20260221_153724.png)

![alt text](IMG_SCREENSHOTS/Screenshot_20260221_160707.png)
>    get certificates
![alt text](IMG_SCREENSHOTS/Screenshot_20260221_162803.png)


***TROUBLESHOOT CERTIFICATE***
##### certificate Errors were due to the ingress rules in (ingress.yaml) and tls host which was www.bbprojects.space instead of the domain www.bbproject.space which was purchased from Namecheap.com and preconfigured with DNS CNAME address
![alt text](IMG_SCREENSHOTS/Screenshot_20260222_095216.png)

![alt text](IMG_SCREENSHOTS/Screenshot_20260222_095249.png)

##### After the correction www.bbproject.space works........  BRAVO!!!
![alt text](IMG_SCREENSHOTS/Screenshot_20260710_144937.png)
![alt text](IMG_SCREENSHOTS/Screenshot_20260710_152708.png)
![alt text](IMG_SCREENSHOTS/Screenshot_20260710-145248.png)

## OBSERVABILITY/MONITORING
#### using helm to install Prometheus and Grafana to monitor the entire bb-cluster
```
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts 

helm repo update
```
> Adding the repo
![alt text](IMG_SCREENSHOTS/Screenshot_20260714_030527.png)
> Updadate the repo
![alt text](IMG_SCREENSHOTS/Screenshot_20260714_035304.png)

> yaml definition for Prometheus,Grafana and exporters  observe/values.yml
![alt text](IMG_SCREENSHOTS/Screenshot_20260714_035501.png)

> helm update command to install prometheus Grafana and the exporters 
![alt text](IMG_SCREENSHOTS/Screenshot_20260714_035919.png)
```
helm upgrade --install monitoring prometheus-community/kube-prometheus-stack -f values.yaml -n monitoring --create-namespace
```
- helm upgrade --install : If the package is installed then, update it otherwise install 
- monitoring :  Release Name
- prometheus-community/kube-prometheus-stack :  This is the repo for the package
- values.yml : the yaml file where the definitions for prometheus, grafana and the exporter are which i created earlier.
- -n monitoring --create-namespace : values.yml should be deployed into monitoring namespace and if the namespace does not exist the, create it and deploy it thereafter

> "kubectl get all -n monitoring" shows that only grafana can be accessed externaly(LoadBalancer) thus the need to patch 
![alt text](IMG_SCREENSHOTS/Screenshot_20260714_040137.png)

```
kubectl patch svc monitoring-kube-prometheus-prometheus -n monitoring -p '{"spec": {"type": "LoadBalancer"}}'
kubectl patch svc monitoring-kube-state-metrics -n monitoring -p '{"spec": {"type": "LoadBalancer"}}'
kubectl patch svc monitoring-prometheus-node-exporter -n monitoring -p '{"spec": {"type": "LoadBalancer"}}'

```
![alt text](IMG_SCREENSHOTS/Screenshot_20260714_040418.png)
![alt text](IMG_SCREENSHOTS/Screenshot_20260714_040500.png)

>Grafana on http://<External-LoadBalancer-Address>
![alt text](IMG_SCREENSHOTS/Screenshot_20260714_041101.png)
> Prometheus

![alt text](IMG_SCREENSHOTS/Screenshot_20260714_041134.png)
![alt text](IMG_SCREENSHOTS/Screenshot_20260714_041420.png)
![alt text](IMG_SCREENSHOTS/Screenshot_20260714_041639.png)
![alt text](IMG_SCREENSHOTS/Screenshot_20260714_042011.png)
![alt text](IMG_SCREENSHOTS/Screenshot_20260714_042049.png)
![alt text](IMG_SCREENSHOTS/Screenshot_20260714_043144.png)
![alt text](IMG_SCREENSHOTS/Screenshot_20260714_043631.png)


#   THANK YOU.


## Some useful Kubernetes commands
```
kubectl get nodes
kubectl get namespace
kubectl get pods -n kube-system
kubectl get secret -A -n webapps
kubectl describe pod bankapp -n webapps
describe deployment bankapp -n webapps
kubectl get pods -n webapps
kubectl get pods -n cert-manager
kubectl get sa -n kube-system

```



















