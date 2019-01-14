# AWSLambdaTemplate
Simple template for setting up Lambda Functions in AWS

Note: this template.yaml file uses explicit parameters defined in the API "Path:" which need to be handled in your function code.

If you want greedy path variables and query string parameters, rather add {proxy+} at the end of the desired API "Path:"

## To Create, Test, Package & Deploy a Lambda Function

| Command                                                                                               | What it does                                                                                       |
|-------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| sudo sam init --runtime python3.7                                                                     | Setup a template folder structure                                                                  |
| Write function called app.py                                                                          | Function code                                                                                      |
| Update requirements.txt                                                                               | List dependencies to include in build                                                              |
| Update template.yaml file                                                                             | List resources/events needed                                                                       |
| sudo python -m compileall app.py                                                                      | Compile python files to generate .pyc files                                                        |
| sudo sam build --use-container                                                                        | Build code inside a Lambda-like container                                                          |
| Verify “.aws-sam/build” subdir created                                                                | Check dependencies are also there                                                                  |
| du -sh .aws-sam/build                                                                                 | Check package size (must be <250MB)                                                                |
| sudo sam local start-api                                                                              | Start local API Gateway for testing                                                                |
| Load in browser: ‘http://localhost:3000/hello’                                                        | Invoke function. Assumes GET hello here.                                                           |
| export MY_BUCKET=*<your_s3_bucket>*                                                                   | Set S3 bucket to use as variable                                                                   |
| aws s3 mb s3://$MY_BUCKET                                                                             | Create S3 bucket to upload package as ZIP                                                          |
| sudo sam package \--output-template-file packaged.yaml \--s3-bucket $MY_BUCKET                        | Package the function and upload to S3                                                              |
| export MY_STACK=*<your_stack_name>*                                                                   | Set Cloudformation Stack to use as variable                                                        |
| sudo sam deploy \--template-file packaged.yaml \--stack-name $MY_STACK \--capabilities CAPABILITY_IAM | Create a Cloudformation Stack (with API Gateway APIs and Lambda function) and deploy SAM resources |
| sudo aws cloudformation describe-stacks \--stack-name sam-app \--query 'Stacks[].Outputs'             | Retrieve API Gateway Endpoint URL                                                                  |

## To Delete the Lambda Function
| Command                                                      | What it does                                                      |
|--------------------------------------------------------------|-------------------------------------------------------------------|
| export MY_STACK=*<your_stack_name>*                          | Set Cloudformation Stack to use as variable                       |
| sudo aws cloudformation delete-stack \--stack-name $MY_STACK | Remove stack from CloudFormation (including API Gateway & Lambda) |
| export MY_PACKAGE=$(grep -o 's3.*' packaged.yaml)            | Set package as a variable                                         |
| sudo aws s3 rm $MY_PACKAGE                                   | Delete package from S3                                            |
