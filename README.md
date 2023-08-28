
# Welcome to FedCiv Learning Days - Simplify EKS
| Agenda   |     |
| ----------- | ----------- |
| Account access, begin EKS deployment      | David Henry       |
| Provision EKS Cluster and Explain about the Workshop and addons |   Naresh Dhiman |
| Deploy Simple Workload   | Dave Gunawan        |
|Autoscaling with Karpenter | Naresh Dhiman |
| (extra credit) EKS Observability | David Henry|


## PRE STEPS
### Get access to your temporary environment
One-time login link: TBD

### Run these commands in your Cloud9 terminal
Please run the following commands in your c9 ide when instructed to update the workshop content to match today's abridged agenda.


    cd ~/environment
    #install the c9 cli tool
    npm install -g c9
    
    #Make sure we're using the 2.x version of the AWS cli  
    rm ~/.local/bin/aws
    bash
    aws --version
    #This should produce an output like "aws-cli/2.13.13 Python/3.11.4 Linux/4.14.322-244.536.amzn2.x86_64 exe/x86_64.amzn.2 prompt/off".  Let your presenter know if you didn't get that.

    #Make sure you've been given the correct IAM role
    aws cloud9 update-environment  --environment-id $C9_PID --managed-credentials-action DISABLE
    aws sts get-caller-identity --query Arn | grep eks-blueprints-for-terraform-workshop-admin -q && echo "IAM role valid" || echo "IAM role NOT valid"
    #These commands should produce an output of "IAM role valid".  Let your presenter know if you get something else.

    #Switch to a forked version of kubernetes-addons to sidestep some issues with workshop content designed for a version of Terraform that is no longer available
    find . -type f -name "main.tf" -print0 | xargs -0 sed -i'' -e 's/github\.com\/aws\-ia\/terraform\-aws\-eks\-blueprints?ref\=v4\.32\.0\/modules\/kubernetes\-addons/github.com\/naredhim\/terraform-aws-eks-blueprints?ref=v5.0.1\/modules\/kubernetes-addons/g'

    #Comment out a workload that we'll re-enable later in the workshop
    find . -type f -name "main.tf" -print0 | xargs -0 sed -i'' -e 's/\(workloads = local.workload_application\)/#\1/g'


## Deploying EKS
 1. Follow the instructions for the "[Optional] - Skip Manual Cluster Provisioning" section https://catalog.workshops.aws/eks-blueprints-terraform/en-US/040-optional-skip-cluster-setup 
 2. Since our time together is limited, please use our GitHub account name `FedCivLearningDays-SimplifyEKS` in place of your own
 3. Come back here once you finish that page

### Run this after Terraform apply for VPC and Cluster
    aws eks --region $AWS_REGION update-kubeconfig --name eks-blueprint-blue

## Deploy a simple workload 
 1. Deploy a simple workload with the instructions at https://catalog.workshops.aws/eks-blueprints-terraform/en-US/050-dev-team-deploy-workload/1-add-app-to-workloads-repo .  We skipped the steps showing how to access your ArgoCD server so use the below commands for access.

Get the url for your ArgoCD server

    ARGOCD_SERVER=$(kubectl get svc argo-cd-argocd-server -n argocd -o json | jq --raw-output '.status.loadBalancer.ingress[0].hostname')
    echo "ArgoCD URL: http://$ARGOCD_SERVER"
    
Get the admin password for your ArgoCD server (username is 'admin')

    ARGOCD_PWD=$(aws secretsmanager get-secret-value --secret-id argocd-admin-secret.eks-blueprint | jq -r '.SecretString')
    echo export ARGOCD_PWD=\"$ARGOCD_PWD\" >> ~/.bashrc
    echo "ArgoCD admin password: $ARGOCD_PWD"


## Autoscaling with Karpenter

Follow the instructions in the section on Autoscaling with Karpenter

https://catalog.workshops.aws/eks-blueprints-terraform/en-US/061-autoscaling-karpenter

## (Extra Credit) EKS Observability

Follow the instructions in this blog on Amazon EKS Observability Accelerator

https://aws.amazon.com/blogs/mt/introducing-amazon-eks-observability-accelerator/
