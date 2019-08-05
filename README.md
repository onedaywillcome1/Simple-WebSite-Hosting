# Hosting a static website on S3
This project creates simple website hosting via S3. The cloudformation script which is "stack.yml" creates S3 bucket, configure it as website hosting 

## Prerequisities
- Install the Amazon Cli
- Export AWS credentials, cloudformation stack name and s3 website hosting domain name as a parameter
  - `export AWS_ACCESS_KEY_ID=<YOUR_AWS_ACCESS_KEY_ID>`
  - `export AWS_SECRET_ACCESS_KEY=<YOUR_AWS_SECRET_KEY>`
  - `export AWS_DEFAULT_REGION=us-east-1`
  - `export AWS_ACCOUNT_ID=$(aws sts get-caller-identity --output text --query 'Account')`
  - `export STACK_NAME=vngrswebsitehosting`
  - `export DOMAIN_NAME=vngrsdemowebsite-$AWS_ACCOUNT_ID`


## Infrastructure

To build the infrastructure use the following command:

    aws cloudformation create-stack --region $AWS_DEFAULT_REGION --template-body file://$PWD/stack.yml --stack-name $STACK_NAME --parameters ParameterKey=DomainName,ParameterValue=$DOMAIN_NAME

To update the bucket:

    aws cloudformation update-stack --region $AWS_DEFAULT_REGION --template-body file://$PWD/stack.yml --stack-name $STACK_NAME --parameters ParameterKey=DomainName,ParameterValue=$DOMAIN_NAME

To destroy the infrastructure:

    aws s3 rm s3://$DOMAIN_NAME --recursive
    aws cloudformation delete-stack --stack-name $STACK_NAME

## Deployment

After Cloudformation stack created, Upload the contents of the html files into the bucket:
    
    aws s3 cp --acl public-read --recursive code/ s3://$DOMAIN_NAME/

## Getting URL of static website
Execute following command to retrieve website output:

    aws cloudformation describe-stacks --stack-name $STACK_NAME --query "Stacks[0].Outputs[?OutputKey=='WebsiteURL'].OutputValue" --output text
    
Sample URL: `http://vngrsdemowebsite-111111111.s3-website-us-east-1.amazonaws.com`
    