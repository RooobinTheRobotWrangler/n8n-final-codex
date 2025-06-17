# Split In Batches Node (Advanced Data Structuring & Control)

**Source:** [n8n Docs - Split In Batches node documentation](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.splitinbatches/)

## Overview
Beyond simply dividing items, the Split In Batches node is a critical tool for **advanced data structuring and workflow control**, especially when dealing with large volumes of data or interacting with rate-limited external services. It enables you to process data in controlled segments, manage performance, and build more resilient workflows.

## Key Capabilities for Data Structuring & Control:

The `Split In Batches` node's main purpose is to manage the flow of data by breaking it into smaller, consumable chunks:

* **Controlled Processing for Large Datasets:**
    * **Purpose:** To prevent memory exhaustion or performance bottlenecks when processing thousands or millions of items.
    * **Structuring Use:** Instead of sending all items at once, which could crash n8n or an external API, the node structures the data into smaller groups. Each group is then processed entirely before the next group begins.
    * **Example:** A workflow that processes 10,000 customer records from a database can be split into batches of 100, ensuring memory usage stays manageable and the database isn't overwhelmed.
* **API Rate Limit Adherence:**
    * **Purpose:** To conform to the limits imposed by external APIs (e.g., "100 requests per minute").
    * **Structuring Use:** By setting a `Batch Size` and `Delay Between Batches`, the node effectively structures the timing of your data delivery. This ensures your workflow doesn't get blocked or banned by APIs due to too many requests in a short period.
    * **Example:** If an API allows 60 requests per minute, splitting into batches of 10 with a 1-second delay between batches ensures you send roughly 10 requests every second (60/minute).
* **Resilient Workflow Execution:**
    * **Purpose:** To allow workflows to continue processing, even if individual batches encounter errors.
    * **Structuring Use:** The `Process All Batches` option (if enabled) changes the workflow's control flow. If an error occurs in one batch, the node can still proceed to process subsequent batches, preventing a single failure from stopping the entire operation. This allows for partial success and more robust error handling strategies.

## Input Structure for Structuring & Control:
The Split In Batches node expects a single input stream containing an array of all items to be processed.

```json
/* Example Input: 20 items, to be batched */
[
  { "json": { "recordId": 1, "value": "A" } },
  { "json": { "recordId": 2, "value": "B" } },
  // ... up to { "json": { "recordId": 20, "value": "T" } }
]
Output Structure for Structuring & Control:
The key aspect of the output is that the subsequent nodes in the workflow will execute repeatedly, once for each batch produced by the Split In Batches node. Each output will be an array of items representing a single batch.

Example Output (if Batch Size is 5):

First Output (Batch 1):

JSON

[
  { "json": { "recordId": 1, "value": "A" } },
  { "json": { "recordId": 2, "value": "B" } },
  { "json": { "recordId": 3, "value": "C" } },
  { "json": { "recordId": 4, "value": "D" } },
  { "json": { "recordId": 5, "value": "E" } }
]
(Then, the workflow processes this batch. After it's done, the node outputs Batch 2, and the workflow runs again for that batch, and so on.)

Advanced Use Cases for Data Structuring & Control:
Segmented Data Processing: Processing sensitive data in smaller, auditable chunks, or sending out notifications in waves.
Concurrent vs. Sequential Batching: Understanding that Split In Batches enforces sequential processing of batches, which is different from potentially concurrent processing that a Split In Batches followed by a Function node might allow (though Function is item-by-item).
Orchestrating Large Migrations: Breaking down a huge data migration into smaller, manageable steps, with checkpoints or logging occurring after each batch completes.
Handling Variable Item Counts: Adapting processing to input sizes without hardcoding loops, as the node dynamically creates batches based on the input item count and Batch Size.
Tips & Best Practices for Data Structuring & Control with Split In Batches Node:
Optimal Batch Size: Experimentation is key to finding the ideal Batch Size. Too small can be inefficient; too large can cause errors.
Delay for External Services: Always consider the rate limits of external APIs when setting Delay Between Batches. A common formula is (1 / API_CALLS_PER_SECOND) * 1000 * BATCH_SIZE for a starting delay.
Error Handling (with Process All Batches): When Process All Batches is enabled, pair it with downstream error handling (e.g., an Error node or a Code node to log specific batch failures) to keep track of incomplete operations.
Logging Batches: For long-running workflows, add logging steps (e.g., using a Set node to add a batchNumber or a Code node to log progress) after the Split In Batches node to track progress.
Combine with Aggregation: After processing batches, you might use an Aggregate node (or a Merge node in Append mode) to collect the results from all batches back into a single output for final processing or reporting.