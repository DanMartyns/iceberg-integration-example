An example demonstrating how to integrate Apache Iceberg into AWS Glue Jobs with PySpark.

# Advantages of Iceberg
Here are some key advantages of using Iceberg tables:

1. Schema Evolution
    1. **Backward and Forward Compatibility**: Iceberg supports schema evolution without rewriting the data. This means you can add, drop, or rename columns without affecting existing queries or having to reload the data.
    2. **Automatic Updates**: Changes to the schema are automatically propagated, making it easier to manage evolving data structures.
3. Partition Evolution
    1. **Dynamic Partitioning**: Iceberg allows you to change partitioning strategies without needing to rewrite your data. This flexibility helps optimize query performance as data patterns change over time.
4.  Hidden Partitioning
    1. **Simplified Queries**: Partitioning logic is hidden from users, simplifying query syntax. Users don't need to include partitioning columns in their queries, which reduces the complexity of query writing.
5. Snapshot Isolation
    1. **ACID Transactions**: Iceberg provides ACID transactions, ensuring data consistency and reliability. You can perform concurrent reads and writes without conflicts.
    2. **Time Travel**: Iceberg supports time travel, allowing you to query historical versions of your data. This is useful for auditing, debugging, and rollback scenarios.

# How to enable Iceberg
1. Add two libraries to our project - You can find them here: [Iceberg Releases](https://iceberg.apache.org/releases/)
    1. iceberg-spark-runtime-3.3_2.12-1.6.0.jar
    2. iceberg-aws-bundle-1.6.0.jar
2. Add the following configuration to the AWS Glue Job
```json
{
  "--conf": "spark.sql.extensions=org.apache.iceberg.spark.extensions.IcebergSparkSessionExtensions", /* By including this configuration, you enable the features and capabilities provided by Iceberg in your Spark SQL sessions, such as advanced table management, versioned data handling, and more efficient querying capabilities. */
}
```

# Standardized Implementation

## Architecture
![Iceberg Controller](https://github.com/user-attachments/assets/3e71e5db-5f89-4b78-9904-bc39877e6f75)

### TableMetadata Class
  - **Purpose**: This class serves as a data structure to hold metadata about a table, such as its name, path, primary keys, schema name, description, and optional partitioning information.
  - **Role**: It is likely passed around between different components or methods that need to reference specific tables and their properties.
  - **Relation**: TableMetadata is used as an input for the methods in IcebergManager, indicating that IcebergManager performs operations on tables defined by instances of TableMetadata.

### SchemaManager Class
  - **Purpose**: This class handles operations related to schema management, such as fetching a schema, comparing schemas, and enforcing schema constraints on data.
  - **Role**: It focuses solely on schema-related concerns.
  - **Relation**: SchemaManager is used by IcebergManager to ensure that any table operations conform to the expected schema. For example, before updating or creating a table, IcebergManager might leverage SchemaManager to validate or enforce the schema.

### IcebergManager Class
  - **Purpose**: This class manages operations related to Iceberg tables, including creation, updates, property management, and data compression.
  - **Role**: It acts as the central manager for performing operations on Iceberg tables, utilizing the metadata defined in TableMetadata and possibly interacting with SchemaManager to ensure schema consistency.
  - **Relation**: It heavily relies on TableMetadata for input on which tables to manage and how. It also calls methods from SchemaManager to handle schema-related tasks.
