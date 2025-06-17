# Notion Node

**Source:** [n8n Docs - Notion node documentation](https://docs.n8n.io/integrations/builtin/app-nodes/n8n-nodes-base.notion/)

## Overview
The Notion node allows your n8n workflows to interact with Notion pages, databases, and content. It's a powerful tool for automating tasks within your Notion workspace, such as managing project tasks, updating content, creating new records in databases, or extracting information for reports.

## Authentication: Notion API Key / OAuth

To use the Notion node, you need to authenticate n8n with your Notion account. The primary method is the **Notion API Key (Internal Integration Token)**.

1.  **Create New Integration in Notion:**
    * Go to your Notion workspace settings > Integrations > Develop your own integrations.
    * Click "New integration." Give it a name and select the workspace.
    * You'll get an "Internal Integration Token" (your API Key). Copy this.
2.  **Add to n8n Credentials:**
    * In n8n, go to **Credentials** (sidebar) > **New Credential**.
    * Search for "Notion API" and select "Notion API."
    * Paste your "Internal Integration Token" into the "Token" field.
    * Save the credential.
3.  **Share Database/Page with Integration:** For n8n to access specific databases or pages, you must explicitly "share" them with your Notion integration.
    * Open the Notion database or page you want to access.
    * Click the `...` (More options) menu in the top right.
    * Click "Add connections" and search for the name of your n8n integration.

## Configurable Options (Key Operations):

The Notion node offers various "Operations" to manage your Notion data, each with specific parameters:

* **Database ID:**
    * **Description:** The unique identifier for a Notion database. You can find this in the URL of a database (e.g., `https://www.notion.so/YOUR_WORKSPACE/YOUR_DATABASE_ID?v=...`).
* **Page ID:**
    * **Description:** The unique identifier for a Notion page.
* **Property Name / Property ID:**
    * **Description:** The name or ID of a property (column) within a Notion database.
* **Value:**
    * **Description:** The data you want to send for a specific property (e.g., text, numbers, dates, select options). This usually comes from expressions.

Here are some common Operations:

1.  **Create Database Item:**
    * **Purpose:** To add a new record (row) to a Notion database.
    * **Parameters:** `Database ID`, and the `Properties` (fields) you want to set for the new item.
    * **Input:** Items containing the data for the new database item. The JSON keys in the input should match the Notion database's property names.

2.  **Get Database Items:**
    * **Purpose:** To retrieve multiple records from a Notion database.
    * **Parameters:** `Database ID`, `Filter by` (Notion query object to filter records based on property values), `Sort by` (sort records), `Limit` (max records to return).
    * **Output:** Each retrieved Notion database item becomes a separate n8n item.

3.  **Update Database Item:**
    * **Purpose:** To modify an existing record in a Notion database.
    * **Parameters:** `Database Item ID` (the unique ID of the record to update), and the `Properties` (fields) you want to change.
    * **Input:** Items containing the `Database Item ID` and the fields to update.

4.  **Append Block:**
    * **Purpose:** To add new content blocks (like paragraphs, headings, lists) to an existing page or database item.
    * **Parameters:** `Parent ID` (ID of the page/item), `Children` (the blocks to append, specified in Notion's block JSON format).
    * **Input:** Items containing the `Parent ID` and the block data.

5.  **Search:**
    * **Purpose:** To search for pages and databases in your Notion workspace.
    * **Parameters:** `Query` (search term), `Filter by` (e.g., page or database type).
    * **Output:** Returns a list of matching pages or databases.

## Input Structure:
The input structure for `Create Database Item` and `Update Database Item` operations typically requires the incoming n8n items' JSON keys to match your Notion database's property names. For `Update`, you'll also need the `Database Item ID`.

```json
/* Example Input for Create Database Item */
[
  {
    "json": {
      "Name": "New Task from n8n",
      "Status": { "name": "To Do" },
      "Tags": [{ "name": "Automation" }]
    }
  }
]
Output Structure:
The output structure varies by Operation. For Get Database Items, each retrieved Notion record becomes a separate n8n item, with Notion properties converted to JSON keys. For Create, Update, Append, the output typically includes the ID and details of the newly created or modified Notion object.

Example Output (from Get Database Items operation):

JSON

[
  {
    "json": {
      "id": "page-id-xyz",
      "properties": {
        "Name": { "title": [{ "plain_text": "My Project" }] },
        "Status": { "select": { "name": "Active" } }
      },
      "url": "[https://www.notion.so/](https://www.notion.so/)...",
      "created_time": "2025-06-14T..."
    }
  }
]
Potential Use Cases:
Project Management Automation: Creating new tasks, updating task statuses, or logging meeting notes.
Content Calendar: Automating creation of blog post entries, tracking their status, and publishing.
Lead Management: Adding new leads from forms or CRM systems into a Notion database.
Documentation Management: Keeping Notion pages updated with information from other services.
To-Do List Sync: Syncing tasks from other applications to your Notion to-do lists.
Tips & Best Practices:
Share with Integration: Remember to explicitly share your Notion databases and pages with your n8n integration after creating the integration in Notion.
Notion's Data Structure: Notion's API uses a complex JSON structure for properties (especially for rich text, select, multi-select, etc.). Be prepared to map your simple JSON keys to Notion's nested property objects using Set or Code nodes.
Database ID vs. Page ID: Understand when to use a Database ID (for interacting with database rows/items) versus a Page ID (for interacting with a standalone page or a database item as a page).
Filtering & Sorting: Leverage Notion's rich filtering and sorting capabilities directly in the node's parameters to retrieve only the data you need, reducing processing overhead.
Rich Text: Notion's rich text fields require specific JSON objects. For simple text, you can often use plain_text or content properties directly. For advanced formatting, you might need to build the complex JSON structure.
Rate Limits: Be mindful of Notion's API rate limits (typically 3 requests per second). Use Wait or Split In Batches for high-volume operations.