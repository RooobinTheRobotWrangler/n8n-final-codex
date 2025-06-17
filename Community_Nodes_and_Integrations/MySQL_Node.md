# MySQL Node

**Source:** [n8n Docs - MySQL node documentation](https://docs.n8n.io/integrations/builtin/app-nodes/n8n-nodes-base.mysql/)

## Overview
The MySQL node enables n8n workflows to connect and perform operations directly on a MySQL database. It's a fundamental node for integrating n8n with applications that use MySQL for data storage, allowing for automation of data synchronization, reporting, record management, and custom database queries.

## Authentication: MySQL Database Credentials

To use the MySQL node, you need to provide n8n with the necessary database connection details.

1.  **Create New Credential:** In n8n, go to **Credentials** (sidebar) > **New Credential**.
2.  Search for **"MySQL"** and select **"MySQL Database"**.
3.  **Provide Details:** Fill in the following connection details:
    * **Host:** The hostname or IP address of your MySQL server (e.g., `localhost`, `my-mysql-db.example.com`).
    * **Port:** The port number MySQL is listening on (default is `3306`).
    * **Database:** The name of the specific database you want to connect to.
    * **User:** The username for connecting to the database.
    * **Password:** The password for the database user.
    * **SSL:** Options for connecting securely (e.g., `Require`, `Prefer`, `Disable`). `Require` is recommended for production.

## Configurable Options (Key Operations):

The MySQL node offers various "Operations" to manage your database data, each with specific parameters:

* **Operation:** The specific database action to perform.
* **Table Name:** The name of the table you want to interact with.

Here are some common Operations:

1.  **Select:**
    * **Purpose:** To retrieve records from a table.
    * **Parameters:** `Table Name`, `Columns` (specific columns to retrieve, default is all), `Where` (conditions to filter rows, e.g., `id = 1` or using expressions for dynamic values), `Order By`, `Limit`, `Offset`.
    * **Output:** Each retrieved database row becomes a separate n8n item, with column names as JSON keys.

2.  **Insert:**
    * **Purpose:** To add new records (rows) to a table.
    * **Parameters:** `Table Name`, `Columns` (names of columns to insert into), `Values` (data to insert, usually from previous nodes via expressions).
    * **Input:** Items containing the data for the new row(s). The JSON keys in the input should ideally match the database column names.

3.  **Update:**
    * **Purpose:** To modify existing records in a table.
    * **Parameters:** `Table Name`, `Columns` (columns to update), `Values` (new data for columns), `Where` (conditions to select rows to update).
    * **Input:** Items containing the update data and conditions to identify records.

4.  **Delete:**
    * **Purpose:** To remove records from a table.
    * **Parameters:** `Table Name`, `Where` (conditions to select rows to delete).
    * **Input:** Items containing conditions to identify records for deletion.

5.  **Execute SQL:**
    * **Purpose:** To run custom SQL queries directly. Provides maximum flexibility.
    * **Parameters:** `Query` (the SQL query string), `Parameters` (values to safely inject into the query).
    * **Input:** Can take input to construct dynamic queries or use parameters.
    * **Output:** The result of the SQL query.

6.  **Truncate:**
    * **Purpose:** To quickly remove all rows from a table.
    * **Parameters:** `Table Name`.

## Input Structure:
The input structure depends on the `Operation`. For `Insert` and `Update`, the node expects items where the JSON keys ideally match the target database column names for easy mapping. For `Select`, `Update`, `Delete` operations, conditions for `Where` clauses are often built using expressions from previous nodes.

```json
/* Example Input for Insert Operation */
[
  {
    "json": {
      "customer_name": "Alice Smith",
      "email": "alice@example.com",
      "signup_date": "2025-06-13"
    }
  }
]
Output Structure:
The output structure varies by Operation. For Select, each retrieved database row becomes a separate n8n item, with column names as JSON keys. For Insert, Update, Delete, Truncate, and Execute SQL, the output usually confirms the operation's success, often including details like the number of affected rows or the results of the query.

Example Output (from Select operation):

JSON

[
  {
    "json": {
      "id": 101,
      "customer_name": "Bob Johnson",
      "email": "bob@example.com"
    }
  },
  {
    "json": {
      "id": 102,
      "customer_name": "Carol White",
      "email": "carol@example.com"
    }
  }
]
Potential Use Cases:
Data Synchronization: Keeping a MySQL database in sync with data from CRMs, spreadsheets, or other APIs.
Reporting & Analytics: Extracting specific datasets for reports or dashboards.
Custom Database Operations: Executing complex stored procedures or custom SQL for specific business logic.
User Management: Adding or updating user records in a custom application's MySQL database.
Logging: Storing detailed logs of workflow executions or external events.
Dynamic Queries: Building and executing SQL queries based on dynamic data from previous nodes.
Tips & Best Practices:
SQL Injection Prevention: When using Execute SQL, always use Parameters (Query field with ? placeholders and Parameters array) instead of directly concatenating user-provided or dynamic data into the SQL string. This prevents security vulnerabilities.
Test Queries Thoroughly: Especially with Execute SQL, test your queries in a database client first to ensure they are correct and perform as expected.
Transactions: For multiple database operations that must succeed or fail together, consider using database transactions (if available in advanced settings or via custom SQL) to ensure data consistency.
Data Types: Be mindful of data types in your database columns versus the JSON data from n8n. Use Set or Code nodes to convert data types if necessary.
Error Handling: Implement robust error handling for database connection failures, SQL errors, or constraint violations.
Rate Limits/Load: For high-volume operations, use Split In Batches with delays to avoid overwhelming your database server.
Permissions: Ensure the database user account used by n8n has only the necessary permissions (e.g., read-only if only reading data).