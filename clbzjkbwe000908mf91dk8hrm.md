# Setting up dbt with Snowflake

dbt (data build tool) is an open-source command-line tool that helps data analysts and data engineers automate the process of transforming and loading data from various sources into a data warehouse. In this tutorial, we will be setting up dbt with Snowflake, a popular cloud-based data warehouse.

Prerequisites

*   A Snowflake account
    
*   Python 3 and pip installed on your machine
    
*   dbt installed on your machine (instructions can be found [**here**](https://docs.getdbt.com/docs/installation/local-installation))
    

Setting up dbt with Snowflake

1.  First, you need to create a new database and a new schema in Snowflake. This can be done through the Snowflake web UI or by running the following SQL commands:
    

```sql
CREATE DATABASE my_database;
USE DATABASE my_database;
CREATE SCHEMA my_schema;
```

2.  Next, you need to create a new role in Snowflake that will be used to run dbt. This can also be done through the Snowflake web UI or by running the following SQL command:
    

```sql
CREATE ROLE my_dbt_role;
```

3.  Now, you need to grant the necessary permissions to the dbt role you just created. Run the following SQL commands to grant SELECT, INSERT, UPDATE, DELETE, and CREATE PROCEDURE permissions to the dbt role:
    

```sql
GRANT SELECT ON SCHEMA my_schema TO ROLE my_dbt_role;
GRANT INSERT ON SCHEMA my_schema TO ROLE my_dbt_role;
GRANT UPDATE ON SCHEMA my_schema TO ROLE my_dbt_role;
GRANT DELETE ON SCHEMA my_schema TO ROLE my_dbt_role;
GRANT CREATE PROCEDURE ON SCHEMA my_schema TO ROLE my_dbt_role;
```

4.  Next, you need to create a new warehouse in Snowflake that will be used by dbt. This can be done through the Snowflake web UI or by running the following SQL command:
    

```sql
CREATE WAREHOUSE my_warehouse
  WITH
    AUTO_SUSPEND = 3600
    AUTO_RESUME = TRUE
    MIN_CLUSTER_COUNT = 1
    MAX_CLUSTER_COUNT = 3
    SCALING_POLICY = standard;
```

5.  Now, you need to create a new database user in Snowflake that will be used by dbt to authenticate and connect to the Snowflake database. This can also be done through the Snowflake web UI or by running the following SQL command:
    

```sql
CREATE USER my_dbt_user PASSWORD = 'my_password';
```

6.  Finally, you need to grant the necessary permissions to the dbt user you just created. Run the following SQL commands to grant USAGE and SELECT privileges to the dbt user:
    

```sql
GRANT USAGE ON WAREHOUSE my_warehouse TO USER my_dbt_user;
GRANT SELECT ON DATABASE my_database TO USER my_dbt_user;
```

Creating a dbt project

1.  Navigate to the directory where you want to create your dbt project and run the following command:
    

```bash
dbt init
```

This will create a new dbt project and generate the necessary files and directories.

2.  Open the `profiles.yml` file in the `~/.dbt` directory and add the following content to it, replacing the placeholders with your own Snowflake account, role, user, and password:
    

```yaml
my_profile:
  outputs:
    my_database:
      type: snowflake
      account: <your_snowflake_account>
      role: my_dbt_role
      user: my_dbt_user
      password: <your_password>
      warehouse: my_warehouse
      database: my_database
      schema: my_schema
```

This will create a new dbt profile called `my_profile` that can be used to connect to your Snowflake database.

Writing dbt models

dbt models are SQL scripts that define the transformations and calculations to be performed on your data. They can be written in either Jinja or pure SQL.

Here is an example of a dbt model written in Jinja:

```yaml
Copy code{{
  config(
    materialized='view',
    unique_key='id'
  )
}}

select *
from {{ ref('my_table') }}
```

This model simply selects all columns from a table called `my_table` and materializes the result as a view.

Here is an example of a dbt model written in pure SQL:

```sql
create or replace view {{ this }} as
select *,
       upper(name) as name_upper
from {{ ref('my_table') }}
```

This model selects all columns from `my_table` and adds an additional column called `name_upper` that contains the uppercase version of the `name` column.

Running dbt

To run your dbt project and execute the models, run the following command:

```bash
dbt run
```

This will execute all of the models in your project and create the necessary tables and views in your Snowflake database.

You can also run specific models by specifying their names:

```bash
dbt run --models my_model_1 my_model_2
```

You can also use the `dbt test` command to verify that your models are producing the expected results.

Conclusion

In this tutorial, we learned how to set up dbt with Snowflake and how to use it to automate the process of transforming and loading data into a data warehouse. We also saw some examples of how to write dbt models and run them in a dbt project. I hope this helps you get started with dbt and Snowflake!