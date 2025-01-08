
In this project, I will be utilizing the NBA API to develop a system that sends notifications via SMS, phone, and email. The NBA API is a free tool that retrieves statistics from the official NBA stats page, making the data accessible to developers for creating applications.

The system I am building will function as a query and response platform. For instance, when I request information about all the games happening at a particular moment, the system will process the request, retrieve the relevant data, and send notifications through email and SMS.

A backend system will interact with the NBA API to fetch the data. AWS services will play a crucial role in this process. Specifically, I will use AWS Lambda to run the code responsible for fetching NBA game data. Additionally, the Simple Notification Service (SNS), a pub-sub service, will be used for delivering notifications. Subscribers to the SNS topic will automatically receive notifications via email or SMS.

To determine how frequently the Lambda function should run, I will use AWS EventBridge, which acts as a cron job scheduler. This will allow me to create a schedule that triggers the Lambda function at specific intervals.

The next step is to navigate to the SNS service, where I will create a standard topic. After the topic is created, I will subscribe to it by providing an email address and confirming the subscription through the email sent to me.

At this point, I need to grant the Lambda function permission to publish messages to the SNS topic. By default, the Lambda function does not have permission to publish to SNS. To enable this, I will create an IAM policy that grants the Lambda function the necessary permissions.

I will proceed to the IAM dashboard, create a new policy, and choose the JSON option. The following policy will be used:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "sns:Publish",
      "Resource": "arn:aws:sns:REGION:ACCOUNT_ID:TOPIC_NAME"
    }
  ]
}
```

I will replace `REGION`, `ACCOUNT_ID`, and `TOPIC_NAME` with the appropriate values (the SNS ARN can be copied directly in the resource).

Next, I will create a role that will be assigned to the Lambda function. This role will grant temporary permissions to Lambda, allowing it to perform the necessary actions. The role will include the policy I just created, which permits Lambda to publish to the SNS topic. I will also ensure that CloudWatch permissions are included for logging and monitoring.

To do this, I will click on "Create Role," select "AWS Service," choose "Lambda," and select the required permission policies, such as `gameday_sns` for SNS and `AWSLambdaBasicExecutionRole` for CloudWatch monitoring. I will then complete the process by clicking "Create Role."

Now, I will navigate to the Lambda service and create a function from scratch. I will assign the role I created earlier and write the necessary code for the Lambda function. Once the function is deployed, I will go to the "Configure" tab to set environment variables, including the NBA API key and the SNS topic ARN.

I will register on SportsData.io, obtain the API key, and copy it into the environment variables as `NBA_API_KEY` along with the SNS topic ARN.

At this point, I will use the "Test" tab in Lambda to create a test event. After naming the test, I will run it, which will trigger the Lambda function to send the relevant data to the subscribed email address.

Lastly, I will use EventBridge to schedule the events. I will configure the cron expression `0 9-23/2,0-2/2 * * ? *`, which ensures that the event is triggered every two hours from 9 AM to 11 PM and from 12 AM to 2 AM, every day and every month.

