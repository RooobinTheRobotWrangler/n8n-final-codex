# Function Node (Data Manipulation Focus)

**Source:** [n8n Docs - Function node documentation](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.function/)

## Overview
The Function node is an excellent choice for **item-by-item data manipulation** using custom JavaScript. It’s ideal when you need to transform or restructure each incoming workflow item individually, providing a clear and often more efficient way to handle repetitive data changes compared to processing all items at once.

## Key Data Manipulation Capabilities:

The `Function` node provides direct access to the `item` currently being processed, allowing you to:

* **Property Renaming and Restructuring (per item):**
    * **Purpose:** To change property names, combine properties, or create new nested structures for each individual data item.
    * **Manipulation Use:** Standardizing inconsistent field names (e.g., `first_name` to `firstName`), combining `street`, `city`, `zip` into a single `address` object, or extracting specific fields into a flatter structure for each record.
    * **Example:** Transform `{ "firstName": "John", "lastName": "Doe" }` into `{ "fullName": "John Doe" }`.
* **Conditional Value Assignment (per item):**
    * **Purpose:** To assign values to properties based on conditions specific to that single item.
    * **Manipulation Use:** Setting a `status` field to "High Priority" if `value` is above a certain threshold, or assigning a default category if one is missing.
* **Simple Calculations (per item):**
    * **Purpose:** Performing mathematical or logical operations on numerical or boolean fields for each item.
    * **Manipulation Use:** Calculating `totalPrice` from `quantity * unitPrice`, applying tax rates, or converting units.
* **String Manipulation (per item):**
    * **Purpose:** Modifying text strings within each item.
    * **Manipulation Use:** Converting text to uppercase/lowercase, trimming whitespace, extracting substrings, or formatting dates into specific string patterns.
* **Filtering Items (selectively return items):**
    * **Purpose:** To prevent certain items from passing to the next node based on custom logic.
    * **Manipulation Use:** If `return null;` is used, the item is dropped. This allows for custom filtering more complex than the `If` node might allow directly on a single field.

## Accessing Data for Manipulation:

Within the `Function` node's code, the primary way to access and modify data for the **current item** is through the `item` variable:

* `item.json`: The JSON data object of the current incoming item.
* `item.binary`: The binary data object of the current incoming item.
* `index`: The numerical position of the current item in the overall list of incoming items.

**Example Code Snippet for Item-by-Item Transformation:**
```javascript
// Example JavaScript inside the Function node
// Assume 'item.json.price' and 'item.json.quantity' exist
item.json.lineTotal = item.json.price * item.json.quantity;
item.json.productName = item.json.productName.toUpperCase(); // Convert product name to uppercase

// You must return the item (or an array of items)
return item;
Corresponding JSON Output (as seen by next node):

JSON

[
  {
    "json": {
      "productName": "LAPTOP",
      "price": 1200,
      "quantity": 1,
      "lineTotal": 1200
    },
    "binary": {}
  }
  // ... other transformed items
]
Advanced Use Cases for Data Manipulation with Function Node:
Email Body Parsing: Extracting specific information from the plain text or HTML body of an email (e.g., order numbers, names) when the structure is relatively consistent.
Custom Data Aggregation (simple): If you need to perform a simple aggregation for each group of items (e.g., calculating a subtotal for items belonging to the same order ID before grouping them with a Merge node).
Applying Complex Business Rules: Implementing custom logic for pricing, discounts, or categorizations that depend on multiple fields within a single record.
Tips & Best Practices for Data Manipulation with Function Node:
Focus on item: Design your code to operate on the item variable, which represents the current single workflow item.
Return item: Always ensure your code explicitly returns the item object (or [item]) after modification. Failing to do so will result in the item not passing to the next node.
Clear and Concise Code: Keep the JavaScript within the Function node as simple and readable as possible. If the logic becomes too complex, consider if the Code node is more appropriate.
Error Handling within Code: Use try...catch blocks to handle potential errors in your custom JavaScript, preventing the entire workflow from failing.
Leverage External Libraries (Carefully): While n8n provides a limited set of pre-installed Node.js modules for use in Function/Code nodes, generally stick to basic JavaScript for simplicity and stability unless absolutely necessary.
<!-- end list -->