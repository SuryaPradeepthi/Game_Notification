In this project, I utilized the NBA API to develop a system that sent notifications via SMS, phone, and email. The NBA API is a free tool that retrieves statistics from the official NBA stats page, making the data accessible to developers for creating applications.

The system I built functioned as a query and response platform. For instance, when I requested information about all the games happening at a particular moment, the system processed the request, retrieved the relevant data, and sent notifications via email and SMS.

A backend system interacted with the NBA API to fetch the data. AWS services played a crucial role in this process. Specifically, I used AWS Lambda to run the code responsible for fetching NBA game data. Additionally, the Simple Notification Service (SNS), a pub-sub service, was used for delivering notifications. Subscribers to the SNS topic automatically received notifications via email or SMS.

To determine how frequently the Lambda function should run, I used AWS EventBridge, which acted as a cron job scheduler. This allowed me to create a schedule that triggered the Lambda function at specific intervals.

The next step was to navigate to the SNS service, where I created a standard topic. After the topic was created, I subscribed to it by providing an email address and confirmed the subscription through the email sent to me.

At this point, I needed to grant the Lambda function permission to publish messages to the SNS topic. By default, the Lambda function did not have permission to publish to SNS. To enable this, I created an IAM policy that granted the Lambda function the necessary permissions.

I proceeded to the IAM dashboard, created a new policy, and chose the JSON option. The following policy was used:

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

I replaced REGION, ACCOUNT_ID, and TOPIC_NAME with the appropriate values (the SNS ARN was copied directly from the resource).

Next, I created a role that was assigned to the Lambda function. This role granted temporary permissions to Lambda, allowing it to perform the necessary actions. The role included the policy I had just created, which permitted Lambda to publish to the SNS topic. I also ensured that CloudWatch permissions were included for logging and monitoring.

To do this, I clicked on "Create Role," selected "AWS Service," chose "Lambda," and selected the required permission policies, such as gameday_sns for SNS and AWSLambdaBasicExecutionRole for CloudWatch monitoring. I then completed the process by clicking "Create Role."

I navigated to the Lambda service and created a function from scratch. I assigned the role I had created earlier and wrote the necessary code for the Lambda function. Once the function was deployed, I went to the "Configure" tab to set environment variables, including the NBA API key and the SNS topic ARN.

I registered on SportsData.io, obtained the API key, and copied it into the environment variables as NBA_API_KEY, along with the SNS topic ARN.

At this point, I used the "Test" tab in Lambda to create a test event. After naming the test, I ran it, which triggered the Lambda function to send the relevant data to the subscribed email address.

Lastly, I used EventBridge to schedule the events. I configured the cron expression 0 9-23/2,0-2/2 * * ? *, which ensured that the event was triggered every two hours from 9 AM to 11 PM and from 12 AM to 2 AM, every day and every month.
