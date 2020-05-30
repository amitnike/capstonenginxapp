# AWS Cloud Devops Nanodegress Project -: capstonenginxapp
Sample Nginx Hello World App

Step 1: Propose and Scope the Project
      The project has jenkins pipeline which will perform below activities -:
      Checkout project from git repo
      Build the docker image and upload it to docker HUB
      Deploy the image to AWS EKS cluster
      Perform blue green deployment

The sample application is nginx Hello world application.

Step 2: Use Jenkins, and implement blue/green or rolling deployment.
      Jenkins is installed on EC2 instances along with necessary plugin on it
      Integrated with my GIT HUB repo.
      Jenkins instance also had installation of AWSCLI , docker , EKSCTL , Tidy and other reuired plugins

Step 3: Pick AWS Kubernetes as a Service, or build your own Kubernetes cluster.
      AWS Infrastructure is setup with AWS Cloudformation. It has dedicated VPC, a new ELS cluster 
      and self maanged worker nodes
      The files to setup AWS infra are inside foder 'aws-eks-setup'.
      First run create-vpc.sh , then create-eks.sh and then followed by create-workernodes.sh.
       
Step 4: Build your pipeline
     As above mentioned , project has jenkinsfile and dockerfile
     Also the K8 blue-green deployment files checked in too.
     The Images folder having all screenhots of pipeline results
      
Step 5: Test your pipeline
    The resuls of AWS Infra as well as jenkins pipeline are in Images folder
    
# Manual activies performed -:
Update to create-workernodes.sh file with EKS ID and security group
Update to aws-auth-cm.yaml file with ARN of Node group Instance role
Set auto assign IPV4 settings to true for newly created subnets in new VPC
Jenkins instance to have docker usergroup configurations
Update security group of worker nodes to allow traffic from Load balancer security group

