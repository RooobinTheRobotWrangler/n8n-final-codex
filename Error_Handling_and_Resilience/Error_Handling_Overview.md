# Error Handling and Resilience Overview

**Source:** [n8n Docs - Error Handling](https://docs.n8n.io/integrations/error-handling/)

## Overview
Building resilient n8n workflows means designing them to anticipate, detect, and respond gracefully to unexpected issues or errors. Errors can occur for many reasons: external API failures, invalid data, network issues, or unhandled logic in custom code. Effective error handling prevents workflows from simply stopping and ensures data integrity, proper notifications, and potential recovery mechanisms.

## Why is Error Handling Important?

* **Reliability:** Ensures workflows continue to run reliably even when external services fail or data is unexpected.
* **Data Integrity:** Prevents corrupted or partial data from being processed and stored.
* **Proactive Notification:** Alerts you immediately when something goes wrong, allowing for quick intervention.
* **User Experience:** For workflows interacting with users, it provides clear feedback instead of silent failures.
* **Resource Management:** Prevents workflows from running indefinitely or consuming excessive resources due to unhandled issues.

## Common Types of Errors in n8n

* **Node-level Errors:** An individual node fails to execute successfully (e.g., HTTP Request gets a 404 error, a database query fails).
* **Data Errors:** Incoming data is malformed, missing required fields, or doesn't meet expected criteria, causing subsequent nodes to fail.
* **Logic Errors:** Issues within custom JavaScript (Code or Function nodes) that lead to unhandled exceptions.
* **Infrastructure Errors:** Problems with the n8n instance itself, network connectivity, or external service outages.

## Key Strategies for Resilience

1.  **Node-Level Error Handling (`Continue On Error`):**
    * Many individual nodes have a "Continue On Error" or similar setting.
    * **Purpose:** This allows a single node to fail for an item without stopping the entire workflow run for *all* items. Failed items are typically moved to a separate branch or output, while successful items continue.
    * **When to use:** When you want to process as much data as possible, even if some items cause errors (e.g., trying to update 100 records, and 5 fail, you still want the other 95 to succeed).

2.  **Dedicated Error Workflows:**
    * **Purpose:** A separate n8n workflow designed *specifically* to catch and handle errors from your main workflows.
    * **How it works:** When a main workflow encounters an unhandled error (or an `Error` node is thrown), n8n can be configured to automatically trigger an "Error Workflow."
    * **Benefits:** Centralized error logging, sending notifications (Slack, email), retrying failed operations, moving failed data to a "dead letter queue" (e.g., a Google Sheet or database for manual review).

3.  **The `Error` Node:**
    * **Purpose:** To intentionally stop a workflow branch and trigger error handling when a *specific, expected condition* is not met (e.g., "required field is missing").
    * **How it works:** It acts as a controlled way to signal an error, providing a custom `Message` and `Code` that can be used by an Error Workflow to react intelligently.

4.  **Retry Mechanisms:**
    * **Purpose:** To automatically re-attempt an operation that failed due to transient issues (e.g., temporary network glitch, API rate limit hit).
    * **Strategies:** Implementing loops with `Wait` nodes or using custom `Code` to re-try an `HTTP Request` a few times with increasing delays (exponential backoff).

5.  **Logging and Monitoring:**
    * **Purpose:** To gain visibility into workflow execution and errors.
    * **Methods:** Using `Code` nodes to log to a central service, sending notifications for workflow failures, and regularly checking n8n's "Executions" list.

6.  **Idempotency:**
    * **Purpose:** Designing workflows such that running them multiple times with the same input doesn't cause unintended duplicate actions.
    * **Strategy:** Before creating a record, check if it already exists; before sending an email, verify it hasn't been sent. This prevents issues if a workflow is accidentally re-triggered or retried.

## Building Resilient Workflows: A Mindset

Error handling isn't an afterthought; it's an integral part of workflow design. Always consider:
* What could go wrong at each step?
* How should the workflow react to that specific error?
* Who needs to be notified?
* Is there a way to recover or retry?
* How can I prevent duplicate actions if the workflow runs again?