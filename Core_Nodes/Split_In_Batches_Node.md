# Split In Batches Node

**Source:** [n8n Docs - Split In Batches node documentation](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.splitinbatches/)

## Overview
The Split In Batches node takes a collection of incoming items and divides them into smaller, predefined groups (batches). It then processes these batches sequentially, sending each batch down the workflow path before moving to the next. This is essential for handling large datasets, respecting API rate limits, or managing memory usage in your workflows.

## Configurable Options:

* **Batch Size:**
    * **Description:** The maximum number of items that should be included in each batch. For example, if you have 100 items and set a batch size of 10, the node will create 10 batches.
    * **Accepted values:** A positive integer.
* **Delay Between Batches:**
    * **Description:** An optional delay (in milliseconds) that the workflow will pause after processing one batch before starting the next. This is extremely useful for respecting API rate limits.
    * **Accepted values:** A positive integer representing milliseconds (e.g., `1000` for 1 second).
* **Process All Batches:**
    * **Description:** If enabled, the node will process all batches, even if an error occurs in a previous batch. If disabled, the workflow will stop if an error occurs within any batch.
    * **Accepted values:** Boolean (checkbox).

## Input Structure:
The Split In Batches node expects an array of JSON objects, where each object is an item that needs to be batched.

```json
/* Example Input: 5 items */
[
  { "json": { "id": 1, "name": "Item A" } },
  { "json": { "id": 2, "name": "Item B" } },
  { "json": { "id": 3, "name": "Item C" } },
  { "json": { "id": 4, "name": "Item D" } },
  { "json": { "id": 5, "name": "Item E" } }
]
Output Structure:
The Split In Batches node outputs each batch of items one by one. The output for each batch is still an array of JSON objects, conforming to the n8n item structure, but containing only the items of that specific batch. The node will re-run the subsequent nodes in the workflow for every batch.

Example Output (if Batch Size is 2):

First Output (Batch 1):

JSON

[
  { "json": { "id": 1, "name": "Item A" } },
  { "json": { "id": 2, "name": "Item B" } }
]
Then, after processing, Second Output (Batch 2):

JSON

[
  { "json": { "id": 3, "name": "Item C" } },
  { "json": { "id": 4, "name": "Item D" } }
]
Finally, Third Output (Batch 3):

JSON

[
  { "json": { "id": 5, "name": "Item E" } }
]
Potential Use Cases:
API Rate Limit Management: Sending data to APIs that limit the number of requests per minute by pausing between batches.
Large Data Processing: Preventing memory issues when dealing with thousands of items by processing them in smaller chunks.
Segmented Notifications: Sending batches of emails or messages to different groups of recipients at intervals.
Controlled Data Migration: Transferring data in phases to ensure stability or allow for review between batches.
Tips & Best Practices:
Test Batch Size: Experiment with different Batch Size values to find the optimal balance between performance and resource consumption for your workflow and external services.
Use Delay for Rate Limits: If you're hitting API rate limits, set a Delay Between Batches that's appropriate for the API's limits (e.g., if an API allows 100 requests per minute, a 600ms delay might be a good starting point for a batch size of 1).
Consider "Process All Batches": Enable this if you want the workflow to continue even if one batch fails, allowing you to log or retry failed batches separately.
Looping vs. Batches: Understand that the Split In Batches node processes items sequentially in groups, effectively causing the entire subsequent part of the workflow to run for each batch. This is different from the Function/Code node's item-by-item processing.
<!-- end list -->