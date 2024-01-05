---
title: "Managing keys & environment variables in a python pipeline/app"
seoTitle: "Manage environment variables in python"
seoDescription: "How to manage environment variables & API keys in python projects"
datePublished: Tue Oct 31 2023 05:00:00 GMT+0000 (Coordinated Universal Time)
cuid: clqzzfdhp000008i47g5u4dvn
slug: managing-keys-environment-variables-in-a-python-pipelineapp
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/q7h8LVeUgFU/upload/90b48283709b4689885069889308e42a.jpeg
tags: python, secrets-management, environment-variables

---

In a production ETL (extract, transform, load) pipeline, it is often helpful to manage environment variables to store sensitive information, such as database credentials or API keys. This allows you to keep this sensitive information separate from your code and make it easier to deploy your pipeline to different environments.

There are several ways you can manage environment variables in a Python ETL pipeline:

1.  Use a library like `python-dotenv`: This library allows you to store environment variables in a `.env` file and then load them into your Python script using the `dotenv` library. This is a convenient way to manage environment variables, especially for development and testing.
    
2.  Use the built-in `os` module: The `os` module in Python provides functions for interacting with the operating system's environment variables. You can use the `os.environ` dictionary to access environment variables and the `os.getenv` function to retrieve the value of a specific environment variable.
    
3.  Use a configuration management tool: There are several tools available for managing environment variables and other configuration settings in a production environment. Examples include Ansible, Chef, and Puppet. These tools can help you automate the deployment and management of your ETL pipeline and make it easier to manage environment variables in different environments.
    

Here is an example of how you might use the `python-dotenv` library to manage environment variables in a Python ETL pipeline:

```bash
# Import the dotenv library
from dotenv import load_dotenv

# Load environment variables from a .env file
load_dotenv()

# Access an environment variable
database_username = os.getenv('DATABASE_USERNAME')
database_password = os.getenv('DATABASE_PASSWORD')

# Connect to the database using the environment variables
conn = psycopg2.connect(
    host='database_host',
    port='database_port',
    user=database_username,
    password=database_password,
    dbname='database_name'
)
```

This example shows how you can use the `load_dotenv` function to load environment variables from a `.env` file and then use the `os.getenv` function to retrieve the values of specific environment variables. You can then use these environment variables in your code to connect to a database, for example.

Here is an example of how you might use the `os` module to manage environment variables in a Python ETL pipeline:

```bash
# Import the os module
import os

# Access an environment variable
database_username = os.environ['DATABASE_USERNAME']
database_password = os.environ['DATABASE_PASSWORD']

# Connect to the database using the environment variables
conn = psycopg2.connect(
    host='database_host',
    port='database_port',
    user=database_username,
    password=database_password,
    dbname='database_name'
)

# You can also use the os.getenv function to retrieve the value of a specific environment variable
api_key = os.getenv('API_KEY')
```

In this example, we use the `os.environ` dictionary to access environment variables directly. We can also use the `os.getenv` function to retrieve the value of a specific environment variable.

It's worth noting that when using the `os` module, you will need to set the environment variables in your operating system before running your script. This can be done through the command line or through your operating system's environment variables management interface.

Using a configuration management tool like Ansible, Chef, or Puppet can also be a good option for managing environment variables in a production ETL pipeline. These tools allow you to automate the deployment and management of your pipeline and make it easier to manage environment variables in different environments.

For example, you can use ansible to define your environment variables in a configuration file and then use ansible to automate the deployment of your pipeline to different environments. This can make it easier to manage environment variables in a production environment and ensure that your pipeline is properly configured for each environment.