# Code Node (Data Manipulation Focus)

**Source:** [n8n Docs - Code node documentation](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.code/)

## Overview
While the Code node allows for general custom JavaScript, its capability for **complex data manipulation** is exceptionally powerful. It's often used when standard nodes (like Set or Function) are insufficient for intricate transformations, combining diverse data structures, or performing advanced structuring operations across multiple items.

## Key Data Manipulation Capabilities:

The `Code` node provides direct access to all incoming workflow items, allowing you to:

* **Custom Aggregation & Combination:**
    * **Purpose:** To group or combine data across multiple incoming items in ways not directly supported by the `Merge` or `Aggregate` nodes.
    * **Manipulation Use:** Summing values from specific fields across all items, collecting unique values from a dataset, or building a single, complex JSON object from multiple input items.
    * **Example:** Calculating a total sum from a list of orders, or creating a unique list of all customers from various sources.
* **Complex Filtering & Sorting (across all items):**
    * **Purpose:** Implementing advanced filtering logic or sorting algorithms that span the entire collection of incoming items.
    * **Manipulation Use:** Filtering items based on multiple, nested conditions; implementing custom deduplication logic; sorting items based on a computed value or multiple criteria.
* **Deep Data Nesting & Flattening:**
    * **Purpose:** Reshaping nested JSON structures or flattening complex arrays into simpler forms.
    * **Manipulation Use:** Transforming data from one API's complex format into another's simpler structure; creating nested objects from flat data, or vice-versa.
* **Iterative Transformation with State:**
    * **Purpose:** Performing transformations where the result for one item depends on the processing of a previous item within the same run.
    * **Manipulation Use:** Calculating running totals, assigning sequential IDs, or building dependent data structures.
* **Binary Data Manipulation:**
    * **Purpose:** Programmatically handling files and other binary data, such as modifying file names, combining attachments, or converting binary formats (requires external libraries for complex conversions).
    * **Manipulation Use:** Renaming files, creating new binary files from base64 strings, or extracting data from file metadata.

## Accessing Data for Manipulation:

Within the `Code` node, you access data using:
* `$json`: An array where each element is the `json` object of an incoming item.
* `$binary`: An array where each element is the `binary` data object of an incoming item.
* `items`: A combined array of objects, each containing both `json` and `binary` data for an item. This is often the most convenient for iterating through all items.

**Example Code Snippet for Combining Data:**
```javascript
// Imagine input items each have a 'revenue' field
let totalRevenue = 0;
for (const item of items) {
  if (item.json && typeof item.json.revenue === 'number') {
    totalRevenue += item.json.revenue;
  }
}

// Return a single item with the aggregated data
return [{
  json: {
    totalRevenue: totalRevenue,
    reportDate: new Date().toISOString()
  }
}];
Advanced Use Cases for Data Manipulation:
Custom Reports & Summaries: Aggregating data from multiple items to generate complex reports (e.g., total sales by region, unique customer counts).
Complex Deduplication: Implementing unique logic to identify and remove duplicate entries based on multiple criteria or fuzzy matching.
Data Pivot/Unpivot: Reshaping data from rows to columns or vice-versa to fit specific database or API requirements.
Conditional Data Merging: Combining data from multiple items only if specific conditions across those items are met.
Schema Transformation: Converting data from one JSON schema to another, especially when source and target schemas differ significantly.
Tips & Best Practices for Data Manipulation with Code Node:
Return Valid n8n Items: Unless using Raw response, your code should return an array of objects, where each object has at least a json key (and optionally a binary key) to ensure data flows correctly to subsequent nodes.
Test with Diverse Data: Ensure your code handles edge cases, missing fields, or different data types gracefully.
Debugging is Essential: Use $logger.log() and n8n's Execution List to inspect intermediate values and verify transformations.
Performance Considerations: For extremely large datasets (thousands/millions of items), very complex Code nodes can be resource-intensive. Optimize your JavaScript or consider alternative strategies if performance becomes an issue.
Comments: Add comments to your code to explain complex logic, making it easier for yourself and others to understand and maintain.