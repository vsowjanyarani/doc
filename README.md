                                    NODEJS PROJECT DEVOPS DOCUMENTATION             
## INTRODUCTION

   This documentation provides details of automatic build and deployment of the nodejs project, and the kubernetes monitoring & log streaming setup.
 ## NodeJs Application Devops Architecture:
 ![cicd](https://github.com/vsowjanyarani/doc/blob/main/main-image.png?raw=true"ci-cd")
    
## TOOLS USED
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
 - one ubuntu virtual machine on aws of type t3-medium
 - one ubuntu virtual machine on aws of type t2-medium for installing elasticsearch and kibana
# STAGE1
## TERRAFORM

 HashicropTerraform is an infrasture as code tool that lets us define both cloud and on-premise resources in human-readable configure files using hcl language. 
 one vpc,2subnets,eks-cluster with one nodegroup  (minimum 1 spot instance and maximum 5 spot instances) are provisioned using terraform.
 
 ![infra](https://github.com/vsowjanyarani/doc/blob/main/Untitled%20Diagram.drawio.png?raw=true"Infra")
 
  ### Install terraform
  Refer the following link to download and install terraform in ubuntu server.
  
      https://www.terraform.io/downloads
 
   ### Provisioning The Infrastructure 
   -  terraform working directory
       -  main.tf file has terraform code to create a vpc, one public subnet and one private subnet
       -  variables.tf has all environment variables used in main.tf file 
   - terraform-eks working directory
      - ekscluster.tf has terraform code to create iam roles ,eks cluster and one worker nodegroup with minimun 1 spot machine and max 5 spot machines
      - vars.tf file has all environment variables used in ekscluster.tf
   ### Commands used to run the terraform code
      terraform init :-    Initialize the terraform working directory .
      terraform plan :-    To view the plan of the terraform code before executing.
      terraform apply :-   To run the terraform code.
      terraform destroy:-  To destroy the infrastructure
    
## AWS-CLI INSTALLATION
   Refer the following link for Installing aws-cli on ubuntu server
   
    https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html
 ## SOURCE CODE MANAGEMENT
  GIthub offers the distributed version control and source code management (SCM) functionality of Git.For every change made to git repo , PR will raised and team lead has to review it and merge into it.

   ![pullrequestmerge](https://github.com/vsowjanyarani/doc/blob/main/git-pr.png?raw=true"sourcecodemanagement")
  ### Git Repositories-
   https://github.com/vsowjanyarani/aatmani-project - the sourcecode of the project available in this repository.
   https://github.com/vsowjanyarani/sowji-devops.git - all the files related to devops automation are available in this repository.

# STAGE2
   ## kUBECTL INSTALLATION
 Refer the following link to  install kubectl on ubuntu server.
      https://docs.aws.amazon.com/eks/latest/userguide/install-kubectl.html  
  
 ### Namespaces in eks-cluster
   3 namespaces for 3 diffirent environments
      1. dev
      2. qa
      3. prod
    ![dev-qa-prod](https://github.com/vsowjanyarani/doc/blob/main/dev-qa-prod.png?raw=true"dev-qa-prod")
    
# STAGE3
## DOCKER
   Dockerfile is to building image for for node project and run it as a container.
   
       . ubuntu is base image for dockerfile
       
       . nodejs application image is exposed to port 3000
       
     ![dockerfile](https://github.com/vsowjanyarani/doc/blob/main/docker.png?raw=true"dockerfile")
   ### DockerInstallation
Follow the below link to install docker on ubuntu server. Docker version is 20.10.17 .
                
                https://docs.docker.com/engine/install/ubuntu/ 
                
 git url to see dockerfile - https://github.com/vsowjanyarani/sowji-devops.git
 
# STAGE4
## CONTINUOUS BUILD AND CONTINUOUS DEPLOYMENT - JENKINS

 The leading open source automation server, Jenkins provides hundreds of plugins to support building, deploying and automating any project
 ### jenkinsInstallation:
  Refer the follwing link to install jenkins  
  
   https://www.jenkins.io/doc/book/installing/linux/
   
 ##### JenkinsPipelinejobs-
 dev-jenkinsJob -  
          This job has pipeline script to build an image and run the image,upload the image to ECR,integrated git with jenkins for trigger automaticaly when there is merge happens to main branch,Integrated to slack channel to get alert on job fails and deployed 1 replica with helm install and dev-values.yaml file.
          
qa-jenkinsJob - 
         This job has pipeline script which will pull node image that created in dev-jenkinsjob from ECR ,attaching new tag and upload the image to ECR. Integrated to git with jenkins to trigger automaticaly when there is merge happens to main branch ,integrated to slack channel to get alert on job fails and deployed 1 replica with helm install and qa-values.yaml file.
         
 prod-jenkinJob -  
         This job has pipeline script which will pull node image that created in qa environment from ECR and attaching new tag and upload the image to ECR. Integrated git with jenkins to trigger automaticaly when there is merge happens to main branch,integrated to slack channel to get alert on job fails and and deployed 1 replica with helm install and dev-values.yaml file.

![jenkins](https://github.com/vsowjanyarani/doc/blob/main/jenkinsjob3.png?raw=true"jenkinsjobs")


# STAGE5
## KUBERNETES
  Kubernetes is a portable, extensible, open source platform for managing containerized workloads and services.
  Containers are a good way to bundle and run your applications. In a production environment, you need to manage the containers that run the applications and ensure that there is no downtime. For example, if a container goes down, another container needs to start.
  ### HELM
   Helm is the package manager for kubernetes. Helm charts are available in helm repositories.
   ##### Helm Installation
Refer the following link to install helm on ubuntu server

https://helm.sh/docs/intro/install/
 
  ##### Creating Helm Chart
Refer the following link for creating helm chart for node application.

    https://phoenixnap.com/kb/create-helm-chart
- created dev-values.yaml ,qa-values.yaml and prod-values.yaml 

- dev-values.yaml file has replicas-1, repository url is node image location in ECR and service type is NodePort with exposed port 3000

- qa-values.yaml file has replicas-1, repository url is node image location in ECR and service type is NodePort with exposed port 3000

- prod-values.yaml file has replicas-2, repository url is node image location in ECR and service type is NodePort with exposed port 3000
- 
![helmchart](https://github.com/vsowjanyarani/doc/blob/main/Untitled%20Diagram.drawio%20(1).png?raw=true"helmchart")


## METRICSERVER
   The Kubernetes Metrics Server is an aggregator of resource usage data in your cluster, and it is not deployed by default in Amazon EKS clusters. The Metrics Server is commonly used by other Kubernetes add ons, such as the Horizontal Pod Autoscaler or the Kubernetes Dashboard. 
   INSTALLATION:
     Refer the following link
     
     https://docs.aws.amazon.com/eks/latest/userguide/metrics-server.html
  
 ## CLUSTER AUTOSCALING
 The Kubernetes Cluster Autoscaler automatically adjusts the number of nodes in your cluster when pods fail or are rescheduled onto other nodes.
 Deploying the cluster autoscaler using the folllowing link
 
  https://docs.aws.amazon.com/eks/latest/userguide/autoscaling.html
  
 #### Enabling hpa in prod environment
 A HorizontalPodAutoscaler (HPA) automatically updates a workload resource (such as a Deployment or StatefulSet), with the aim of automatically scaling the workload to match demand.
 Horizontal scaling means that the response to increased load is to deploy more Pods. 
 HPA is enabled in prod environment by increase and decrease the number of replicas (by updating the Deployment) to maintain an average CPU utilization across all Pods of 50%, minimum 1 instance and maximum 10 instances.
 
 # STAGE6
 ## PROMETHEUS AND GRAFANA
 Prometheus is an opensource systems monitoring and alerting tool kit. It collects and stores metrics as time series data. PromQL is the query language can be used to query the metrics.
 
 Grafana is multi-platform open source and allows to visualize the data stored in Prometheus (and other sources). Grafana provides charts,graphs,alerts of the application when connected to supported datasource.
 ![prom](https://github.com/vsowjanyarani/doc/blob/main/prome.png?raw=true:"prometheus")
 
  #### Components Used In Prometheus Server
  NodeExporter -its a deamonset ,it runs on every node in the eks cluster and stores metrics of the node in the prometheus server database.
  
  AlertManager- it will handle the alerts rules.
  #### Installation
  Create a namespace monitoring in eks-cluster and deploy Promethues and grafana using promethushelm repository and grafanahelm repository from helm community.
  Refer the below links
  
  https://artifacthub.io/packages/helm/prometheus-community/prometheus
  
  https://artifacthub.io/packages/helm/grafana/grafana
  #### Prometheus SetUp
  Step 1: Create alerting rules in Prometheus
  - rules.yml - to specify alerting rules(Rules used are Node down,Instance down,kube pod crash looping,low memory)
  - prometheus.yml - promethues configuration file
  -  configuring rules.yml file to promethueus.yml file
  
   Step 2: Set up Alertmanager
  - alertmanager.yml - integrate slack channel(#testing) to prometheus alerts in alertmanager.yml file
  
  #### Grafana SetUp
 - setup logindetails for grafana
 - integrate the prometheus to grafana by creating datasource with promethues url 
 - create a dashboard using existing prometheus import code.
 # STAGE7
 ## KUBERNETES LOGGING
#### ELASTICSEARCH ,FLUENT-BIT AND KIBANA (EFK)
   Securely and reliably search, analyze, and visualize your data.
   
   ![efk](https://github.com/vsowjanyarani/doc/blob/main/efk.png?raw=true:"EFK")
   

 ### ELASTICSEARCH
   Elasticsearch is a distributed, open-source data source,search and analytics engine built on Apache Lucene and developed in Java.
#### ElasticSearch installation
   Refer the following link for installation on ubuntu server
   
       https://linuxize.com/post/how-to-install-elasticsearch-on-ubuntu-20-04/
       
 ### FLUENT-BIT
 Fluent Bit is a lightweight log processor and forwarder that allows you to collect data and logs from different sources, unify them, and send them to multiple destinations.
   Here fluentbit stores the log data to elasticsearch data source. fulent-bit runs as a deamonset in kubernetes cluster.so that it can collect log information from every node
 #### Fluent-Bit Installation:
  Refer the following link for installation on ubuntu server
  
  https://docs.fluentbit.io/manual/v/1.3/installation/kubernetes
  
 ### KIBANA
 Kibana is a data visualization and exploration tool used for log and time-series analytics, application monitoring.
 kibana takes the log data from elasticsearch and visualized in kibana web UI.
 #### Kibana Installation:
 Refer the following link for installation on ubuntu server
 
 https://phoenixnap.com/kb/how-to-install-elk-stack-on-ubuntu
 
 ### Enable Basic Authentication on EFK Stack
 This enables it to restrict access to various resources within the cluster. To access these resources when authentication is enabled, a user has to prove their identity using username/passwords.
 Refer the following link
 
 https://kifarunix.com/how-to-enable-basic-authentication-on-elk-stack/
