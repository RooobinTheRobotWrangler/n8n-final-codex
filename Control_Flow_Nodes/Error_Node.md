# Error Node

**Source:** [n8n Docs - Error node documentation](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.error/)

## Overview
The Error node is a **control flow node** that allows you to deliberately trigger an error in your workflow. While most nodes are designed to prevent errors, the Error node is used to *create* a specific error state. This is invaluable for implementing custom error handling logic, ensuring that your workflow responds predictably when specific conditions or unexpected data occur, and routing these "expected errors" to dedicated error handling workflows.

## Configurable Options:

* **Message:**
    * **Description:** The error message that will be thrown. This message will appear in the workflow execution logs. You can use [expressions](https://docs.n8n.io/code/expressions/) here to create dynamic error messages based on incoming data.
    * **Accepted values:** Any string.
* **Code:**
    * **Description:** An optional error code that will be associated with the error. This can be a custom code you define (e.g., `DATA_VALIDATION_FAILED`, `MISSING_REQUIRED_FIELD`) which can be used by downstream error handling workflows to categorize and react to specific errors.
    * **Accepted values:** Any string.
* **Throw Mode:**
    * **Description:** Determines how the error is thrown.
    * **Options:**
        * **All Items:** (Default) All incoming items will cause an error.
        * **Per Item:** Each incoming item will cause an individual error.

## Input Structure:
The Error node accepts any standard n8n input. It will process each incoming item and throw an error for it according to its configuration.

```json
/* Example Input: Items that should trigger an error */
[
  { "json": { "recordId": 101, "status": "invalid" } },
  { "json": { "recordId": 102, "status": "missing_data" } }
]
Output Structure:
The Error node typically has no successful output. When it executes, it intentionally stops the current workflow branch and triggers n8n's error handling mechanism (which can then be caught by a dedicated Error Workflow).

Potential Use Cases:
Custom Data Validation: If incoming data fails a complex validation check (e.g., done in a Code node or Function node), an Error node can be used to stop processing that item and route it to an error workflow.
Required Field Enforcement: If a critical field is missing from an item, use an If node to check for it, and then connect to an Error node if it's missing.
Explicit Failure States: Signalling a specific, expected failure condition (e.g., "Invoice Not Found") within the workflow to differentiate it from unexpected system errors.
Early Exit Strategy: Stopping a workflow branch as soon as a non-recoverable problem is detected, rather than letting it proceed with bad data.
Testing Error Workflows: Deliberately creating an error to test the functionality of your Error Workflows.
Tips & Best Practices:
Always Pair with an Error Workflow: The Error node is most useful when you have an Error Workflow set up to catch and handle the errors it throws (e.g., logging them, sending notifications, moving bad data to a "dead letter queue"). Without an Error Workflow, it will simply cause the workflow to fail.
Descriptive Messages and Codes: Use clear Message and Code values to make debugging easier and to allow your Error Workflow to react differently to various types of errors.
Conditional Errors: Usually, an Error node is preceded by an If node, a Code node, or a Function node that determines when the error should be thrown.
Know When to Use vs. Continue On Error: The Error node stops the branch (and potentially the workflow) by design. If you want the workflow to continue despite errors for some items, consider using the "Continue On Error" setting on individual nodes instead.