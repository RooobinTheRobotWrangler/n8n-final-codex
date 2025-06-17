# Google Sheets Node

**Source:** [n8n Docs - Google Sheets node documentation](https://docs.n8n.io/integrations/builtin/app-nodes/n8n-nodes-base.googleSheets/)

## Overview
The Google Sheets node allows you to automate interactions with Google Sheets spreadsheets directly from your n8n workflows. This is incredibly powerful for tasks like data logging, reporting, managing lists, or even using spreadsheets as simple databases for your automations. It integrates seamlessly with your Google Account.

## Authentication: Google OAuth2

To use the Google Sheets node, you need to authenticate n8n with your Google Account using **Google OAuth2**.

1.  **Create New Credential:** In n8n, go to **Credentials** (sidebar) > **New Credential**.
2.  Search for **"Google"** and select **"Google OAuth2 API"**.
3.  **Choose Scope:** Select the necessary scopes. For Google Sheets, common scopes are:
    * `Google Sheets (read-only)`: `https://www.googleapis.com/auth/spreadsheets.readonly`
    * `Google Sheets (full)`: `https://www.googleapis.com/auth/spreadsheets`
    * `Google Drive (files)`: `https://www.googleapis.com/auth/drive.file` (often needed if you want to select sheets via their file ID from Drive).
4.  **Connect Account:** Follow the prompts to sign into your Google Account and grant n8n the requested permissions.

## Configurable Options (Key Operations):

The Google Sheets node has various "Operations" you can perform, each with its own set of parameters:

* **Sheet ID / Spreadsheet ID:**
    * **Description:** The unique ID of the Google Sheet you want to interact with. You can find this in the sheet's URL (e.g., `https://docs.google.com/spreadsheets/d/THIS_IS_THE_ID/edit`).
* **Sheet Name:**
    * **Description:** The specific tab (worksheet) within the Google Sheet to operate on (e.g., `Sheet1`, `Orders`).
* **Range:**
    * **Description:** For operations like `Read` or `Clear`, you can specify a cell range (e.g., `A1:C10`, `Sheet2!D:D`).
* **Options common to many operations:**
    * **Column Name as Key:** If checked, the first row of your sheet will be treated as column headers, and n8n will use these names as JSON keys in the output. Highly recommended.
    * **Return All Fields:** For Read operations, if checked, returns all fields in the selected range.

Here are some common Operations:

1.  **Read Sheet:**
    * **Purpose:** To retrieve data from a Google Sheet.
    * **Parameters:** `Sheet ID`, `Sheet Name`, `Range`, `Limit` (number of rows to read), `Column Name as Key`, `Return All Fields`.
    * **Output:** Each row in the specified range becomes a separate n8n item, with column headers as JSON keys.

2.  **Append Row:**
    * **Purpose:** To add new data as new rows to the end of a sheet.
    * **Parameters:** `Sheet ID`, `Sheet Name`, `Column Name as Key`, `Value` (the data to append, usually from previous nodes via expressions).
    * **Input:** Items containing the data for the new row(s). The JSON keys should match the sheet's column headers if `Column Name as Key` is used.

3.  **Update Row:**
    * **Purpose:** To modify existing rows in a sheet.
    * **Parameters:** `Sheet ID`, `Sheet Name`, `Update By` (e.g., `Row Number`, `Column Name`), `Row Number`, `Value` (data to update).
    * **Input:** Items containing the data to update and a way to identify the row (e.g., row number or a key value).

4.  **Delete Row:**
    * **Purpose:** To remove specific rows from a sheet.
    * **Parameters:** `Sheet ID`, `Sheet Name`, `Delete By` (e.g., `Row Number`, `Column Name`), `Row Number`.
    * **Input:** Items containing the identifier for the row(s) to delete.

5.  **Clear Sheet:**
    * **Purpose:** To clear all content from a sheet or a specific range without deleting the sheet itself.
    * **Parameters:** `Sheet ID`, `Sheet Name`, `Range`.

## Input Structure:
The input structure depends heavily on the `Operation`. For `Append` and `Update` operations, the node expects items where the JSON keys match the column headers in your Google Sheet (if "Column Name as Key" is used).

```json
/* Example Input for Append Row */
[
  {
    "json": {
      "Name": "New Product",
      "Price": 99.99,
      "Category": "Electronics"
    }
  }
]
Output Structure:
The output structure varies by Operation. For Read Sheet, it outputs an array of items, where each item's json property represents a row from the spreadsheet. For Append, Update, Delete, Clear, the output often reflects the success of the operation, sometimes including metadata about the affected rows.

Example Output (from Read Sheet operation):

JSON

[
  {
    "json": {
      "Name": "Laptop",
      "Price": "1200",
      "Category": "Electronics"
    }
  },
  {
    "json": {
      "Name": "Mouse",
      "Price": "25",
      "Category": "Peripherals"
    }
  }
]
Potential Use Cases:
Logging Data: Recording website form submissions, API responses, or workflow errors into a spreadsheet.
Reporting: Generating daily/weekly reports by pulling data from a sheet, processing it, and sending it out.
Simple Database: Using a Google Sheet as a lookup table or a simple database for workflow configuration data.
Data Migration: Moving data between Google Sheets and other systems (CRMs, marketing tools).
Inventory Management: Updating stock levels based on sales or new deliveries.
Budget Tracking: Automating expense logging or financial reporting.
Tips & Best Practices:
Service Account vs. OAuth: For automated, non-interactive workflows, consider using a Service Account credential if your n8n instance supports it, as it's often more robust than personal OAuth2 tokens for background tasks.
"Column Name as Key": Always enable this if your sheet has headers. It makes data manipulation much easier and more readable in n8n.
Expressions for Dynamic IDs/Names: Use expressions to dynamically select Sheet ID or Sheet Name if your workflow interacts with multiple sheets.
Handle Data Types: Google Sheets can be finicky with data types (numbers vs. strings). You might need to use Set or Code nodes to ensure data is in the correct format before writing to the sheet, or to convert data after reading.
Range Limitations: Be mindful of the maximum number of cells Google Sheets can handle per API call. For very large sheets, consider using Split In Batches with Read Sheet to paginate.
Error Handling: Implement error handling for potential issues like invalid sheet IDs, non-existent sheet names, or API rate limits from Google.
Sharing Permissions: Ensure the Google Sheet you are trying to access has the correct sharing permissions for the Google Account n8n is authenticated with.