# Serverless Engineering Pipeline for NLP


This project emulates the example in Professor Noah Gift's Lecture on creating a serverless engineering pipeline on AWS Lambda
https://www.youtube.com/watch?v=zXxdbtamoa4

The Pipeline has six main parts to it:
* Producer Lambda Function
* Create DynamoDB Table
* Cloudwatch Trigger for Producer Lambda Function
* Consumer Lambda Function
* Create S3 Bucket to store results
* SQS Trigger for Consumer Lambda Function

To make sure everything runs properly, all the elements of the pipeline must be created in the same region

## Part 1: Creating Lambda Functions and Associated Triggers
### Step 1:
Create an environment in AWS Cloud 9 with the default configurations

### Step 2: 
Once the environment is created, go to the right corner tab 'AWS Resources' and click on the \lambda+ sign to create a new lambda function.
While setting up the Lambda function, make sure that you have the following parameters setup:
* runtime: python3.6
* function trigger: none
* role: (do not automatically generate role here, instead follow step 2a to create your own role with administrative access) choose an existing role
* existing role: the role in which you had created in Step 2a
Do this for both producer and consumer lambda functions

### Step 2a:
to create a new role, go to the Console and search for IAM
* click on 'Create Role'
* Choose a use case: in this case its 'Lambda', and click 'Next:Permissions'
* At the 'Attach Policy Permissions' tab, choose 'AdministratorAccess' and finally give this role a name

### Step 3:
The code is as provided in the repository: make sure that the following variables/parameters are changed in the lambda_function.py 
* TABLE: this refers to the table name that you'll be setting up in DynamoDB 
* QUEUE: this refers to the queue name that you'll be setting up in SQS Management
Both will be explained in Step 4

### Step 4:
To access DynamoDB, go to AWS Console and search for 'DynamoDB'.
* Go to the 'Tables' tab and 'Create Table': name your table accordingly - in this case, I have named my table 'company' with primary key 'guid'. You can add the names of company that you are interested in by clicking on the table name and at the 'Items' tab, 'Create Items'

To access SQS, similarly, search for 'SQS' at the AWS Console. Create a queue and in this case, I have named my queue 'producer'

### Step 5
Before, we deploy, we will also have to amend the consumer function to reflect the following:
* region: whichever region in which your lambda function resides in
* relevant S3 bucket name that the results will be stored in (explained in Step 5a) - changes to the final line of lambda_function.py consumer code

### Step 5a
To create a S3 bucket, search for S3 in AWS Console and 'Create Bucket', with default parameters and a name of your choice

### Step 6
Now, we will deploy the Lambda Functions. Before we do that, we'll have to ```cd``` to the folder in which the lambda functions are at, and do ```source ../venv/bin/activate/``` then ```pip3 install python-json-logger --target ../ ```, and the same for ```boto3``` for the producer function. Then we'll deploy the functions by right clicking on the lambda function upload icons on the right. The process is the same for the consumer function, where we'll have to do ```pip3 install``` for ```python-json-logger```, ```boto3```, ```wikipedia``` and ```pandas``` at the folder for the consumer function. Now we can deploy the consumer function as well

### Step 6
Now, we can go to the Lambda function dashboard (Search for Lambda at AWS console), and add the triggers for the producer and consumer functions. 
* for producer, the trigger is based on 'CloudWatch Events', and create a new rule with 'rate(1 minute)' since this is for development purposes
* for consumer, the trigger is based on 'SQS', and choose the queue that has been created in Step 4

### Step 7
You should now see that the queue which takes in the data from DynamoDB is moving, and the results are reflected and downloadable in S3



