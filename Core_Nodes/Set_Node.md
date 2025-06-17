# Set Node

**Source:** [n8n Docs - Set node documentation](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.set/)

## Overview
The Set node is a crucial data transformation node in n8n. Its primary purpose is to manipulate data within an item's JSON structure. You can use it to add new properties, update existing ones, or remove properties from the incoming data. It's often used to prepare data for subsequent nodes or to standardize data formats.

## Configurable Options:

The Set node offers different "Modes" to control how it manipulates data:

* **Mode:** Defines how new properties are added or how existing ones are handled.
    * **Merge:** (Default) Adds new properties. If a property with the same name already exists, the new value overwrites the old one.
    * **Replace:** Removes all existing properties from the input item(s) and replaces them entirely with the properties you define in the Set node.
    * **Add:** Adds new properties. If a property with the same name already exists, the new value is *not* added, and the original value is kept.
* **Keep Only Set:** (Boolean checkbox) If checked, only the properties explicitly defined in the Set node will be kept in the output. All other properties from the input item(s) will be removed. This is useful for cleaning up data.
* **Options (for each property you define):**
    * **Property Name:** The name of the new or existing property you want to manipulate (e.g., `userStatus`, `totalAmount`).
    * **Value:** The value you want to assign to the property. This can be a static string, a number, a boolean, or most commonly, an [expression](https://docs.n8n.io/code/expressions/) that pulls data from previous nodes (`{{ $json.name }}`, `{{ $item(0).$json.address }}`).
    * **Value Type:** Allows you to explicitly set the data type of the output value (e.g., `String`, `Number`, `Boolean`, `JSON`, `Date`, `Object`). This is important for ensuring data consistency.

## Input Structure:
The Set node typically takes an array of JSON objects, where each object represents an item from a previous node in the workflow. It processes each item individually.

```json
[
  {
    "json": {
      "firstName": "John",
      "lastName": "Doe",
      "age": 30
    }
  },
  {
    "json": {
      "firstName": "Jane",
      "lastName": "Smith",
      "age": 25
    }
  }
]
Output Structure:
The output structure depends heavily on the "Mode" and "Keep Only Set" options. Generally, the Set node outputs an array of JSON objects, identical in number to the input items, but with the modified JSON properties.

Example Output (Mode: Merge, adding 'fullName'):

JSON

[
  {
    "json": {
      "firstName": "John",
      "lastName": "Doe",
      "age": 30,
      "fullName": "John Doe"
    }
  },
  {
    "json": {
      "firstName": "Jane",
      "lastName": "Smith",
      "age": 25,
      "fullName": "Jane Smith"
    }
  }
]
Example Output (Mode: Replace, adding 'status' only):
(Assuming 'Keep Only Set' is checked)

JSON

[
  {
    "json": {
      "status": "active"
    }
  },
  {
    "json": {
      "status": "active"
    }
  }
]
Potential Use Cases:
Renaming Properties: Changing firstName to first_name for API compatibility.
Adding New Properties: Creating a fullName property by combining firstName and lastName.
Filtering Properties: Using "Keep Only Set" to ensure only necessary data passes to the next node.
Setting Default Values: Assigning a default status if it's missing from input.
Type Conversion: Ensuring a number is treated as a number, not a string.
Standardizing Data: Transforming varied input formats into a consistent structure for downstream processing.
Tips & Best Practices:
Use Expressions: The Set node becomes truly powerful when combined with expressions to dynamically calculate values.
Understand 'Mode' and 'Keep Only Set': These two options fundamentally change the output structure. Test them to ensure you get the desired result.
Chaining Set Nodes: For complex transformations, it can sometimes be clearer to use multiple Set nodes in sequence, each handling a specific transformation.
Debugging: Use the NoOp node or inspect outputs in test runs to see exactly what the Set node is doing to your data.
<!-- end list -->