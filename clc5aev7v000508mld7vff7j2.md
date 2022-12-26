# Boto3 : AWS'ing in Python

Boto3 is the Amazon Web Services (AWS) Software Development Kit (SDK) for Python, which allows Python developers to write software that makes use of services like Amazon S3 and Amazon EC2. Boto3 makes it easy to integrate your Python application, library, or script with AWS services.

Boto3 is vast and we will only cover a few popular services here, list of all [`available services`](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/index.html)

![](https://i0.wp.com/blog.knoldus.com/wp-content/uploads/2022/02/image-2-2.png?fit=810%2C301&ssl=1 align="center")

Here is an in-depth tutorial on using Boto3 with examples to give you a better understanding of how it works.

## **Installation**

To install Boto3, simply use pip:

```python
pip install boto3
```

You will also need to have an AWS account and set up your access keys in order to use Boto3. You can do this by going to the IAM (Identity and Access Management) section of the AWS Management Console and creating a new access key. Make sure to save the access key ID and secret access key in a secure location, as you will need them to authenticate your Boto3 scripts.

## **Importing Boto3 and Setting Up a Client**

To use Boto3, you will first need to import it and create a client for the service you want to use. Here's an example of how to import Boto3 and create a client for the EC2 (Elastic Compute Cloud) service:

```python
import boto3

ec2 = boto3.client('ec2')
```

You can use a client to make API calls to a specific service. In this example, the EC2 client will allow us to make calls to the EC2 API.

You can also use a resource to manage resources. A resource represents a collection of related actions you can perform. Here's an example of how to import Boto3 and create a resource for the S3 (Simple Storage Service) service:

```python
import boto3

s3 = boto3.resource('s3')
```

## **Example: Listing EC2 Instances**

Now that we have a client for the EC2 service, let's use it to list all the instances in our account. Here's the code to do that:

```python
import boto3

ec2 = boto3.client('ec2')

response = ec2.describe_instances()

for reservation in response['Reservations']:
    for instance in reservation['Instances']:
        print(instance['InstanceId'])
```

This code will print the ID of each EC2 instance in your account.

## **Example: Creating an S3 Bucket**

Now let's use Boto3 to create an S3 bucket. Here's the code to do that:

```python
import boto3

s3 = boto3.client('s3')

response = s3.create_bucket(
    ACL='private',
    Bucket='my-new-bucket',
    CreateBucketConfiguration={
        'LocationConstraint': 'us-west-2'
    }
)

print(response)
```

This code will create a new S3 bucket named "my-new-bucket" in the US West (Oregon) region.

## **Example: Uploading a File to S3**

Now let's use Boto3 to upload a file to our S3 bucket. Here's the code to do that:

```python
import boto3

s3 = boto3.client('s3')

response = s3.s3.upload_file( 'local/path/to/file.txt', 'my-new-bucket', 'remote/path/to/file.txt' )
```

Copy code This code will upload the file "file.txt" from your local machine to the "remote/path/to/file.txt" location in the "my-new-bucket" S3 bucket. ## Example: Downloading a File from S3 Now let's use Boto3 to download a file from our S3 bucket. Here's the code to do that:

```python
import boto3
s3 = boto3.client('s3')

s3.download_file( 'my-new-bucket', 'remote/path/to/file.txt', 'local/path/to/file.txt' )
```

This code will download the file "file.txt" from the "remote/path/to/file.txt" location in the "my-new-bucket" S3 bucket to your local machine. ## Example: Listing S3 Buckets Now let's use Boto3 to list all the S3 buckets in our account. Here's the code to do that:

```python
import boto3
s3 = boto3.client('s3')
response = s3.list_buckets()

for bucket in response['Buckets']: print(bucket['Name'])
```

This code will print the name of each S3 bucket in your account.  
Example: Deleting an S3 Bucket Now let's use Boto3 to delete an S3 bucket. Here's the code to do that:

```python
import boto3
s3 = boto3.client('s3')

#First, delete all the objects in the bucket
response = s3.list_objects(Bucket='my-new-bucket')

for obj in response['Contents']: s3.delete_object(Bucket='my-new-bucket', Key=obj['Key'])

#Then delete the bucket itself
s3.delete_bucket(Bucket='my-new-bucket')
```

This code will delete the `"my-new-bucket"` S3 bucket, along with all the objects in the bucket. ## Conclusion I hope this tutorial has given you a good understanding of how to use Boto3 to interact with AWS services. Boto3 is a powerful Python library that can be used to automate a wide variety of AWS tasks, such as creating and managing EC2 instances, uploading and downloading files to S3, and much more. With Boto3, you can easily integrate your Python application, library, or script with AWS services.

## **Example: Listing RDS Instances**

Let's say you want to use Boto3 to list all the RDS (Relational Database Service) instances in your account. Here's the code to do that:

```python
import boto3

rds = boto3.client('rds')

response = rds.describe_db_instances()

for instance in response['DBInstances']:
    print(instance['DBInstanceIdentifier'])
```

This code will print the identifier of each RDS instance in your account.

## **Example: Creating an RDS Instance**

Now let's use Boto3 to create an RDS instance. Here's the code to do that:

```python
import boto3

rds = boto3.client('rds')

response = rds.create_db_instance(
    DBName='mydatabase',
    DBInstanceIdentifier='mydbinstance',
    AllocatedStorage=5,
    DBInstanceClass='db.t2.micro',
    Engine='mysql',
    MasterUsername='admin',
    MasterUserPassword='password',
    VpcSecurityGroupIds=[
        'sg-0123456789'
    ]
)

print(response)
```

This code will create a new RDS instance with the identifier "mydbinstance", using the MySQL engine and the "db.t2.micro" instance class. The instance will be associated with the VPC security group with the ID "sg-0123456789" and will have a master username and password of "admin" and "password" respectively.

## **Example: Deleting an RDS Instance**

Now let's use Boto3 to delete an RDS instance. Here's the code to do that:

```python
import boto3

rds = boto3.client('rds')

rds.delete_db_instance(
    DBInstanceIdentifier='mydbinstance',
    SkipFinalSnapshot=True
)
```

This code will delete the RDS instance with the identifier `"mydbinstance"`, skipping the creation of a final snapshot.

## **Example: Listing SNS Topics**

Now let's use Boto3 to list all the SNS (Simple Notification Service) topics in our account. Here's the code to do that:

```python
import boto3

sns = boto3.client('sns')

response = sns.list_topics()

for topic in response['Topics']:
    print(topic['TopicArn'])
```

This code will print the Amazon Resource Name (ARN) of each SNS topic in your account.

## **Example: Sending a Text Message with SNS**

Now let's use Boto3 to send a text message using SNS. Here's the code to do that:

```python
import boto3

sns = boto3.client('sns')

response = sns.publish(
    PhoneNumber='+1234567890',
    Message='Hello, world!'
)

print(response)
```

This code will send the text message "Hello, world!" to the phone number "+1234567890".

## **Example: Listing SQS Queues**

Now let's use Boto3 to list all the SQS (Simple Queue Service) queues in our account. Here's the code to do that:

```python
import boto3

sqs = boto3.client('sqs')

response = sqs.list_queues()

for queue_url in response['QueueUrls']:
    print(queue_url)
```

This code will print the URL of each SQS queue in your account.

## **Example: Sending a Message to an SQS Queue**

Now let's use Boto3 to send a message to an SQS queue. Here's the code to do that:

```python
import boto3

sqs = boto3.client('sqs')

response = sqs.send_message(
    QueueUrl='https://sqs.us-west-2.amazonaws.com/123456789012/my-queue',
    MessageBody='Hello, world!'
)

print(response)
```

This code will send the message "Hello, world!" to the SQS queue with the URL "[**https://sqs.us-west-2.amazonaws.com/123456789012/my-queue**](https://sqs.us-west-2.amazonaws.com/123456789012/my-queue)".

## **Example: Receiving a Message from an SQS Queue**

Now let's use Boto3 to receive a message from an SQS queue. Here's the code to do that:

```python
import boto3

sqs = boto3.client('sqs')

response = sqs.receive_message(
    QueueUrl='https://sqs.us-west-2.amazonaws.com/123456789012/my-queue',
    MaxNumberOfMessages=1
)

if 'Messages' in response:
    message = response['Messages'][0]
    body = message['Body']
    receipt_handle = message['ReceiptHandle']

    # Do something with the message

    sqs.delete_message(
        QueueUrl='https://sqs.us-west-2.amazonaws.com/123456789012/my-queue',
        ReceiptHandle=receipt_handle
    )
```

This code will receive a single message from the SQS queue with the URL "[**https://sqs.us-west-2.amazonaws.com/123456789012/my-queue**](https://sqs.us-west-2.amazonaws.com/123456789012/my-queue)". If a message is received, the code will do something with the message and then delete it from the queue.

## DynamoDB

DynamoDB is a fully managed NoSQL database service that provides fast and predictable performance with seamless scalability.

Here is an example of how you can use Boto3 to interact with DynamoDB in Python:

```python
import boto3

# Get the service resource
dynamodb = boto3.resource('dynamodb')

# Create a DynamoDB table
table = dynamodb.create_table(
    TableName='users',
    KeySchema=[
        {
            'AttributeName': 'username',
            'KeyType': 'HASH'
        },
        {
            'AttributeName': 'last_name',
            'KeyType': 'RANGE'
        }
    ],
    AttributeDefinitions=[
        {
            'AttributeName': 'username',
            'AttributeType': 'S'
        },
        {
            'AttributeName': 'last_name',
            'AttributeType': 'S'
        },
    ],
    ProvisionedThroughput={
        'ReadCapacityUnits': 5,
        'WriteCapacityUnits': 5
    }
)

# Wait until the table exists
table.meta.client.get_waiter('table_exists').wait(TableName='users')

# Print out some data about the table
print(table.item_count)
```

This example creates a new DynamoDB table called "users" with a composite primary key made up of a partition key (username) and a sort key (last\_name). It sets the provisioned throughput for reads and writes to 5 capacity units each.

To add an item to the table, you can use the `put_item` method:

```python
table.put_item(
   Item={
        'username': 'johndoe',
        'last_name': 'Doe',
        'age': 25,
        'account_type': 'standard_user',
    }
)
```

To retrieve an item from the table, you can use the `get_item` method:

```python
response = table.get_item(
    Key={
        'username': 'johndoe',
        'last_name': 'Doe'
    }
)
item = response['Item']
print(item)
```

This will return the item with the primary key (username = "johndoe" and last\_name = "Doe").

You can also use the `query` method to retrieve items based on the values of secondary index keys:

```python
response = table.query(
    IndexName='age-index',
    KeyConditionExpression='age = :age',
    ExpressionAttributeValues={
        ':age': 25
    }
)
items = response['Items']
print(items)
```

This will return all items with an "age" attribute of 25, assuming that you have created a secondary index called "age-index" on the "age" attribute.

I hope this helps! Let me know if you have any questions.