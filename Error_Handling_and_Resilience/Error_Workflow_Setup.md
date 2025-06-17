# Error Workflow Setup

**Source:** [n8n Docs - Error Handling](https://docs.n8n.io/integrations/error-handling/)

## Overview
Dedicated Error Workflows are separate n8n workflows that are automatically triggered whenever an unhandled error occurs in any of your *other* active workflows. This provides a centralized and robust way to manage failures, allowing you to execute specific actions like sending notifications, logging errors, or moving problematic data to a "dead letter queue" for later review.

## How to Set Up an Error Workflow

Setting up an Error Workflow involves two main parts:

1.  **Creating the Error Workflow itself.**
2.  **Configuring your n8n instance to trigger it.**

### Part 1: Creating the Error Workflow

1.  **Create a New Workflow:** In your n8n instance, create a brand new, empty workflow.
2.  **Add an "Error Trigger" Node:**
    * This is a special trigger node specifically designed to be activated by errors from other workflows.
    * Add an **Error Trigger** node as the very first node in this new workflow.
    * This node automatically receives details about the error that occurred (e.g., workflow name, node name, error message, and even the data that caused the error).
3.  **Define Error Handling Logic:**
    * Connect subsequent nodes to the Error Trigger node to define what actions should be taken when an error is caught.
    * **Common actions include:**
        * **Notifications:** Send an email, Slack message, or push notification to alert the relevant team or person about the error.
        * **Logging:** Write error details to a database, spreadsheet (e.g., Google Sheets), or a log file for auditing and later analysis.
        * **Dead Letter Queues (DLQ):** Move the data that caused the error to a designated storage location (e.g., a specific row in a Google Sheet, a record in Airtable, a file in cloud storage) for manual inspection or retry.
        * **Metrics:** Increment a counter in a monitoring system.
        * **Automated Retries (Advanced):** In some cases, the error workflow might trigger a retry mechanism for the original workflow (though this requires careful design to prevent infinite loops).
4.  **Activate the Error Workflow:** Make sure to activate this workflow (turn it ON) once it's built, just like any other workflow.

### Part 2: Configuring n8n to Trigger the Error Workflow

This step tells your n8n instance *which* workflow to run when an error occurs.

1.  **Go to n8n Settings:** In your n8n UI, click on **"Settings"** in the sidebar.
2.  **Navigate to "Workflows":** Within Settings, find and click on the **"Workflows"** tab or section.
3.  **Set "On Error Workflow":**
    * You will see an option called **"On Error Workflow"** (or similar).
    * From the dropdown menu, **select the Error Workflow you just created** (by its name).
    * **Save** the settings.

Now, whenever an active workflow (that doesn't have its own node-level "Continue On Error" setting enabled to catch all items) throws an unhandled error, n8n will automatically trigger the selected Error Workflow.

## Input Structure of the Error Trigger Node

The Error Trigger node receives a specific payload containing details about the error. This payload is crucial for understanding what went wrong.

```json
/* Example JSON received by Error Trigger node */
[
  {
    "json": {
      "workflow": {
        "id": "workflow-id-123",
        "name": "My Main Data Processing Workflow",
        "url": "[https://your.n8n.instance/workflow/workflow-id-123](https://your.n8n.instance/workflow/workflow-id-123)"
      },
      "node": {
        "id": "node-id-xyz",
        "name": "HTTP Request",
        "type": "n8n-nodes-base.httpRequest"
      },
      "error": {
        "message": "Request failed with status code 404",
        "code": "404_NOT_FOUND",
        "stack": "...", // JavaScript stack trace if applicable
        "description": "The requested resource was not found on the server."
      },
      "data": [
        // This array contains the items that caused the error
        {
          "json": {
            "recordId": "problematic_id",
            "urlToFetch": "[https://api.example.com/data/notfound](https://api.example.com/data/notfound)"
          },
          "binary": {}
        }
      ],
      "execution": {
        "id": "execution-id-456",
        "url": "[https://your.n8n.instance/workflow/execution-id-456](https://your.n8n.instance/workflow/execution-id-456)",
        "mode": "manual", // or webhook, cron, etc.
        "startedAt": "2025-06-13T10:00:00.000Z"
      }
    }
  }
]
(You will use expressions like {{ $json.workflow.name }} or {{ $json.error.message }} in your error handling logic.)

Potential Use Cases for Error Workflows:
Centralized Alerting: All unhandled errors trigger a single point of notification.
Automated Error Logging: Create a consistent record of all workflow failures for auditing and troubleshooting.
"Bad Data" Quarantining: Automatically move data that caused an error to a holding area for manual inspection and correction.
Workflow Monitoring: Integrate with external monitoring tools to track workflow health.
Tips & Best Practices:
Keep Error Workflows Simple: Avoid complex logic within the error workflow itself to ensure it runs reliably even under stress. Focus on notification and logging.
Avoid Infinite Loops: Be very careful if your error workflow tries to re-trigger the failing workflow, as this can create an infinite loop if the error persists.
Test Your Error Workflow: Deliberately cause an error in a test workflow to ensure your Error Workflow triggers and functions as expected.
Use Error Trigger Node: Always start an Error Workflow with the specific Error Trigger node.
Handle Binary Data: If your main workflow handles binary data that might cause errors, ensure your Error Workflow is capable of receiving and logging/storing that binary data.