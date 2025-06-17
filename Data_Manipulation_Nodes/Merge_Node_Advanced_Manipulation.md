# Merge Node (Advanced Data Manipulation)

**Source:** [n8n Docs - Merge node documentation](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.merge/)

## Overview
Beyond simply rejoining workflow branches, the Merge node is a powerful tool for **advanced data manipulation, combining, and restructuring**. It allows you to intelligently consolidate items, enrich data, and create complex data structures by matching and combining data from different input streams based on various strategies.

## Key Operations for Advanced Data Manipulation:

The `Merge` node's versatility for data manipulation comes from its `Mode` and `Combine Mode` settings:

* **`Combine` Mode (Primary for Data Manipulation):**
    * **Purpose:** To join data from two input streams (Input 1 and Input 2) based on a common `Key` (a property value).
    * **Advanced Use Cases:**
        * **Data Enrichment:** Taking a primary list of items (e.g., customer IDs) from Input 1 and enriching each item with detailed data (e.g., customer names, addresses) fetched from a separate source (Input 2), where both share a common identifier (`userId`).
        * **Relationship Building:** Combining related records from different systems (e.g., orders with their corresponding products, or tasks with their assigned users).
        * **Conditional Combination:** Use an `If` node to filter data into two streams, then use `Merge` with `Combine` to intelligently rejoin them based on some ID, ensuring that only the relevant, processed data is combined.
    * **`Combine Mode` Options (within `Combine` mode):**
        * **`Merge`:** Merges JSON objects, with values from Input 2 overwriting values from Input 1 if property names conflict.
        * **`Merge (Keep Input 1)`:** Merges JSON objects, but values from Input 1 are kept if property names conflict.
        * **`Merge (Keep Input 2)`:** Merges JSON objects, but values from Input 2 are kept if property names conflict.
        * **`Combine Array`:** Creates a new array for each matched pair, containing the Input 1 item as the first element and the Input 2 item as the second. This is useful for retaining both original structures when merging.
    * **`Keep non-matching`:** This crucial option determines whether items that don't find a match in the other input are discarded or passed through. Useful for ensuring all initial data is carried forward, even if not enriched.

* **`Wait` Mode:**
    * **Purpose:** To combine items strictly by their position/order across two input streams, waiting for a corresponding item from both before proceeding.
    * **Advanced Use Cases:**
        * **Parallel Processing Rejoin:** If you have two parallel branches that process data, and each branch outputs items in the same order corresponding to the initial input, `Wait` mode can precisely zip them back together.
        * **Ordered Data Integration:** Combining sequentially dependent data streams (e.g., a list of successful operations and a list of their corresponding log entries, where order matters).

* **`Append` Mode:**
    * **Purpose:** Simply stacks all items from Input 2 after all items from Input 1. No matching is performed.
    * **Advanced Use Cases:**
        * **Dataset Concatenation:** Combining two complete datasets into a single, larger dataset (e.g., merging sales data from Q1 and Q2).
        * **Batch Accumulation:** Collecting results from multiple workflow runs into a single final output.

## Input Structure for Manipulation:
The Merge node typically takes two distinct sets of items as input (Input 1 and Input 2), usually from different upstream nodes or branches. Each input will be a standard n8n array of JSON objects.

```json
/* Input 1 Example (Basic User Data) */
[
  { "json": { "id": "U1", "name": "Alice" } },
  { "json": { "id": "U2", "name": "Bob" } }
]

/* Input 2 Example (Detailed User Info) */
[
  { "json": { "id": "U1", "email": "alice@email.com", "status": "active" } },
  { "json": { "id": "U3", "email": "charlie@email.com", "status": "pending" } } // No match for U3 in Input 1
]
Output Structure for Manipulation:
The output structure is highly dependent on the chosen Mode and Combine Mode. It will be an array of n8n items, with their JSON data restructured or combined according to the merge logic.

Example Output (Mode: Combine, Key: id, Combine Mode: Merge, Keep non-matching: TRUE):

JSON

[
  {
    "json": {
      "id": "U1",
      "name": "Alice",
      "email": "alice@email.com",
      "status": "active"
    }
  },
  {
    "json": {
      "id": "U2",
      "name": "Bob" // No matching data from Input 2 for U2
    }
  },
  {
    "json": {
      "id": "U3",
      "email": "charlie@email.com",
      "status": "pending" // No matching data from Input 1 for U3
    }
  }
]
Advanced Use Cases for Data Manipulation:
Database Join Simulation: Mimicking database join operations (INNER JOIN, LEFT JOIN, FULL OUTER JOIN) using Combine mode with and without "Keep non-matching".
Enriching Data from Multiple Sources: Combining customer records from a CRM (Input 1) with their latest transaction data from a payment gateway (Input 2).
A/B Testing Outcome Analysis: Merging original test group assignments (Input 1) with conversion results (Input 2) to analyze performance.
Workflow State Merging: Bringing back data generated in a sub-workflow or a complex branch to merge it with the original item's context.
Tips & Best Practices for Data Manipulation with Merge Node:
Understand 'Key' Importance: For Combine mode, the Key field is critical. Ensure your input items have a consistent property to match on.
Preview Output: Always use n8n's "Execute Workflow" feature to examine the output of the Merge node carefully. The results can be counter-intuitive if the mode isn't exactly what you intended.
Order of Inputs Matters (especially for 'Append' and 'Wait'): Input 1 and Input 2 are distinct. Data from Input 1 always comes first in Append mode, and the position is crucial in Wait mode.
Debugging Merges: If items aren't merging as expected, inspect the input data for both streams to ensure keys match, data types are consistent, and items are in the correct order for positional merges.
Combine with Set or Code: Often, you'll use a Set or Code node before the Merge node to ensure that the Key fields are clean, consistent, and ready for matching.