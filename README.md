# serverless-architecture-with-AWS-Lambda
This is a serverless architecture with AWS lambda. The walkflow is shown below.  

<img width="1263" alt="walkthrough" src="https://user-images.githubusercontent.com/44473421/113493108-992ac400-94aa-11eb-9a58-31853e463541.png">


## Reference
Source code: [github.com/noahgift/awslambda](https://github.com/noahgift/awslambda)  

## How to build from scratch
To build this sesrverless architecture on AWS from scratch, you can follow these steps:

### Create DynamoDB table, SQS queue, S3 bucket & working environment
Open AWS DynamoDB console, create a table called "ffang"(or whatever you want). For the primary key, use "name" with type "string".  
Click on the newly created table, add items with name like "Apple", "Google" and etc.  
Open AWS SQS console, create a standard queue called "checkDB"(or whatever your want).     
Open AWS S3 console, create a bucket called "faangsentiment"(or whatever you want).  
Open AWS Cloud9 console, open the IDE of your working environment, create a new directory for this project and cd into it.  

### Create producer-side lambda function
The first lambda function reads data from DynamoDB table, and puts messages into SQS. It is triggered by a cloudwatch timer.  

#### Create SAM application
<img width="1279" alt="create lambda" src="https://user-images.githubusercontent.com/44473421/113493040-1f92d600-94aa-11eb-98a3-d485305cc1b4.png">

In Cloud9 IDE, click on "AWS", then right click on "Lambda", and choose to "Create Lambda SAM Application".  
For settings of the SAM app, select python 3.7/3.8 as the run time, select "AWS SAM Hello World" as the SAM application template, select the directory you want to store the files, and name your app "checkDB"(or whatever your want).  
In Cloud9 IDE, click on "Environment" and check the file hierarchy. Find your lambda function's folder and open the sub-folder "hello-world".  
Replace "app.py" with the file [checkDB/hello_world/app.py](https://github.com/yueyang0115/serverless-architecture-with-AWS-Lambda/blob/main/checkDB/hello_world/app.py) from this repo.  
Modify app.py, change the name of DynamoDB table and the name of SQS queue.  
#### Build and deploy
Then you can build and deploy this application. More details can be found in the README file in this lambda fuction's folder.  
```
sam build --use-container
sam deploy --guided
```
#### Add permission & trigger
Open AWS Lambda console, there'll be a new app "checkDB" and a new lambda function "checkDB-HelloWorldFunction-xxxx".  
Click on the function, then click on "Configuration", then click on "permission", and finally click on the existing role. You will be lead to the IAM Management console.  
On the new page, click on "Attach Policies". On the next page, find "AdministratorAccess" policy and choose to attach it.  
Go back to previous function page in AWS lambda console, click on "Triggers". Delete the "API Gateway" trigger.  
Add a new EventBridge(CloudWatch Events) trigger. For the settings, create a new rule, and name it "OneMinuteTimer"(or whatever). For "schedule expression", type "rate(1 minute)".  

#### Test 
Now you can enable the trigger and see the messages in SQS queue.  
In AWS SQS console, click on the "checkDB" queue, then click on "Send and receive messages" and then "poll for messages".  
In AWS Lambda console, you can click on the monitor and find more details about the activity. You can also "View Logs in CloudWatch".  
