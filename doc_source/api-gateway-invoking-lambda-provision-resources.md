--------

Help us improve the AWS SDK for JavaScript version 3 \(V3\) documentation by providing feedback using the **Feedback** link, or create an issue or pull request on [GitHub](https://github.com/awsdocs/aws-sdk-for-javascript-v3)\.

 The [AWS SDK for JavaScript V3 API Reference Guide](https://docs.aws.amazon.com/AWSJavaScriptSDK/v3/latest/index.html) describes in detail all the API operations for the AWS SDK for JavaScript version 3 \(V3\)\.

--------

# Create the AWS resources<a name="api-gateway-invoking-lambda-provision-resources"></a>

This topic is part of a tutorial that demonstrates how to invoke a Lambda function through Amazon API Gateway using the AWS SDK for JavaScript\. To start at the beginning of the tutorial, see [Invoking Lambda with API Gateway](api-gateway-invoking-lambda-example.md)\.

This tutorial requires the following resources\.
+ An Amazon DynamoDB table named \*\*Employee\*\* with a key named \*\*Id\*\* and the fields shown in the previous illustration\. Make sure you enter the correct data, including a valid mobile phone that you want to test this use case with\. For more information, see [Create a Table](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/getting-started-step-1.html)\.
+ An IAM role with attached permissions to execute Lambda functions\.
+ An Amazon S3 bucket to host Lambda function\.

You can create these resources manually, but we recommend provisioning these resources using the AWS Cloud Development Kit \(AWS CDK\) as described in this tutorial\.

**Note**  
The AWS CDK is a software development framework that enables you to define cloud application resources\. For more information, see the [AWS Cloud Development Kit \(AWS CDK\) Developer Guide](https://docs.aws.amazon.com/cdk/latest/guide/home.html)\.

## Create the AWS resources using the AWS CLI<a name="api-gateway-invoking-lambda-resources-cli"></a>

To run the stack using the AWS CLI:

1. Install and configure the AWS CLI following the instructions in the [AWS CLI User Guide](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-welcome.html)\.

1. Create a file named `setup.yaml` in the root directory of your project folder, and copy the content [ here on GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/blob/master/javascriptv3/example_code/cross-services/lambda-api-gateway/setup.yaml) into it\.

1. Run the following command from the command line, replacing *STACK\_NAME* with a unique name for the stack\.
**Important**  
The stack name must be unique within an AWS Region and AWS account\. You can specify up to 128 characters, and numbers and hyphens are allowed\.

   ```
   aws cloudformation create-stack --stack-name STACK_NAME --template-body file://setup.yaml --capabilities CAPABILITY_IAM
   ```

   For more information on the `create-stack` command parameters, see the [AWS CLI Command Reference guide](https://docs.aws.amazon.com/cli/latest/reference/cloudformation/create-stack.html), and the [AWS CloudFormation User Guide](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/using-cfn-cli-creating-stack.html)\.

1. When the stack is create, use the AWS SDK for JavaScript to populate the DynamoDB table\. Create a file named `populate-table.ts` in the root directory of your project folder, and copy the content [ here on GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/blob/master/javascriptv3/example_code/cross-services/lambda-api-gateway/src/helper-functions/populate-table.ts) into it\.

1. Run the following command from the command line\.

   ```
   ts-node populate-table.ts
   ```

   ```
   // Load the required Amazon DynamoDB client and commands.
   const {
     DynamoDBClient,
     BatchWriteItemCommand,
   } = require("@aws-sdk/client-dynamodb");
   
   // Set the AWS Region.
   const REGION = "REGION"; //e.g. "us-east-1"
   
   // Set the parameters.
   const params = {
     RequestItems: {
       Employees: [
         {
           PutRequest: {
             Item: {
               id: { N: "1" },
               firstName: { S: "Bob" },
               phone: { N: "155555555555654" },
               startDate: { S: "2019-12-20" },
             },
           },
         },
         {
           PutRequest: {
             Item: {
               id: { N: "2" },
               firstName: { S: "Xing" },
               phone: { N: "155555555555653" },
               startDate: { S: "2019-12-17" },
             },
           },
         },
         {
           PutRequest: {
             Item: {
               id: { N: "55" },
               firstName: { S: "Harriette" },
               phone: { N: "155555555555652" },
               startDate: { S: "2019-12-19" },
             },
           },
         },
       ],
     },
   };
   
   // Create DynamoDB service object.
   const dbclient = new DynamoDBClient({ region: REGION });
   
   const run = async () => {
     try {
       const data = await dbclient.send(new BatchWriteItemCommand(params));
       console.log("Success", data);
     } catch (err) {
       console.log("Error", err);
     }
   };
   run();
   ```

    This code is available [ here on GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/blob/master/javascriptv3/example_code/cross-services/lambda-api-gateway/src/helper-functions/populate-table.ts)\.

## Create the AWS resources using the AWS Management Console;<a name="api-gateway-invoking-lambda-resources-console"></a>

To create resources for the app in the console, follow the instructions in the [AWS CloudFormation User Guide](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-console-create-stack.html)\. Use the template provided create a file named `setup.yaml`, and copy the content [ here on GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/blob/master/javascriptv3/example_code/cross-services/lambda-api-gateway/setup.yaml)\.

**Important**  
The stack name must be unique within an AWS Region and AWS account\. You can specify up to 128 characters, and numbers and hyphens are allowed\.

View a list of the resources in the console by opening the stack on the AWS CloudFormation dashboard, and choosing the **Resources** tab\. You require these for the tutorial\. 