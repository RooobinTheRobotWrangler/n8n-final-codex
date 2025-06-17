# Airtable Node

**Source:** [n8n Docs - Airtable node documentation](https://docs.n8n.io/integrations/builtin/app-nodes/n8n-nodes-base.airtable/)

## Overview
The Airtable node allows your n8n workflows to interact directly with your Airtable bases, tables, and records. It's an incredibly versatile node for automating data management tasks, from adding new records and updating existing ones to querying and deleting information in your Airtable databases.

## Authentication: Airtable API Key / OAuth

To use the Airtable node, you need to authenticate n8n with your Airtable account. The most common methods are:

1.  **Airtable API Key (Personal Access Token):**
    * **Description:** A long string of characters that grants n8n access to your Airtable account. This is the simplest method for quick connections. You can generate this from your Airtable account settings.
    * **Setup:** In n8n, go to **Credentials** > **New Credential**, search for "Airtable API Key," and paste your API key.
2.  **Airtable OAuth (for official n8n.cloud or advanced setups):**
    * **Description:** A more secure, token-based authorization method. This is generally preferred for production environments or when using n8n.cloud. It requires configuring an OAuth client in Airtable.
    * **Setup:** In n8n, go to **Credentials** > **New Credential**, search for "Airtable OAuth," and follow the prompts, which typically involve authorizing through Airtable's web interface.

## Configurable Options (Key Operations):

The Airtable node offers various "Operations" to manage your data, each with specific parameters:

* **Base ID:**
    * **Description:** The unique identifier for your Airtable Base. You can find this in the URL of your base (e.g., `https://airtable.com/appTHISISYOURBASEID/tbl...`).
* **Table Name or ID:**
    * **Description:** The name or unique ID of the specific table within your base you want to interact with.
* **Record ID:**
    * **Description:** The unique ID of a specific record in Airtable (starts with `rec...`). Used for operations like `Get`, `Update`, `Delete` a single record.

Here are some common Operations:

1.  **Get All:**
    * **Purpose:** To retrieve multiple records from a table.
    * **Parameters:** `Base ID`, `Table Name`, `View` (e.g., `Grid view`, `Gallery view`), `Filter By Formula` (Airtable formula to filter records), `Max Records`, `Sort` (sort by field), `Fields` (specific fields to return).
    * **Output:** Each retrieved Airtable record becomes a separate n8n item.

2.  **Create:**
    * **Purpose:** To add one or more new records to a table.
    * **Parameters:** `Base ID`, `Table Name`, `Value` (the data for the new record(s), usually from previous nodes via expressions).
    * **Input:** Items containing the data for the new record(s). The JSON keys in the input should match the field names in your Airtable table.

3.  **Update:**
    * **Purpose:** To modify existing records in a table.
    * **Parameters:** `Base ID`, `Table Name`, `Record ID` (for single record update) or `Update By Field` (for updating multiple records matching a field value), `Value` (data to update).
    * **Input:** Items containing the `Record ID` and the fields to update.

4.  **Delete:**
    * **Purpose:** To remove one or more records from a table.
    * **Parameters:** `Base ID`, `Table Name`, `Record ID` (for single record delete) or `Delete By Field` (for deleting multiple records matching a field value).
    * **Input:** Items containing the `Record ID` or matching field values to delete records.

## Input Structure:
The input structure for Airtable operations like `Create` and `Update` typically requires the incoming n8n items' JSON keys to match your Airtable field names. For `Update` and `Delete` of specific records, the `Record ID` is often passed as a property in the input item.

```json
/* Example Input for Create Record */
[
  {
    "json": {
      "Name": "New Task",
      "Status": "To Do",
      "Assignee": "John Doe"
    }
  }
]

/* Example Input for Update Record */
[
  {
    "json": {
      "id": "recABCXYZ", // This is the Airtable Record ID
      "Status": "Done",
      "Notes": "Task completed."
    }
  }
]
Output Structure:
The output structure varies by Operation. For Get All, each retrieved Airtable record becomes a separate n8n item, with Airtable field names as JSON keys. For Create, Update, Delete, the output often includes the Record ID of the affected records and status information.

Example Output (from Get All records):

JSON

[
  {
    "json": {
      "id": "rec001",
      "Name": "Project X",
      "Status": "In Progress",
      "Owner": "Alice"
    }
  },
  {
    "json": {
      "id": "rec002",
      "Name": "Project Y",
      "Status": "Completed",
      "Owner": "Bob"
    }
  }
]
Potential Use Cases:
CRM/Lead Management: Automating creation of new leads, updating customer statuses, or logging interactions.
Content Management: Managing blog post drafts, social media content, or asset libraries.
Project Management: Automating task creation, status updates, or progress tracking.
Form Submissions: Sending data from web forms directly into an Airtable database.
Inventory Tracking: Updating stock levels in Airtable based on sales or deliveries.
Simple Database Backend: Using Airtable as a backend for simple applications or dashboards driven by n8n.
Tips & Best Practices:
Match Field Names: Ensure the JSON keys in your n8n items exactly match the field names in your Airtable table for Create and Update operations.
Handle Record IDs: For updating or deleting specific records, you'll need the Airtable Record ID. Make sure your workflow can retrieve or generate these IDs.
Formula Filtering: Leverage Filter By Formula in "Get All" operations for powerful server-side filtering, reducing the amount of data n8n needs to process.
Rate Limits: Be aware of Airtable's API rate limits (typically 5 requests/second per base). Use the Wait node or Split In Batches with delays for high-volume operations.
Authentication Security: Always store your Airtable API Key securely in n8n Credentials. Avoid hardcoding it.
Error Handling: Implement error handling for common Airtable API errors (e.g., invalid record IDs, missing fields).