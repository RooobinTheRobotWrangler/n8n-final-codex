# Dead Letter Queues (DLQ)

**Source:** [n8n Docs - Error Handling](https://docs.n8n.io/integrations/error-handling/) (general context) and best practices for resilient systems.

## Overview
A Dead Letter Queue (DLQ) is a designated storage location for messages or data items that a system (in this case, an n8n workflow) failed to process successfully, even after multiple attempts (like retries). Instead of discarding these "problem" items, they are moved to the DLQ for later inspection, debugging, or manual reprocessing. DLQs are a critical component of highly resilient and fault-tolerant automation systems.

## Why Use a DLQ?

* **Prevent Data Loss:** Ensures that data that couldn't be processed isn't simply lost.
* **Facilitate Debugging:** Provides a central place to find and analyze problematic items, allowing you to understand *why* they failed.
* **Improve Workflow Reliability:** Allows the main workflow to continue processing other items, even if a few consistently fail, rather than halting the entire process.
* **Manual Intervention Point:** Creates a clear point where human review or intervention can occur for complex or unusual errors.
* **Auditing:** Provides a record of failed processing attempts.

## How to Implement a DLQ in n8n

Implementing a DLQ typically involves routing items that have exhausted their retries (or failed due to non-retriable errors) to a persistent storage location.

1.  **Identify Failed Items:**
    * **From "Continue On Error" branches:** If a node has "Continue On Error" enabled, items that fail in that node will exit through its error output branch.
    * **From `Error` nodes:** If you use an `Error` node to intentionally fail items based on custom logic (e.g., missing critical data), these items will trigger the Error Workflow.
    * **From exhausted Retry Loops:** Items that have been retried a maximum number of times and still failed should be directed to the DLQ.

2.  **Route to a Storage Node:**
    * Connect the output of your error handling logic (e.g., from an error branch or an Error Workflow) to a node that can store data persistently.
    * **Common DLQ Storage Options in n8n:**
        * **Google Sheets:** A simple and accessible option for logging failed items in a spreadsheet.
        * **Airtable:** Flexible database for structured logging and easy review.
        * **Database (e.g., PostgreSQL, MySQL):** For more robust, programmatic access to failed items.
        * **Cloud Storage (e.g., Google Drive, Amazon S3):** Good for storing failed items, especially if they involve binary data (like failed file uploads).
        * **Specific Logging Services (e.g., Datadog, Splunk):** If integrated, for centralized error monitoring.

3.  **Include Error Context:**
    * When sending items to the DLQ, always include crucial context:
        * The original item data that failed.
        * The error message and code (`{{ $json.error.message }}`, `{{ $json.error.code }}` from an Error Trigger node).
        * The name of the workflow and node where the error occurred.
        * A timestamp of when the error happened.
        * The retry count (if applicable).
    * Use a `Set` node before the DLQ storage node to structure this error context cleanly.

## Input Structure for DLQ Implementation

Items arriving at a DLQ strategy usually contain their original data along with error details, often added by n8n's error handling mechanisms or custom `Set`/`Code` nodes.

```json
/* Example Item arriving at a DLQ route */
[
  {
    "json": {
      "originalData": {
        "orderId": "ORD-XYZ",
        "customerEmail": "bad@example.com"
      },
      "errorDetails": {
        "message": "Invalid email format for customerEmail",
        "code": "VALIDATION_ERROR",
        "node": "Email Validation Node",
        "workflow": "Customer Onboarding Flow"
      },
      "timestamp": "2025-06-13T16:30:00Z",
      "retryAttempts": 3
    }
  }
]
Output Structure for DLQ (Conceptual)
A DLQ implementation typically results in the storage of data in an external system. The "output" of the DLQ segment of your workflow is usually that the item has been successfully logged/stored, and the workflow run itself continues or completes.

Potential Use Cases for DLQ:
Failed API Calls: Storing details of API requests that consistently fail due to bad data or unexpected responses.
Invalid Data Handling: Quarantining records that don't meet validation criteria and can't be automatically corrected.
Unprocessed Messages: Ensuring that messages from queues or streams that couldn't be processed are not lost.
Debugging Complex Workflows: Providing a detailed audit trail of failed items in a complex, multi-step automation.
Tips & Best Practices for DLQs:
Centralized DLQ: Consider having a single, well-defined DLQ location (e.g., one specific Google Sheet) for all failed items across multiple workflows to simplify monitoring.
Clear Schema: Define a consistent schema for the data you store in your DLQ (e.g., columns in a spreadsheet) to make analysis easier.
Alerting on DLQ Additions: Set up monitoring or alerts on your DLQ (e.g., a separate n8n workflow that triggers when a new row is added to the DLQ spreadsheet) to know immediately when items are being quarantined.
Manual Review Process: Establish a process for regularly reviewing and attempting to reprocess or fix items in your DLQ.
Separate from Main Workflow: Ensure your DLQ logic is robust and doesn't interfere with the main workflow's critical path.