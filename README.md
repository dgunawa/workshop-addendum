
# Welcome to FedCiv Learning Days - Simplify EKS


## Please run the following commands in your c9 ide when instructed to update the workshop content to match today's abridged agenda.


### Make sure we're using the 2.x version of the AWS cli
    rm ~/.local/bin/aws
    bash
    aws --version
This should produce an output like "aws-cli/2.13.13 Python/3.11.4 Linux/4.14.322-244.536.amzn2.x86_64 exe/x86_64.amzn.2 prompt/off".  Let your presenter know if you didn't get that.


### Make sure you've been given the correct IAM role
    aws cloud9 update-environment  --environment-id $C9_PID --managed-credentials-action DISABLE
    aws sts get-caller-identity --query Arn | grep eks-blueprints-for-terraform-workshop-admin -q && echo "IAM role valid" || echo "IAM role NOT valid"
These commands should produce an output of "IAM role valid".  Let your presenter know if you get something else.

### Update the version of ...
    find . -type f -name "main.tf" -print0 | xargs -0 sed -i'' -e 's/ref=v4.32.0/ref=v4.32.1/g'

### Comment out a few things in our Terraform code that we'll re-enable later in the workshop
    find . -type f -name "main.tf" -print0 | xargs -0 sed -i'' -e 's/\(workloads = local.workload_application\)/#\1/g'
    find . -type f -name "locals.tf" -print0 | xargs -0 sed -i'' -e 's/\(karpenterInstanceProfile = module.karpenter.instance_profile_name\)/#\1/g'

### Update the kubernetes-addon source to sidestep a minor bug in our environment setup
use the following source url in module "kubernetes_addons".

source = "github.com/naredhim/terraform-aws-eks-blueprints?ref=v5.0.1/modules/kubernetes-addons"
