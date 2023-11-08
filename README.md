# Event-Driven-Architecture
Building Event Driven Architecture with Event-Bridge

<img width="1147" alt="EventBridgeDiagram copy" src="https://github.com/Vishnu5L3/Serverless-Lab-Design/assets/142379885/564849ee-9852-4f2a-b230-1993a6a745c7">


Amazon EventBridge is a serverless event bus service that connects applications with real-time data from various sources, including AWS services and SaaS applications. It routes data to targets like AWS Lambda, making it easy to create event-driven architectures that react in real time. EventBridge simplifies building loosely coupled and distributed event-driven systems while allowing event filtering to ensure targets receive only relevant data, enhancing developer agility and application resiliency.

*Here are the key concepts related to Amazon EventBridge in a concise format:*

Events: Events represent changes in an environment, including AWS environments, SaaS services, or custom applications.

Rules: Rules match incoming events and route them to various targets for processing. Rules can route events to multiple targets in parallel and don't follow a specific order. This flexibility allows different parts of an organization to process events of interest.

Targets: Targets are responsible for processing events and can include services like Amazon EC2, Lambda, Kinesis, ECS, Step Functions, SNS, SQS, and built-in targets. Targets receive events in JSON format.

Event Buses: Event buses receive events. Each rule is associated with a specific event bus, and it only matches events received by that bus. An AWS account has a default event bus for receiving events from AWS services, and you can create custom or partner event buses to receive events from your applications.

In this module, you'll use EventBridge to create an event bus, route events to targets through rules, and schedule recurring events using expressions.

![eventbus and targets](https://github.com/Vishnu5L3/Serverless-Lab-Design/assets/142379885/2adc8947-2c82-4a7e-874b-041ebaa0297f)


In this module, we will create a custom EventBridge event bus named "Orders" and an EventBridge rule called "OrderDevRule." This rule will match all events sent to the "Orders" event bus and send them to a CloudWatch Logs log group named "/aws/events/orders."

*Step 1: Create a custom event bus*

Open the AWS Management Console for EventBridge in a new tab or window while keeping this guide open for reference.

On the EventBridge homepage, navigate to "Events" and select "Event buses" from the left navigation.

Open the AWS Management Console for EventBridge as instructed.

On the EventBridge homepage, go to "Events" and select "Event buses" from the left navigation.

Click the "Create event bus" button.

In the "Create event bus" dialog:

Enter the name "Orders" in the "Name" field.
<img width="1440" alt="Screenshot 2023-10-31 at 6 45 23 PM" src="https://github.com/Vishnu5L3/Serverless-Lab-Design/assets/142379885/4b03e83d-590b-4af1-be0f-1c377477fec9">


Leave "Event archive" and "Schema discovery" disabled.
Keep the "Resource-based policy" blank.
Click the "Create" button to create the custom event bus named "Orders."

Define rule detail:

Add OrdersDevRule as the Name of the rule
Add Catchall rule for development purposes for Description
Select Rule with an event pattern for the Rule type

In the next step, Build event pattern

under Event source, choose Other

Under Event pattern, further down the screen, enter the following pattern to catch all events from com.aws.orders:
```
{
   "source": ["com.aws.orders"]
}
```
<img width="1440" alt="Screenshot 2023-10-31 at 6 49 52 PM" src="https://github.com/Vishnu5L3/Serverless-Lab-Design/assets/142379885/494acd9a-a4f0-4711-9292-0ab743e30fe9">

To select CloudWatch Logs as the target and name your log group "/aws/events/orders," follow these steps:

After creating the custom event bus, you should still be in the process of setting up the EventBridge rule. In the rule configuration, you'll see a section to select your rule target.

From the "Target" dropdown, select "CloudWatch log group."

In the "Name" field, enter "/aws/events/orders" as the name for your log group.

Make sure to save or apply your rule configuration as needed. This configuration will send events to the specified CloudWatch Logs log group.

To proceed with testing your dev rule, follow these steps:

Skip through the "configure tags" section as instructed.

Review your rule configuration and ensure it's correct.

Click the "Create" button to create your rule.

Step 3: Test your dev rule

In the left pane, select "Event buses" to access your event bus configuration.

Select "Send events" to test the newly created event rule.

Ensure that the custom event is populated with the following details:

Event Bus selected: Orders
Source: com.aws.orders
In the "Detail Type" field, add "Order Notification"
JSON payload for the Event detail should be as specified.
```{
   "category": "lab-supplies",
   "value": 415,
   "location": "eu-west"
}
```
Click Send

With these settings, you can test your event rule and verify its behavior as per your requirements.

Choose Log groups in the left navigation and select the /aws/events/orders log group.

Select the Log stream.

Toggle the log event to verify that you received the event.

<img width="1435" alt="Screenshot 2023-10-31 at 7 07 56 PM" src="https://github.com/Vishnu5L3/Serverless-Lab-Design/assets/142379885/f343c04d-216b-4957-b963-413dc26b8ec6">


*WORKING WITH EVENT-BRIDGE RULES:*



![eventbridge_rules](https://github.com/Vishnu5L3/Serverless-Lab-Design/assets/142379885/c081fbe9-156d-46ce-a4e1-645d2d2c33fa)

Rules are a crucial part of Amazon EventBridge. They match incoming events and route them to one or more targets for processing. A single rule can direct events to multiple targets, and these targets are processed in parallel. It's important to note that rules are not processed in a specific order. Additionally, a rule can modify the JSON data sent to the target by either selecting specific parts or overwriting it with a constant value. EventBridge provides support for over 28 AWS service targets.

In this module, you'll learn how to create an EventBridge rule for the "Orders" event bus. This rule is designed to match events with a source of "com.aws.orders" and send those events to various destinations, including an Amazon API Gateway endpoint, an AWS Step Function, and an Amazon Simple Notification Service (SNS) topic.

Events in Amazon EventBridge are represented as JSON objects and have the following envelope signature:
```{
  "version": "0",
  "id": "6a7e8feb-b491-4cf7-a9f1-bf3703467718",
  "detail-type": "EC2 Instance State-change Notification",
  "source": "aws.ec2",
  "account": "111111111111",
  "time": "2017-12-22T18:43:48Z",
  "region": "us-west-1",
  "resources": [
    "arn:aws:ec2:us-west-1:123456789012:instance/ i-1234567890abcdef0"
  ],
  "detail": {
    "instance-id": " i-1234567890abcdef0",
    "state": "terminated"
  }
}
```
Rules in Amazon EventBridge use event patterns to select events and determine how to route them to specific targets. These event patterns are defined as JSON objects and work on a simple principle: either an event matches the pattern, or it doesn't. The structure of event patterns is similar to that of the events themselves.

As an example, consider the following event pattern, which allows you to subscribe to events specifically from Amazon EC2:
```
{
  "source": [ "aws.ec2" ]
}
```
In event patterns, you specify the fields you want to match and provide the values you are looking for. When working with nested event structures, you can create event patterns to target specific subfields. For example, if you want to process all instance-termination events within a nested event structure, you can create an event pattern like the following:
```
{
  "source": [ "aws.ec2" ],
  "detail-type": [ "EC2 Instance State-change Notification" ],
  "detail": {
    "state": [ "terminated" ]
  }
}
```
"When working with event pattern matching, it's essential to keep in mind the following GitHub-ready tips:

To match an event with a pattern, the event must include all the field names specified in the pattern, maintaining the same nested structure.

Fields in the event that aren't mentioned in the pattern are effectively treated as wildcard matches, represented by '' : ''.

The matching process is precise and doesn't involve case-folding or any string normalization.

Values in event patterns follow JSON conventions, including string values enclosed in quotes, numbers, and keywords like true, false, and null.

Number matching is at the string representation level. For instance, 300, 300.0, and 3.0e2 are considered distinct."

*IDENTIFY THE API RULE*

You can find the API URL for this challenge in the Outputs of the CloudFormation Stack with a name containing ApiUrl.

To configure the EventBridge API Destination with basic auth security, follow these instructions:

Open the AWS Management Console for EventBridge in a new tab or window while keeping this guide open for reference.

On the EventBridge homepage, navigate to "API destinations" from the left navigation.

On the API destinations page, select "Create API destination."

Please proceed with the configuration based on your specific requirements and the instructions provided in the AWS Management Console.

To create the API destination with basic auth security as per your instructions, follow these steps:

On the "Create API destination" page:

Enter "api-destination" as the Name.
Enter the API URL identified in Step 1 as the API destination endpoint.
Select "POST" as the HTTP method.
Select "Create a new connection" for the Connection.
Enter "basic-auth-connection" as the Connection name.
Choose "Basic (Username/Password)" as the Authorization type.
Enter "myUsername" as the Username.
Enter "myPassword" as the Password.

<img width="1435" alt="Screenshot 2023-10-31 at 7 27 46 PM" src="https://github.com/Vishnu5L3/Serverless-Lab-Design/assets/142379885/8da91ab1-9cf6-4f8c-8a9a-459d071645db">

<img width="1440" alt="Screenshot 2023-10-31 at 7 27 53 PM" src="https://github.com/Vishnu5L3/Serverless-Lab-Design/assets/142379885/88990058-0ca4-4609-bb87-27e86b1e91b1">

To configure an EventBridge rule targeting the EventBridge API Destination, please follow these steps:

From the left-hand menu, select "Rules."

In the "Event bus" dropdown, choose the "Orders" event bus.

Click "Create rule."

On the "Define rule detail" page:

Enter "OrdersEventsRule" as the Name of the rule.

For Description, input "Send com.aws.orders source events to API Destination."

Under "Build event pattern":

Choose "Other" for your Event source.
Copy and paste the following event pattern:
```
{
  "source": ["com.aws.orders"]
}
```
To specify the rule target as instructed:

Select "EventBridge API destination" as the target type.

Choose "api-destination" from the list of existing API destinations.

This configuration will direct events matching the defined pattern to the specified API destination.

<img width="1440" alt="Screenshot 2023-10-31 at 7 39 14 PM" src="https://github.com/Vishnu5L3/Serverless-Lab-Design/assets/142379885/46ce0d53-2f5b-4866-af5e-1f79c03bd51c">

To complete the rule creation:

Click "Next" and follow the remaining steps in the walk-through to finish creating the rule.
Step 4: Send a test Orders event

Using the "Send events" function, you can send the following "Order Notification" events from the source "com.aws.orders" as per the provided instructions. This will allow you to test the newly created rule and verify its functionality.
```
{ "category": "lab-supplies", "value": 415, "location": "us-east" }
```
Step 5: Verify API Destination
If the event sent to the Orders event bus matches the pattern in your rule, then the event will be sent to an API Gateway REST API endpoint.

Open the AWS Management Console for CloudWatch Log Groups  in a new tab or window, so you can keep this step-by-step guide open.
Select the Log group with an API-Gateway-Execution-Logs prefix.

Select the lATEST Log stream if you tried it multiple times.

<img width="1436" alt="Screenshot 2023-11-01 at 1 46 01 AM" src="https://github.com/Vishnu5L3/Serverless-Lab-Design/assets/142379885/73bf9fc1-e0f2-40a6-8e4d-cca97b0e2e38">

Toggle the log event to verify the basic authorization was successful and the API responds with a 200 status.

<img width="1440" alt="Screenshot 2023-11-01 at 1 47 00 AM" src="https://github.com/Vishnu5L3/Serverless-Lab-Design/assets/142379885/10777115-c668-475a-bc2f-898f5e899827">

Congratulations! You have successfully created your first custom event.

STEP FUNCTIONS CHALLENGE:

Step 1: Implement an EventBridge rule to target Step Functions
Use the EventBridge Console to:

Add a rule to the Orders event bus with the name EUOrdersRule
Define an event pattern to match events with a detail location in eu-west or eu-east
Target the OrderProcessing Step Functions state machine

Using the Send events function, send the following Order Notification events from the source com.aws.orders:
```
{ "category": "office-supplies", "value": 300, "location": "eu-west" }
```
To verify the execution of the Step Functions workflow as instructed, follow these steps:

Open the AWS Management Console for Step Functions in a new tab or window while keeping this guide open for reference.

On the Step Functions homepage, navigate to the left-hand navigation and select "State machines."

In the "Search for state machines" box, enter "OrderProcessing" to find the state machine.

Verify that the state machine execution has succeeded. You can review the state machine execution details to ensure it has processed successfully.

The Step Functions state machine will publish an "OrderProcessed" event back to the "Orders" event bus. This is achieved using a new Service Integration for EventBridge, simplifying event production during workflow execution. You can explore this event further in the workshop by selecting "OrderProcessing" from the list of state machines and then choosing the specific workflow execution from the list.

Step 1: Enable Schema Discovery

Open the AWS Management Console.
Navigate to the EventBridge service.
In the Event buses section, select the custom event bus you're using for your events (e.g., "Orders").
Click on the event bus to open its settings.
Scroll down to the "Schema discovery" section and click "Edit schema discovery."
In the "Edit schema discovery" dialog:
Set "State" to "Enabled."
For "Prefix," provide a prefix to identify the schemas in the registry. For example, "myapp."
Click "Save" to enable Schema Discovery for your event bus.
Step 2: Publish Events with Schema Information

When publishing events to your event bus, include schema information in your events. The schema should follow a predefined format and be included in the event's details.
Ensure that you provide valid JSON schema information within the event, adhering to your specified schema prefix.
Step 3: Schema Detection and Registry

EventBridge Schema Discovery will automatically detect the schema information from the events you publish.
Detected schemas will be added to the EventBridge Schema Registry, where you can access them.
Step 4: Generate Code Bindings

To generate code bindings for a specific schema, follow these steps:
Open the AWS Management Console.
Navigate to the EventBridge service.
In the "Schema registry" section, you'll find the list of schemas detected by Schema Discovery.
Select the schema for which you want to generate code bindings.
In the schema details page, you'll see a "Code bindings" tab.
Click the "Download code bindings" button.
Choose your preferred programming language from the options available (e.g., Java, Python, TypeScript).
Download the generated code bindings for your chosen language.
Use these code bindings in your application code for working with event data.
Optional: AWS Toolkit Integration (IntelliJ and VS Code)
If you're using AWS Toolkit for IntelliJ or VS Code, you can also generate code bindings from within your integrated development environment.

Install the AWS Toolkit plugin for your IDE (IntelliJ or VS Code).
Open your project in your IDE.
Access the AWS Toolkit's EventBridge Schema Registry features.
Select the schema you want to generate code bindings for and choose your desired language.
Download and integrate the generated code bindings into your project.
By following these steps, you'll enable Schema Discovery, automatically detect and register schemas, and generate code bindings to work with strongly typed event data in your preferred programming language.

Create an AWS Lambda Function
![lambda_dest_arch](https://github.com/Vishnu5L3/Serverless-Lab-Design/assets/142379885/6dcc2f5f-5815-4cf5-96e2-1879835c84eb)


Open the AWS Management Console.
Navigate to the AWS Lambda service.
Click "Create function."
Choose "Author from scratch."
Enter a name for your Lambda function (e.g., "MyEventDrivenLambda").
For "Runtime," select the runtime environment compatible with your application code (e.g., Python, Node.js).
Choose or create an execution role for the Lambda function that has permission to access EventBridge.
Click "Create function" to create the Lambda function.
Step 3: Set up an EventBridge Rule

Open the AWS Management Console.
Navigate to the EventBridge service.
In the Event buses section, select the same custom event bus you're using for your events (e.g., "Orders").
Click "Create rule."
In the "Create rule" page:
Enter a name for your rule (e.g., "MyEventDrivenRule").
For "Description," provide a meaningful description of your rule.
Choose "Event Source" and select "Event bus created by AWS account" as the source.
For "Event bus," select the custom event bus (e.g., "Orders").
For "Event source," choose "Other" to match any event source.
Define the event pattern to match, for example:
```
{
  "detail-type": ["OrderNotification"]
}
```
*CONGRATS ! YOU CREATED AN EVENT-DRIVEN-ARCHITECTURE USING EVENT EVENT-BRIDGE,SNS,SCHEMA REGISTRY,RULES,MICROSERVICES ETC.*



























































































