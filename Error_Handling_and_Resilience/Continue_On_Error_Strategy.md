# "Continue On Error" Strategy

**Source:** [n8n Docs - Error Handling](https://docs.n8n.io/integrations/error-handling/) and [n8n Docs - Basic Error Handling](https://docs.n8n.io/integrations/error-handling/basic-error-handling/)

## Overview
The "Continue On Error" setting is a fundamental strategy for building resilient n8n workflows. It's an option available on many individual nodes that dictates how the workflow should behave if that specific node encounters an error while processing an item. When enabled, it allows the workflow to continue executing for other items or subsequent nodes, even if one or more items cause a failure in that particular node.

## How it Works

* **Location:** You'll find "Continue On Error" as a checkbox or toggle within the settings of many n8n nodes (e.g., `HTTP Request`, database nodes, app nodes). It's typically located under a section like "Error Handling" or "Settings."
* **Default Behavior (Off):** By default, if "Continue On Error" is *not* enabled and an error occurs for any item, the entire workflow execution will stop at that point, and the workflow run will be marked as failed. No further items will be processed by that node or any subsequent nodes.
* **Enabled Behavior (On):** When "Continue On Error" is enabled:
    * If an item causes an error in that node, that specific item will be moved to a **separate output branch** of the node (usually labeled "Errors" or similar).
    * The workflow execution for **other successful items** (those that did not cause an error in that node) will continue normally through the node's primary output.
    * The workflow run itself will generally **not fail immediately** at that node, but will proceed.

## Input Structure
The "Continue On Error" setting applies to nodes that receive input items. The node attempts to process each item.

```json
/* Example Input: Multiple items, some may cause errors */
[
  { "json": { "userId": 1, "data": "valid_data" } },
  { "json": { "userId": 2, "data": "invalid_data_causing_error" } },
  { "json": { "userId": 3, "data": "another_valid_data" } }
]
Output Structure
When "Continue On Error" is active, the node will have two outputs:

Primary Output (Success): Contains all items that were processed successfully by the node.
Secondary Output (Errors): Contains the items that caused an error in the node, along with error details (often in a _error property added to the item's JSON).
Example Output (if item 2 causes error):

Primary Output (Success Branch):

JSON

[
  { "json": { "userId": 1, "processed_data": "..." } },
  { "json": { "userId": 3, "processed_data": "..." } }
]
Secondary Output (Error Branch):

JSON

[
  {
    "json": {
      "userId": 2,
      "data": "invalid_data_causing_error",
      "_error": {
        "message": "Specific error message from node",
        "code": "ERROR_CODE",
        "stack": "..."
      }
    }
  }
]
Potential Use Cases:
Partial Success: When you want to process as many items as possible from a list, even if some fail (e.g., updating a batch of customer records where some IDs might be invalid).
Robust Data Ingestion: Receiving data from an external source where some records might be malformed, but you want to process the valid ones and log the invalid ones separately.
Non-Blocking API Calls: Making API calls where a failure for one request shouldn't halt the entire workflow for others.
Separate Error Logging/Notification: Routing failed items to a specific branch to log their errors, send notifications, or move them to a "dead letter queue" for manual review, without stopping the main workflow.
Tips & Best Practices:
Always Handle the Error Branch: If you enable "Continue On Error," make sure to connect a downstream node to the error output. Otherwise, the error is still caught, but the item that caused it might be silently discarded if nothing is connected to receive it. Common connections include Set (to format error data), Log (to record), Email or Slack (for notifications), or a database node.
Do Not Use for Critical Failures: Avoid using "Continue On Error" if an error indicates a critical issue that must stop the entire workflow (e.g., database connection failure, critical authentication error). For such cases, let the workflow fail to ensure immediate attention.
Combine with Error Workflows: While "Continue On Error" handles node-level item failures, a dedicated Error Workflow is still crucial for catching unexpected, workflow-wide errors.
Clear Naming: When using error branches, name the connected nodes clearly (e.g., "Log Failed Record," "Notify Admin of Invalid Data") to indicate their purpose.
Idempotency: When retrying or re-processing items from an error branch, ensure your workflow is idempotent to prevent duplicate actions.