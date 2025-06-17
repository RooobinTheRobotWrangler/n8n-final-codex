# Code Node

**Source:** [n8n Docs - Code node documentation](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.code/)

## Overview
The Code node allows you to execute custom JavaScript code directly within your n8n workflow. This provides immense flexibility for tasks that require highly specific data manipulation, complex calculations, or custom logic that cannot be achieved with standard nodes. You can access and modify incoming data, and return new data to subsequent nodes.

## Configurable Options:

* **Code:**
    * **Description:** This is the main area where you write your JavaScript code.
    * **Context:**
        * `$json`: An array containing the `json` data of all incoming items.
        * `$binary`: An array containing the `binary` data of all incoming items.
        * `items`: A convenience array combining both `json` and `binary` data for all incoming items, often used for iteration.
        * `$logger`: An object for logging messages to the n8n console during execution.
        * `$parameters`: An object containing values of node parameters configured in the workflow.
        * *(Note: The environment provided to the Code node is similar to Node.js, allowing use of certain built-in modules.)*
* **Response Format:**
    * **Description:** How the output of the Code node should be formatted.
    * **Options:** `JSON` (default, standard n8n item structure), `Raw` (returns the exact output of your code without n8n's item wrapper).
* **Output Binary Data:**
    * **Description:** If your code processes or generates binary data, this option helps n8n correctly handle it.
    * **Options:** `Append` (adds new binary data to existing items), `Replace` (replaces existing binary data).

## Input Structure:
The Code node processes items from previous nodes. The input will be an array of objects, each representing an item. Within your JavaScript code, you access this data via the `$json` and `$binary` global variables (or the `items` array).

```json
/* Example Input */
[
  {
    "json": {
      "firstName": "Alice",
      "lastName": "Wonder",
      "email": "alice@example.com"
    },
    "binary": {}
  },
  {
    "json": {
      "firstName": "Bob",
      "lastName": "Builder",
      "email": "bob@example.com"
    },
    "binary": {}
  }
]
Output Structure:
The Code node returns an array of items, similar to other n8n nodes. Your JavaScript code should return an array of objects, where each object typically has a json key (and optionally a binary key). If you set "Response Format" to Raw, the output will be exactly what your code returns.

Example Code Output (if Code transforms data):

JavaScript

// Example JavaScript inside the Code node
const outputItems = [];
for (const item of items) {
  const fullName = `${item.json.firstName} ${item.json.lastName}`;
  outputItems.push({
    json: {
      fullName: fullName,
      email: item.json.email
    }
  });
}
return outputItems;
Corresponding JSON Output (as seen by next node):

JSON

[
  {
    "json": {
      "fullName": "Alice Wonder",
      "email": "alice@example.com"
    }
  },
  {
    "json": {
      "fullName": "Bob Builder",
      "email": "bob@example.com"
    }
  }
]
Potential Use Cases:
Complex Data Transformation: Performing calculations, conditional logic, or reformatting data that's too intricate for standard nodes like Set or Function.
Custom Filtering/Sorting: Implementing specific filtering or sorting algorithms not available in built-in nodes.
API Response Parsing: Extracting very specific data from complex or inconsistent API responses.
Generating Dynamic Content: Creating unique IDs, timestamps, or complex strings based on workflow data.
Interacting with External Libraries: (Advanced) Using certain pre-installed Node.js modules for specific tasks (though this is less common for simple transformations).
Tips & Best Practices:
Start Simple: Begin with small, understandable code blocks and gradually increase complexity.
Test Frequently: Use n8n's "Execute Workflow" feature often to see the output of your Code node and debug.
Use $logger for Debugging: Print messages to the workflow execution log using $logger.log('My debug message') or $logger.warn('Warning!').
Return Correct Format: Always ensure your Code node returns data in the expected n8n item format (an array of objects with a json key) unless you specifically choose Raw response.
Handle Errors: Implement try...catch blocks in your JavaScript to gracefully handle potential errors within your code.
Prefer Built-in Nodes: If a built-in node (like Set, Function, Split In Batches, If) can do the job, it's generally more efficient, easier to understand, and less error-prone than writing custom code. Use the Code node when other options are exhausted.
<!-- end list -->