       AATMANI PROJECT BUILD AND DEPLOYMENT AUTOMATION                        
## Introduction

   This documentation tells automatic build and deployment of the nodejs project.Finally monitoring the application in grafana and log streaming.
    
## Tools used
 - Infrastructure as code - Terraform
 - Artifact Managing tools  - NPM
 - Cloud Services - AWS
 - Source Code Management tools- Github
 - Continuous integration tools-  Jenkins
 - Configuration Management tools - Ansible
 - Monitoring Tools - Prometheus,Grafana
 - Orchestration tools - Kubernetes, Nomad, Docker
 - logs monitoring tools - Elasticsearch,Fluent-Bit,Kibana
 
## PRE-REQUISITES
 - one ubuntu virtual machine on aws. t3-medium
## TERRAFORM

 HashicropTerraform is an infrasture as code tool that lets us define both cloud and on-premise resources in human-readable configure files using hcl language. The main goal of using terraform is evaluating the existing terraform code i.e reusing the code  and the automation of infrastructure. 
  ## Install terraform
  Refer the following link to download and install terraform in ubuntu server.
      https://www.terraform.io/downloads
  ## AWS-CLI Installation
   Refer the following link for Installing aws-cli on ubuntu server.
   https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html
  ## command to configure aws account on ubuntu server
      aws configure
   ## Provisioning The Infrastructure 
   -  terraform working directory
       -  main.tf file has terraform code to create a vpc, one public subnet and one private subnet. all environment variables used in main.tf file ,declared in another file variables.tf file.
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
  ## Commands to connect to eks-cluster
    aws eks —region region-name update-kubeconfig —name cluster-name
 ## Commands to create 3 namespaces in eks-cluster
    kubectl create namespace dev
    kubectl create namespace qa
    kubectl create namespace prod
## SourceCodeManagement
  ## git repositories-
    https://github.com/vsowjanyarani/aatmani-project - sourcecode of the project available
    https://github.com/vsowjanyarani/sowji-devops.git - all the files related to automation are available
 
## DOCKER
   Dockerfile is to building image for for node project and run it as a container.
   The following commands are used to create Dockerfile.
      . FROM ubuntu - base image for dockerfile
