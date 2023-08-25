rm ~/.local/bin/aws
bash
aws sts get-caller-identity --query Arn | grep eks-blueprints-for-terraform-workshop-admin -q && echo "IAM role valid" || echo "IAM role NOT valid"
find . -type f -name "main.tf" -print0 | xargs -0 sed -i'' -e 's/ref=v4.32.0/ref=v4.32.1/g'
