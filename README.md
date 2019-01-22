# AWSLambdaTemplate
Simple template for setting up Lambda Functions in AWS using Ubuntu 16.04 LTS

Note: this template.yaml file returns greedy path variables and query string parameters, by using proxy integration via {proxy+} at the end of the desired API "Path:"

## To Create, Test, Package & Deploy a Lambda Function

| Steps to perform                                                                                      | What it does                                                                                       |
|-------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| sam init --runtime python3.7                                                                     | Setup a template folder structure                                                                  |
| Write function called app.py                                                                     | Function code                                                                                      |
| Update requirements.txt                                                                          | List dependencies to include in build                                                              |
| Update template.yaml file                                                                        | List resources/events needed                                                                       |
| python -m compileall app.py                                                                      | Compile python files to generate .pyc files                                                        |
| sam build --use-container                                                                        | Build code inside a Lambda-like container                                                          |
| Verify “.aws-sam/build” subdir created                                                           | Check dependencies are also there                                                                  |
| du -sh .aws-sam/build                                                                            | Check package size (must be <250MB)                                                                |
| sam local start-api                                                                              | Start local API Gateway for testing                                                                |
| Load in browser: ‘http://localhost:3000/calc’                                                    | Invoke function. Assumes GET hello here.                                                           |
| export MY_BUCKET=*<your_s3_bucket>*                                                              | Set S3 bucket to use as variable                                                                   |
| aws s3 mb s3://$MY_BUCKET                                                                        | Create S3 bucket to upload package as ZIP                                                          |
| sam package \--output-template-file packaged.yaml \--s3-bucket $MY_BUCKET                        | Package the function and upload to S3                                                              |
| export MY_STACK=*<your_stack_name>*                                                              | Set Cloudformation Stack to use as variable                                                        |
| sam deploy \--template-file packaged.yaml \--stack-name $MY_STACK \--capabilities CAPABILITY_IAM | Create a Cloudformation Stack (with API Gateway APIs and Lambda function) and deploy SAM resources |
| aws cloudformation describe-stacks \--stack-name sam-app \--query 'Stacks[].Outputs'             | Retrieve API Gateway Endpoint URL                                                                  |

## To Delete the Lambda Function
| Steps to perform                                             | What it does                                                      |
|--------------------------------------------------------------|-------------------------------------------------------------------|
| export MY_STACK=*<your_stack_name>*                     | Set Cloudformation Stack to use as variable                       |
| aws cloudformation delete-stack \--stack-name $MY_STACK | Remove stack from CloudFormation (including API Gateway & Lambda) |
| export MY_PACKAGE=$(grep -o 's3.*' packaged.yaml)       | Set package as a variable                                         |
| aws s3 rm $MY_PACKAGE                                   | Delete package from S3                                            |
