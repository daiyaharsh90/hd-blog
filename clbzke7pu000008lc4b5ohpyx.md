# AWS for Data stuff : A primer

Amazon Web Services (AWS) is a comprehensive cloud computing platform that provides a wide range of services for building, deploying, and managing applications and data. In this blog post, we will explore some of the key features of AWS that are particularly relevant for data-intensive applications, including storage, processing, and analysis. We will also provide some example code snippets to demonstrate how to use these services in practice.

## **Storage**

One of the most fundamental components of any data-intensive application is a reliable and scalable storage system. AWS offers a variety of storage options to suit different needs and use cases.

### **S3**

Amazon Simple Storage Service (S3) is an object storage service that allows you to store and retrieve data from anywhere on the web. It is designed to be highly scalable, with the ability to store and retrieve any amount of data, at any time, from anywhere on the web.

S3 is a great option for storing large amounts of unstructured data, such as images, videos, audio files, and log files. It is also commonly used as a data lake, where raw data can be stored in its original format and accessed by various analytics and machine learning tools.

Here is an example of how to use the AWS SDK for Python (Boto3) to create a new S3 bucket and upload a file to it:

```bash
Copy codeimport boto3

# Create an S3 client
s3 = boto3.client('s3')

# Create a new S3 bucket
s3.create_bucket(Bucket='my-new-bucket')

# Upload a file to the bucket
s3.upload_file(Bucket='my-new-bucket', Filename='example.txt', Key='example.txt')
```

### **EBS**

Amazon Elastic Block Store (EBS) is a block-level storage service that provides persistent storage for Amazon Elastic Compute Cloud (EC2) instances. EBS volumes can be attached to and detached from EC2 instances as needed, making it easy to scale up or down based on the needs of your applications.

EBS is a good choice for storing data that requires fast, low-latency access, such as databases and file systems. It is also well-suited for use as a boot volume for EC2 instances, allowing you to store the operating system and application files on a separate, persistent volume.

Here is an example of how to use the AWS SDK for Python to create a new EBS volume and attach it to an EC2 instance:

```bash
Copy codeimport boto3

# Create an EC2 client
ec2 = boto3.client('ec2')

# Create a new EBS volume
response = ec2.create_volume(AvailabilityZone='us-east-1a', Size=1, VolumeType='gp2')
volume_id = response['VolumeId']

# Attach the volume to an EC2 instance
ec2.attach_volume(Device='/dev/xvdf', InstanceId='i-1234567890abcdefg', VolumeId=volume_id)
```

## **Processing**

Once you have your data stored in the cloud, you may need to perform various types of processing on it, such as transforming, aggregating, or filtering. AWS provides a range of services that can help you do this efficiently and at scale.

### **EC2**

As mentioned earlier, Amazon EC2 is a web service that provides resizable compute capacity in the cloud. You can launch on EC2 instances, which are virtual machines running in the cloud, and use them to perform a variety of tasks, including data processing.

One of the key advantages of using EC2 for data processing is that you have complete control over the hardware and software resources of the instances. This means you can choose the exact configuration and packages that are optimal for your workload, and scale up or down as needed to meet the changing demands of your application.

Here is an example of how to use the AWS SDK for Python to launch a new EC2 instance and run a simple data processing job on it:

```bash
Copy codeimport boto3

# Create an EC2 client
ec2 = boto3.client('ec2')

# Launch a new EC2 instance
response = ec2.run_instances(
    ImageId='ami-12345678',
    InstanceType='t2.micro',
    MinCount=1,
    MaxCount=1,
    KeyName='my-key-pair',
    SecurityGroups=['my-security-group']
)
instance_id = response['Instances'][0]['InstanceId']

# Wait for the instance to be in the 'running' state
ec2.wait_until_instance_running(InstanceIds=[instance_id])

# Connect to the instance using SSH
# (replace 'ec2-user' with the appropriate user for your AMI)
import paramiko

ssh = paramiko.SSHClient()
ssh.connect(hostname='ec2-12-34-56-78.compute-1.amazonaws.com', username='ec2-user', key_filename='my-key-pair.pem')

# Run a data processing job on the instance
stdin, stdout, stderr = ssh.exec_command('python my_data_processing_script.py')
for line in stdout:
    print(line.strip())
```

### **EMR**

Amazon EMR (Elastic MapReduce) is a fully-managed service that makes it easy to process and analyze large data sets using the Hadoop ecosystem and other big data technologies. EMR allows you to create a cluster of EC2 instances that are pre-configured with a range of tools and frameworks, such as Hadoop, Spark, Hive, and Pig, and then run data processing and analytics jobs on the cluster.

EMR is well-suited for a wide range of data processing and analytics tasks, including batch processing, stream processing, machine learning, and SQL queries. It is also highly scalable and can automatically add or remove nodes from the cluster based on the workload.

Here is an example of how to use the AWS SDK for Python to create an EMR cluster and run a Spark job on the cluster:

```bash
Copy codeimport boto3

# Create an EMR client
emr = boto3.client('emr')

# Create an EMR cluster
response = emr.run_job_flow(
    Name='My EMR Cluster',
    ReleaseLabel='emr-6.0.0',
    Instances={
        'InstanceGroups': [
            {
                'Name': 'Master nodes',
                'Market': 'ON_DEMAND',
                'InstanceRole': 'MASTER',
                'InstanceType': 'm5.xlarge',
                'InstanceCount': 1
            },
            {
                'Name': 'Worker nodes',
                'Market': 'ON_DEMAND',
                'InstanceRole': 'CORE',
                'InstanceType': 'm5.xlarge',
                'InstanceCount': 2
            }
        ],
        'Ec2KeyName': 'my-key-pair',
        'KeepJobFlowAliveWhenNoSteps': True,
        'TerminationProtected': False
    },
    Applications=[{'Name': 'Spark'}],
    Configurations=[
        {
            'Classification': 'spark-env',
            'Configurations': [
                {
                    'Classification': 'export',
                    'Properties': {
                        'PYSPARK_PYTHON': '/usr/bin/python3'
                    }
                }
            ]
        }
    ],
    JobFlowRole='EMR_EC2_DefaultRole',
    ServiceRole='EMR_DefaultRole',
    VisibleToAllUsers=True,
    Tags=[
        {
            'Key': 'project',
            'Value': 'data-processing'
        }
    ]
)
cluster_id = response['ClusterId']

# Wait for the cluster to be in the 'waiting' state
emr.wait_until_cluster_running(ClusterId=cluster_id)

# Add a Spark step to the cluster
emr.add_job_flow_steps(
    ClusterId=cluster_id,
    Steps=[
        {
            'Name': 'Spark job',
            'ActionOnFailure': 'CONTINUE',
            'HadoopJarStep': {
                'Jar': 'command-runner.jar',
                'Args': [
                    'spark-submit',
                    '--deploy-mode', 'cluster',
                    '--class', 'com.example.MySparkJob',
                    's3://my-bucket/my-spark-job.jar' ] 
            } 
        } 
        ] 
    )
```

### **Wait for the Spark step to complete**

```bash
step_id = response['StepIds'][0] emr.wait_until_step_complete(ClusterId=cluster_id, StepId=step_id)
```

### Terminate the EMR cluster

```bash
emr.terminate_job_flows(JobFlowIds=[cluster_id])
```

In this example, we create an EMR cluster with one master node and two worker nodes, and then run a Spark job on the cluster by adding a Spark step. The Spark job is submitted using the `spark-submit` script, and the `--deploy-mode cluster` flag tells Spark to run the job in cluster mode, using the available worker nodes to parallelize the computation.

EMR also provides several other features and capabilities, such as integration with other AWS services, such as S3 and Athena, support for custom AMIs and bootstrap actions, and the ability to run Jupyter notebooks on the cluster.

### Analysis

Once you have processed your data, you may want to perform various types of analysis on it, such as querying, visualization, or machine learning. AWS provides a range of services that can help you do this quickly and easily.

### Athena

Amazon Athena is a serverless, interactive query service that allows you to analyze data in Amazon S3 using SQL. Athena is particularly useful for ad-hoc querying and exploration of large datasets, as it allows you to run queries on S3 data without having to first load it into a separate data store.

Athena is based on Presto, an open-source SQL query engine, and supports a wide range of data formats, including CSV, JSON, ORC, Parquet, andAVRO. It is also highly performant, with the ability to parallelize queries across thousands of nodes.

Here is an example of how to use the AWS SDK for Python to run a query on an Athena table and print the results:

```bash
Copy codeimport boto3

# Create an Athena client
athena = boto3.client('athena')

# Run a query on an Athena table
response = athena.start_query_execution(
    QueryString='SELECT * FROM my_table LIMIT 10',
    QueryExecutionContext={
        'Database': 'my_database'
    },
    ResultConfiguration={
        'OutputLocation': 's3://my-bucket/athena-results/'
    }
)
query_execution_id = response['QueryExecutionId']

# Wait for the query to complete
athena.wait_until_query_complete(QueryExecutionId=query_execution_id)

# Get the results of the query
response = athena.get_query_results(QueryExecutionId=query_execution_id)
columns = response['ResultSet']['ResultSetMetadata']['ColumnInfo']
rows = response['ResultSet']['Rows']

# Print the results
for row in rows:
    values = row['Data']
    print(','.join([val['VarCharValue'] for val in values]))
```

### **QuickSight**

Amazon QuickSight is a cloud-based business intelligence (BI) service that allows you to create and publish interactive dashboards and reports. QuickSight integrates with a wide range of data sources, including S3, Athena, Redshift, and RDS, and provides a drag-and-drop interface for building charts and graphs.

QuickSight is a great option for quickly visualizing and exploring your data, as well as for creating dashboards and reports that can be shared with your team or organization.

Here is an example of how to use the AWS SDK for Python to create a new QuickSight dataset from an S3 bucket and build a simple bar chart from the data:

```bash
Copy codeimport boto3

# Create a QuickSight client
quicksight = boto3.client('quicksight')

# Create a new QuickSight dataset
response = quicksight.create_data_set(
    AwsAccountId='123456789012',
    DataSetId='my-dataset',
    Name='My Dataset',
    PhysicalTableMap={
        's3_table': {
            'RelationalTable': {
                'DataSourceArn': 'arn:aws:quicksight:us-east-1:123456789012:datasource/my-datasource',
                'InputColumns': [
                    {
                        'Name': 'col1',
                        'Type': 'INTEGER'
                    },
                    {
                        'Name': 'col2',
                        'Type': 'STRING'
                    }
                ],
                'Name': 'My S3 Table',
                'Schema': 'my_schema'
            },
            'CustomSql': {
                'DataSourceArn': 'arn:aws:quicksight:us-east-1:123456789012:datasource/my-datasource', 'Name': 'My S3 Table', 'SqlQuery': 'SELECT * FROM s3_table' }, 'S3Source': { 'DataSourceArn': 'arn:aws:quicksight:us-east-1:123456789012:datasource/my-datasource', 'UploadSettings': { 'Format': 'CSV', 'StartFromRow': 1, 'ContainsHeader': True, 'TextQualifier': 'DOUBLE_QUOTE', 'Delimiter': 'COMMA' }, 'InputColumns': [ { 'Name': 'col1', 'Type': 'INTEGER' }, { 'Name': 'col2', 'Type': 'STRING' } ], 'Name': 'My S3 Table', 'S3Uri': 's3://my-bucket/my-data.csv' } } } )
```

### **Create a new QuickSight analysis**

```bash
response = quicksight.create_analysis( AwsAccountId='123456789012', AnalysisId='my-analysis', Name='My Analysis', DataSetIds=[ 'my-dataset' ], ThemeArn='arn:aws:quicksight:us-east-1:123456789012:theme/Default' )
```

### **Create a new QuickSight dashboard**

```bash
response = quicksight.create_dashboard( AwsAccountId='123456789012', DashboardId='my-dashboard', Name='My Dashboard', AnalysisId='my-analysis', ThemeArn='arn:aws:quicksight:us-east-1:123456789012:theme/Default' )
```

### **Add a bar chart to the dashboard**

```bash
response = quicksight.update_dashboard( AwsAccountId='123456789012', DashboardId='my-dashboard', DashboardPublishOptions={ 'AdHocFilteringOption': { 'AvailabilityStatus': 'ENABLED' }, 'ExportToCSVOption': { 'AvailabilityStatus': 'ENABLED' }, 'SheetControlsOption': { 'AvailabilityStatus': 'ENABLED' } }, Name='My Dashboard', SourceEntity={ 'SourceAnalysis': { 'DataSetReferences': [ { 'DataSetPlaceholder': 'My S3 Table', 'DataSetArn': 'arn:aws:quicksight:us-east-1:123456789012:dataset/my-dataset' } ], 'Arn': 'arn:aws:quicksight:us-east-1:123456789012:analysis/my-analysis' } }, Versions=[ { 'Action': 'CREATE_NEW', 'Description': 'Initial version' } ] )
```

### **Get the URL of the dashboard**

```bash
response = quicksight.get_dashboard_embed_url( AwS AccountId='123456789012', DashboardId='my-dashboard', IdentityType='IAM', ResetDisabled=True ) dashboard_url = response['EmbedUrl'] print(f'Dashboard URL: {dashboard_url}')
```

### **Sagemaker**

Amazon SageMaker is a fully managed machine learning service that enables developers and data scientists to build, train, and deploy machine learning models quickly. SageMaker removes the heavy lifting from each step of the machine learning process, so developers and data scientists can focus on the interesting parts: designing, training, and fine-tuning models.

SageMaker provides several tools for preparing, processing, and modeling data, including Jupyter notebooks, data preparation and transformation libraries, and algorithms for training models. It also provides integration with popular deep learning frameworks, such as TensorFlow and PyTorch, so you can use the libraries and tools you're already familiar with.

Here's a simple example of how you can use SageMaker to train and deploy a machine learning model using the Python SDK:

First, you'll need to install the SageMaker Python SDK and set up your AWS credentials:

```bash
pip install sagemaker
```

Next, you'll need to create a `sagemaker.Session` object, which you'll use to interact with SageMaker:

```bash
import sagemaker

sagemaker_session = sagemaker.Session()
```

Next, you'll need to specify the data that you'll use to train your model. You can use the `sagemaker.session.upload_data` function to upload your data to an Amazon S3 bucket, which SageMaker will use to store the data and model artifacts:

```bash
Copy codedata_path = sagemaker_session.upload_data(path='data.csv', key_prefix='data')
```

Next, you'll need to specify the training script and the entry point for your model. The training script should be a Python script that loads and prepares the data, trains a model, and saves the trained model to a file:

```bash
!pygmentize train.py
```

```bash
import argparse
import pandas as pd

from sklearn.ensemble import RandomForestClassifier

if __name__ == '__main__':
    parser = argparse.ArgumentParser()

    # Hyperparameters are described here.
    parser.add_argument('--n-estimators', type=int, default=10)
    parser.add_argument('--min-samples-leaf', type=int, default=3)
    parser.add_argument('--max-depth', type=int, default=None)

    # Sagemaker specific arguments.
    parser.add_argument('--output-data-dir', type=str, default=os.environ['SM_OUTPUT_DATA_DIR'])
    parser.add_argument('--model-dir', type=str, default=os.environ['SM_MODEL_DIR'])
    parser.add_argument('--train', type=str, default=os.environ['SM_CHANNEL_TRAIN'])

    args = parser.parse_args()

    # Read in csv training file
    input_data = pd.read_csv(os.path.join(args.train, "train.csv"), header=None, names=None)

    # Labels are in the first column
    labels = input_data.iloc[:,0]
    features = input_data.iloc[:,1:]

    # Define a model and train it
    model = RandomForestClassifier(n_estimators=
```

Once you have your training script and data ready, you can use the `sagemaker.estimator.Estimator` class to specify the training job and launch it. The `Estimator` class takes several arguments, including the training script, the training instances, and the hyperparameters for the training job:

```bash
from sagemaker.sklearn.estimator import SKLearn

sklearn = SKLearn(
    entry_point='train.py',
    train_instance_type='ml.m4.xlarge',
    role='<Your IAM Role>',
    sagemaker_session=sagemaker_session,
    hyperparameters={
        'n-estimators': 10,
        'min-samples-leaf': 3,
        'max-depth': None
    }
)
```

Next, you can call the `fit` method of the `Estimator` object to start the training job:

```bash
sklearn.fit({'train': data_path})
```

Once the training job is complete, you can use the trained model to make predictions. To do this, you'll need to deploy the model to an endpoint using the `deploy` method of the `Estimator` object:

```bash
predictor = sklearn.deploy(initial_instance_count=1, instance_type='ml.m4.xlarge')
```

Finally, you can use the `predictor` object to make predictions on new data. The `predictor` object has a `predict` method that takes a NumPy array of input data and returns a NumPy array of predictions:

```bash
import numpy as np

data = np.array([[5.1, 3.5, 1.4, 0.2]])
predictions = predictor.predict(data)

print(predictions)
```

This is just a basic example of how you can use SageMaker to train and deploy a machine learning model. SageMaker provides many other features and tools that you can use to build more complex and powerful models.

### Security and Compliance

AWS takes security and compliance very seriously and provides many tools and services to help you secure your data and meet regulatory requirements. Some of the key security and compliance features of AWS include:

\- Identity and Access Management (IAM) – IAM allows you to control who has access to your AWS resources, and what actions they can perform. You can use IAM to create and manage users and groups and define fine-grained permissions using policies.

\- Encryption – AWS provides a range of options for encrypting your data at rest and in transit, including support for encryption in S3, EBS, and RDS, and the option to use your encryption keys with the AWS Key Management Service (KMS).

\- Compliance – AWS has several compliance programs and certifications, such as SOC, PCI DSS, and HIPAA, and provides tools and resources to help you meet compliance requirements for your specific use case.

\- Monitoring and Auditing – AWS provides several tools and services for monitoring and auditing your resources and activity, including CloudTrail, CloudWatch, and Config. These tools allow you to track changes to your resources, set alarms for specific events, and generate reports for compliance purposes.