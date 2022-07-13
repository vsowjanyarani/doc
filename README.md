           AATMANI PROJECT DEVOPS DOCUMENTATION                  
## Introduction

   This documentation provides details of automatic build and deployment of the nodejs project, and the monitoring & log streaming setup.
    
## Tools used
 - Infrastructure as code - Terraform
 - Artifact Managing tools  - NPM
 - Cloud Services - AWS
 - Source Code Management tools- Github
 - Continuous integration tools-  Jenkins
 - Configuration Management tools - Ansible
 - Monitoring Tools - Prometheus,Grafana
 - Orchestration tools - Kubernetes
 - logs monitoring tools - Elasticsearch,Fluent-Bit,Kibana
 
## PRE-REQUISITES
 - one ubuntu virtual machine on aws. t3-medium
## TERRAFORM

 HashicropTerraform is an infrasture as code tool that lets us define both cloud and on-premise resources in human-readable configure files using hcl language. vpc,2subnets,eks-cluster with one nodegroup  (minimum 1 spot instance and maximum 5 spot instances) are provisioned using terraform.
 
 ![infra](https://github.com/vsowjanyarani/doc/blob/main/doc1.png?raw=true "Infra")
 
  ## Install terraform
  Refer the following link to download and install terraform in ubuntu server.
      https://www.terraform.io/downloads
  ## AWS-CLI Installation
   Refer the following link for Installing aws-cli on ubuntu server.
   https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html
   ## Provisioning The Infrastructure 
   -  terraform working directory
       -  main.tf file has terraform code to create a vpc, one public subnet and one private subnet. all environment variables used in main.tf file ,declared in another file i.e variables.tf file.
   - terraform-eks working directory
      - ekscluster.tf has terraform code to create iam roles ,eks cluster and one worker nodegroup with minimun 1 spot machine and max 5 spot machines.all environment variables used in ekscluster.tf file ,declared in another file vars.tf file.
   ## Commands used to run the terraform code
   - terraform init :- Initialize the terraform working directory .
    - terraform plan :- To view the plan of the terraform code before executing.
    - terraform apply :- To run the terraform code.
    - terraform destroy :- To destroy the infrastructure
   ## kubectl installation
 Refer the following link to  install kubectl on ubuntu server.
      https://docs.aws.amazon.com/eks/latest/userguide/install-kubectl.html  
  ## Command to connect to eks-cluster
    aws eks —region region-name update-kubeconfig —name cluster-name
 ## Namespaces in eks-cluster
   
## SourceCodeManagement
  ## git repositories-
    https://github.com/vsowjanyarani/aatmani-project - sourcecode of the project available
    https://github.com/vsowjanyarani/sowji-devops.git - all the files related to automation are available
 
## DOCKER
   Dockerfile is to building image for for node project and run it as a container.
       . ubuntu is base image for dockerfile
       . application image is exposed to port 3000
   ## DockerInstallation
Follow the below link to install docker on ubuntu server. Docker version is 20.10.17 .
                https://docs.docker.com/engine/install/ubuntu/ 
 git url to see dockerfile - https://github.com/vsowjanyarani/sowji-devops.git
##Push Image To Amazon Elastic Container Registry
   push node image to AmazonECR

## KUBERNETES
  Kubernetes is a portable, extensible, open source platform for managing containerized workloads and services.
  Containers are a good way to bundle and run your applications. In a production environment, you need to manage the containers that run the applications and ensure that there is no downtime. For example, if a container goes down, another container needs to start.
  ## HElM
    Helm is the package manager for kubernetes,that is chart. Helm charts are available in helm repositories.
   ## Helm Installation
Refer the following link to install helm on ubuntu server
https://helm.sh/docs/intro/install/
 
  ## Creating Helm Chart
Refer the following link for creating helm chart for node application.
    https://phoenixnap.com/kb/create-helm-chart
    Commands to create helm chart for the application
- helm create node-helm
- created dev-values.yaml ,qa-values.yaml and prod-values.yaml
- dev-values.yaml file has replicas-1, repository url is node image location in ECR and service type is NodePort with exposed port 3000.
- qa-values.yaml file has replicas-1, repository url is node image location in ECR and service type is NodePort with exposed port 3000.
- prod-values.yaml file has replicas-2, repository url is node image location in ECR and service type is NodePort with exposed port 3000.
 ## Installing helmchart of node applicatiom in diffrent environments dev,qa,prod. 
- helm install dev-helm nodehelm/ -n dev -f dev-values.yaml
 - helm install qa-helm nodehelm/ -n qa -f qa-values.yaml
- helm install prod-helm nodehelm/ -n prod -f prod-values.yaml
3 classic loadbalancers got created.

## Continuous Build and Continuous Deployment-Jenkins

 The leading open source automation server, Jenkins provides hundreds of plugins to support building, deploying and automating any project
 ## jenkinsInstallation:
  Refer the follwing link to install jenkins
   https://www.jenkins.io/doc/book/installing/linux/
   
 JenkinsPipelinejobs-
- dev-jenkinsfile -   run a script which will pull node image from ECR and run the docker buid , upload the image to ECR. git integration with jenkins for trigger automaticaly when there is merge happens to main branch. integrated to slack channel to get alert on job fails.
- qa-jenkinsfile -    run a script which will pull node image from ECR and run the docker buid , upload the image to ECR. git integration with jenkins for trigger automaticaly when there is merge happens to main branch. integrated to slack channel to get alert on job fails.
- prod-jenkinfile -    run a script which will pull node image from ECR and run the docker buid , upload the image to ECR. git integration with jenkins for trigger automaticaly when there is merge happens to main branch. integrated to slack channel to get alert on job fails.

## METRICSERVER
   The Kubernetes Metrics Server is an aggregator of resource usage data in your cluster, and it is not deployed by default in Amazon EKS clusters. The Metrics Server is commonly used by other Kubernetes add ons, such as the Horizontal Pod Autoscaler or the Kubernetes Dashboard. 
   INSTALLATION:
     Refer the following link
     https://docs.aws.amazon.com/eks/latest/userguide/metrics-server.html
## AWS INGRESS CONTROLLER
Kubernetes Ingress is an API resource that allows you manage external or internal HTTP(S) access to Kubernetes services running in a cluster.The open source AWS ALB Ingress controller triggers the creation of an ALB and the necessary supporting AWS resources whenever a Kubernetes user declares an Ingress resource in the cluster.
  The Ingress resource uses the ALB to route HTTP(S) traffic to different endpoints within the cluster. The AWS ALB Ingress controller works on any Kubernetes cluster including Amazon Elastic Kubernetes Service (Amazon EKS).
  
  Refer the following link to deploy awsingresscontroller
  https://aws.amazon.com/blogs/opensource/kubernetes-ingress-aws-alb-ingress-controller/
  
 ## CLUSTER AUTOSCALING
 The Kubernetes Cluster Autoscaler automatically adjusts the number of nodes in your cluster when pods fail or are rescheduled onto other nodes.
 Deploying the cluster autoscaler using the folllowing link
  https://docs.aws.amazon.com/eks/latest/userguide/autoscaling.html
 ## Enabling hpa in prod environment
 A HorizontalPodAutoscaler (HPA for short) automatically updates a workload resource (such as a Deployment or StatefulSet), with the aim of automatically scaling the workload to match demand.
 Horizontal scaling means that the response to increased load is to deploy more Pods.
 ## PROMETHEUS AND GRAFANA
 Prometheus is an opensource systems monitoring and alerting tool kit. It collects and stores metrics as time series data. PromQL is the query language can be used to query the metrics.
 
 Grafana is multi-platform open source and allows to visualize the data stored in Prometheus (and other sources). Grafana provides charts,graphs,alerts of the application when connected to supported datasource.
 
  ## Components Used In Prometheus Server
  NodeExporter -its a deamonset ,it runs on every node in the eks cluster and stores metrics of the node in the prometheus server.
  
  AlertManager- it will handle the alerts.
  ## Installation
  Create a namespace monitoring in eks-cluster and deploy Promethues and grafana using promethushelm repository and grafanahelm repository from helm community.
  Refer the below links
  https://artifacthub.io/packages/helm/prometheus-community/prometheus
  https://artifacthub.io/packages/helm/grafana/grafana
  ## Prometheus SetUp
  Step 1: Create alerting rules in Prometheus
  - rules.yml - to specify alerting rules(Rules used are Node down,Instance down,kube pod crash looping,low memory)
  - prometheus.yml - promethues configuration file
  -  configuring rules to promethueus.yml file
  
   Step 2: Set up Alertmanager
  - alertmanager.yml - integrate slack channel(#testing) to prometheus alerts in alertmanager.yml file
  
  ## Grafana SetUp
 - setup logindetails for grafana
 - integrate the prometheus to grafana by creating datasource with promethues url 
 - create a dashboard using existing prometheus import code.
 ## LOGGING
## ELASTICSEARCH ,FLUENT-BIT AND KIBANA (EFK)
   When running multiple services and applications on a Kubernetes cluster, a centralized, cluster-level logging stack can help you quickly sort through and analyze the heavy volume of log data produced by your Pods. One popular centralized logging solution is the Elasticsearch, Fluentbit, and Kibana (EFK) stack.
  ## ElasticSearch
