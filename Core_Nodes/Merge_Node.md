# Merge Node

**Source:** [n8n Docs - Merge node documentation](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.merge/)

## Overview
The Merge node is a powerful tool in n8n for combining data items within a workflow. It takes inputs from multiple branches or from different points in a single branch and merges them into a unified output. This is crucial for situations where data needs to be consolidated after conditional processing, or when you need to combine related information.

## Configurable Options:

* **Mode:** This is the most important setting and dictates how the items are combined.
    * **Append:** Simply adds all items from the secondary input (Input 2) to the end of the items from the primary input (Input 1). No matching is performed.
    * **Combine:** Matches items from Input 1 and Input 2 based on a specified "Key" (a property value). For each matching pair, it combines their JSON data into a single output item. Items without a match are discarded by default (unless "Keep non-matching" is enabled).
    * **Wait:** Combines items from Input 1 and Input 2 by their exact position in the workflow. The node waits until it receives a corresponding item from both inputs before outputting a combined item. This is similar to a "zip" operation.
    * **Pass Through:** This mode is for specific advanced use cases. It merges items from Input 1 and Input 2, but requires items to exist in both inputs to be passed through, based on position.
* **Key:** (Used in "Combine" mode) The name of the property that will be used to match items between Input 1 and Input 2 (e.g., `id`, `email`, `orderNumber`). Items with matching values for this property will be combined.
* **Combine Mode:** (Used in "Combine" mode) How the data from matching items is combined.
    * **Merge:** Merges the JSON data of matching items. If properties have the same name, the value from Input 2 overwrites Input 1.
    * **Merge (Keep Input 1):** Merges, but values from Input 1 are prioritized if names conflict.
    * **Merge (Keep Input 2):** Merges, but values from Input 2 are prioritized if names conflict.
    * **Combine Array:** Combines the two items into an array where the first element is from Input 1 and the second from Input 2.
* **Keep non-matching:** (Used in "Combine" mode) If checked, items from Input 1 or Input 2 that do not find a match based on the "Key" will still be passed through to the output, but without combined data from the other input.

## Input Structure:
The Merge node typically takes items from at least two different inputs (Input 1 and Input 2). The structure of these inputs will be standard n8n JSON item arrays.

```json
/* Input 1 (e.g., User Details) */
[
  { "json": { "userId": "A1", "name": "Alice" } },
  { "json": { "userId": "B2", "name": "Bob" } }
]

/* Input 2 (e.g., Order Data) */
[
  { "json": { "userId": "A1", "orderId": "101", "amount": 50 } },
  { "json": { "userId": "B2", "orderId": "102", "amount": 75 } }
]
Output Structure:
The output structure depends heavily on the chosen "Mode" and "Combine Mode".

Example Output (Mode: Combine, Key: userId, Combine Mode: Merge):

JSON

[
  {
    "json": {
      "userId": "A1",
      "name": "Alice",
      "orderId": "101",
      "amount": 50
    }
  },
  {
    "json": {
      "userId": "B2",
      "name": "Bob",
      "orderId": "102",
      "amount": 75
    }
  }
]
Potential Use Cases:
Consolidating Data: Combining customer details with their latest order information.
Rejoining Workflow Branches: After an If node splits data, a Merge node can bring the processed data back together.
Aggregating Information: Merging multiple API responses into a single item for a report.
Adding Context: Bringing in supplementary data (e.g., product details) to items that only contain IDs.
Tips & Best Practices:
Understand Your Data: Know exactly what properties you need to match on for "Combine" mode.
Visualize Paths: For complex merges, it helps to draw out the workflow paths leading into the Merge node.
Test Each Mode: The different merge modes behave very differently. Use test data to confirm the output is as expected before deploying.
"Keep non-matching" is powerful: If you need to ensure all original items are processed, even if no match is found, use this option.
Combine with Sort: Sometimes, sorting items before a Merge node (especially in "Wait" mode) can help ensure items are matched correctly based on their position.
<!-- end list -->