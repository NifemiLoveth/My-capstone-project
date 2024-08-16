# Deployment of Sock-shop Microservice-based Application on Kubernetes using IAC

<img src="images/capstone main image.png">

## Project Overview
This project aims to deploy a microservices-based application, specifically the Socks Shop, using a modern approach that emphasizes automation and efficiency. The goal is to use Infrastructure as Code (IaaC) for rapid and reliable deployment on Kubernetes.
It includes provisioning the necessary infrastructure resources on AWS, setting up a deployment pipeline, monitoring the performance and health of the application, and securing the infrastructure.

## Project Requirements
- Terraform
- AWS account
- Kubernetes
- Helm
- Prometheus
- ELK stack
- Let's Encrypt
- Socks Shop Application

## Project Objectives:
The objectives of the project are as follows:

- To eploy the Socks Shop application on a Kubernetes cluster using an Infrastructure as Code (IaC) approach
- To provision the necessary infrastructure resources on AWS, including VPCs, subnets, security groups, and EKS cluster
- To set up a deployment pipeline using GitHub Actions to build and deploy the Socks Shop application to the Kubernetes cluster
- To monitor the performance and health of the Socks Shop application using Prometheus
- To collect and analyze logs from the Socks Shop application using a centralized logging solution, such as ELK stack.

## Infrastructure Provisioning
Using terraform, we provisioned the necessary infrastructure resources on AWS, including VPCs, subnets, security groups, and EKS cluster. 
### Step 1
Download and install terraform on your machine.
Install and configure AWS CLI 

### Step 2
Create a new directory for the project and a terraform folder in it.
cd into the terraform folder

### Step 3
Run the command to initialize the Terraform project

        terraform init

### Step 4
Run the command to create an execution plan

        terraform plan

### Step 5
Run the command to apply the changes

        terraform apply --auto-approve

Below is a screenshot of my EKS cluster being provisioned by terraform
 <img src="images/capstone 2.jpg">

 Below is a picture of my nodes
  <img src="images/Capstone 3.JPG">

 ### Step 6
 To configure the kubectl to connect to the EKS cluster, the specified region and the cluster name, run the following command
   
       aws eks update-kubeconfig --region us-east-1 --name capstone-cluster

 Below is a screenshot of mine
   <img src="images/Capstone 4.JPG">
 
 ###  Step 7  
 Run the command to create namespace

    kubectl create namespace sock-shop

Below is a screenshot of mine
 <img src="images/capstone 8.jpg">  

 ### Step 8
 Set as default namespace using the following compound
    
     kubectl config set-context --current --namespace-sockshop

  ### Step 9 
  Run this command to create your deployments

     kubectl create -f .
     
Below is a screenshot of mine afterwards
    <img src="images/capstone 9.jpg">


### Step 10
Run the commands below to get your running pods and services 
    kebectl get pods
    kubectl get services

my results are displayed below
<img src="images/capstone 10.jpg">
<img src="images/Capstone 11.JPG">

To verify that the Socks Shop application is running on the Kubernetes cluster

    kubectl get all -A

### Step 11
Now, its time to use helm as the package manager for your kubenetes cluster
Download helm
Add helm repo

Install your helm ingress repo
    helm install ingress ingress/ingress-nginx

### Apply and get your ingress files using the following commands
    kubectl apply -f ingress.yaml
    kubectl get ingress

See images below

<img src="images/capstone 15.jpg">

<img src="images/capstone 16.jpg">

### Step 12
View the fontend of your deployed application through your hosted zone domain name


### Step 13
To view the secure version of your page, you need a certificate
      
    Kubectl apply -f cluster-issuer.yaml 
    kubectl apply -f ingress.yaml
    kubectl apply -f certificate.yaml
    kubectl get certificate


See image below;
<img src="images/capstone 18.png">
      
### Deployment Pipeline
The deployment pipeline was configured using a GitHub Actions workflow file, which defined the steps required to build and deploy the Socks Shop application. The workflow file was triggered by a push to the main branch of the repository, and include the following steps:

Our workflow file must be in our root directory for our GitHub Actions to detect the file automatically.

- Checkout the source code from the repository
- Build the Docker images for the Socks Shop application
- Deploy the Socks Shop application to the Kubernetes cluster
- The deployment pipeline will be configured to run automatically whenever changes are pushed to the main branch of the repository, ensuring that the Socks Shop application is always up to date and running the latest version.

### Monitoring
Prometheus was used to monitor the performance and health of the Socks Shop application. This include metrics such as request latency, error rate, and request volume. The Prometheus server was configured to scrape metrics from the Socks Shop application and store them in a time-series database. Grafana was used to visualize the metrics and create dashboards to monitor the performance and health of the application.

First create the monitoring namespace using the 00-monitoring-ns.yaml file

    kubectl create -f 00-monitoring-ns.yaml

#### Prometheus
To deploy simply apply all the prometheus manifests in any order

    kubectl apply $(ls *-prometheus-*.yaml | awk ' { print " -f " $1 } ')
The prometheus server will be exposed on Nodeport 31090 using the following command:

    kubectl port-forward service/prometheus 31090:9090 -n monitoring

<img src="images/capstone 17.png">

#### Grafana
First apply the grafana manifests

    kubectl apply $(ls *-grafana-*.yaml | awk ' { print " -f " $1 }'  | grep -v grafana-import)
Once the grafana pod is in the Running state apply the 23-grafana-import-dash-batch.yaml manifest to import the Dashboards

    kubectl apply -f 23-grafana-import-dash-batch.yaml

Grafana will be exposed on the NodePort 31300 using the following command:

    kubectl port-forward service/grafana 31300:3000 -n monitoring

<img src="images/capstone 19.png">

### Logging
We used the ELK stack to collect and analyze logs from the Socks Shop application. The ELK stack is a collection of three open-source products — Elasticsearch, Logstash, and Kibana — all developed, managed, and maintained by Elastic. The ELK Stack is used to collect, search, analyze, and visualize log data in real time.


### Security
The application was secured with HTTPS using a Let's Encrypt certificate. Let's Encrypt is a free, automated, and open certificate authority that provides free SSL/TLS certificates for websites. The certificate was used to secure the communication between the client and the Socks-Shop application, ensuring that the data is encrypted and secure.

### Conclusion
At the end of the project, you will have a fully functional deployment pipeline for the Socks Shop application, including infrastructure provisioning, monitoring, logging, and security.


# Thank you






 


