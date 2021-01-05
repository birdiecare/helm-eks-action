# Helm EKS Action
A Github Action for executing Helm commands on EKS using the AWS CLI.  
It can also run AWS, kubeclt and SOPs commands.

This action was inspired by [helm-eks-action](https://github.com/koslib/helm-eks-action). 
We've adapted the action to allow for more granular control over the package versions installed.

# Instructions

Use this as a Github Action step as follows:

- You have to authenticate against an AWS account to be able to access an EKS.

## Example
```yaml
name: deploy

on:
    push:
        branches:
            - staging

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2

      - name: AWS Credentials
        uses: aws-actions/configure-aws-credentials@v1
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: us-west-2
          
      - name: Login to Amazon ECR
        id: login-ecr
        uses: aws-actions/amazon-ecr-login@v1
        
      - name: helm deploy
        uses: birdiecare/helm-eks-action@v1
        env:
          KUBE_CONFIG_DATA: ${{ secrets.KUBE_CONFIG_DATA }}
        with:
          command: |
          sops helm upgrade <release name> --install --wait <chart> -f <path to values.yaml>
```

# Secrets

Create a GitHub Secret for each of the following values:

- `KUBE_CONFIG_DATA`  
  Your kube config file in base64-encrypted form. You can do that with  
  `cat $HOME/.kube/config | base64`

- `AWS_ACCESS_KEY_ID`
- `AWS_SECRET_ACCESS_KEY`
