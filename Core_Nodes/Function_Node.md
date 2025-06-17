# Function Node

**Source:** [n8n Docs - Function node documentation](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.function/)

## Overview
The Function node allows you to execute custom JavaScript code to transform incoming data items **one by one**. Unlike the Code node (which processes all items as a single array), the Function node is ideal for simple, repetitive transformations on each individual item, making the code often cleaner and easier to manage for such tasks.

## Configurable Options:

* **Code:**
    * **Description:** This is the main area where you write your JavaScript code.
    * **Context:** Within the code, you'll work with:
        * `item`: An object representing the **current individual input item** being processed (e.g., `item.json.name`).
        * `index`: The numerical position of the current item in the overall list of incoming items (starting from 0).
        * `items`: (Available, but less common to use directly in Function node) The array of *all* incoming items, similar to the Code node.
        * `this`: Refers to the current node context, primarily for accessing parameters of the node itself (though less common in basic usage).
    * **Output:** Your code should return an `item` object (or an array of `item` objects if you want to split one item into multiple).

## Input Structure:
The Function node processes items from previous nodes. The input will be an array of objects, each representing an item. The Function node iterates over this array, running your custom code for each `item` individually.

```json
/* Example Input: Multiple Items */
[
  {
    "json": {
      "productName": "Laptop",
      "price": 1200
    },
    "binary": {}
  },
  {
    "json": {
      "productName": "Mouse",
      "price": 25
    },
    "binary": {}
  }
]
Output Structure:
The Function node expects your JavaScript code to return an item object (or an array of item objects). It will collect these returned items from each execution and output them as an array of n8n items to the next node.

Example Code Output (if Code transforms each item):

JavaScript

// Example JavaScript inside the Function node
item.json.priceWithTax = item.json.price * 1.05; // Add a new property
return item; // Return the modified item
Corresponding JSON Output (as seen by next node):

JSON

[
  {
    "json": {
      "productName": "Laptop",
      "price": 1200,
      "priceWithTax": 1260
    },
    "binary": {}
  },
  {
    "json": {
      "productName": "Mouse",
      "price": 25,
      "priceWithTax": 26.25
    },
    "binary": {}
  }
]
Potential Use Cases:
Simple Item-by-Item Transformations: Renaming fields, converting data types, or calculating new properties for each item.
Applying Business Logic: Implementing a simple rule for each individual item (e.g., categorizing products based on their price).
Conditional Field Population: Adding a field to an item only if a certain condition is met for that item.
Generating IDs/Timestamps per item: Creating a unique ID or timestamp for each individual item as it passes through.
Tips & Best Practices:
Use for Item-by-Item: The Function node is best suited when your logic applies independently to each item. If your logic needs to consider all items together (e.g., sum of all prices), the Code node is usually more appropriate.
Return the item Object: Always ensure your Function node explicitly returns the item object (or [item]) after modification, otherwise, the item might not pass to the next node.
Keep it Concise: For very complex logic, consider if the Code node or a series of simpler nodes might be clearer.
Debugging: Similar to the Code node, you can use $logger.log() within the Function node to print messages to the workflow execution log for debugging.
Error Handling: Implement try...catch blocks within your JavaScript to handle potential errors in your custom logic.
<!-- end list -->

