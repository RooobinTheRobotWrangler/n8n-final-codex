# Item Lists Node

**Source:** [n8n Docs - Item Lists node documentation](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.itemlists/)

## Overview
The Item Lists node is designed for manipulating arrays (lists) of data within your n8n items. It allows you to perform various operations on arrays, such as adding, removing, getting specific elements, or setting entire lists, offering fine-grained control over list data within your workflow. This node is especially useful when dealing with data that arrives or needs to be structured as a list of sub-items.

## Configurable Options:

* **Operation:** This defines the specific action the node will perform on an item list. Common operations include:
    * **Add:** Adds new items to an existing list.
    * **Remove:** Removes items from a list based on an index or a condition.
    * **Get:** Retrieves a specific item or a range of items from a list by index.
    * **Set:** Replaces an entire list with new items, or sets a specific item in a list.
    * **Length:** Outputs the number of items in a list.
    * **Reverse:** Reverses the order of items in a list.
    * **Sort:** Sorts items in a list based on a property.
* **Source Field:**
    * **Description:** The JSON property in the incoming item that contains the array you want to manipulate (e.g., `products`, `lineItems`). This field supports [expressions](https://docs.n8n.io/code/expressions/).
* **Target Field:**
    * **Description:** The JSON property where the modified or resulting list should be placed in the output item. If left empty, it might overwrite the source field or add a new one depending on the operation.
* **Index:**
    * **Description:** For operations like `Get`, `Remove`, `Set`, this specifies the numerical position (starting from 0) of the item within the list to be affected. Supports expressions.
* **Value:**
    * **Description:** For `Add` or `Set` operations, this is the item(s) you want to add to or set in the list. This typically comes from expressions using data from previous nodes.
* **Condition:**
    * **Description:** For `Remove` operations, you can specify a condition (similar to the If node) to remove items from the list that meet that condition.
* **Sort By:**
    * **Description:** For `Sort` operation, the name of the property within each item in the list to sort by (e.g., `price`, `date`).
* **Sort Order:**
    * **Description:** For `Sort` operation, whether to sort in `Ascending` or `Descending` order.
* **Output Mode:**
    * **Description:** Determines how the output is structured.
    * **Options:** `Item List` (output the modified list as a property within the original item structure), `Items` (output each item from the list as a separate n8n item).

## Input Structure:
The Item Lists node expects an array of JSON objects, where each object represents an item that contains a list (array) you want to modify.

```json
/* Example Input: Item with a list of 'products' */
[
  {
    "json": {
      "orderId": "ORD-001",
      "products": [
        { "id": "P1", "name": "Laptop", "price": 1200 },
        { "id": "P2", "name": "Mouse", "price": 25 }
      ]
    }
  },
  {
    "json": {
      "orderId": "ORD-002",
      "products": [
        { "id": "P3", "name": "Keyboard", "price": 75 }
      ]
    }
  }
]
Output Structure:
The output depends on the Operation and Output Mode. It generally maintains the incoming n8n item structure, but with the Source Field (or Target Field) modified according to the operation. If Output Mode is Items, each item from the list will become a separate output item.

Example Output (Operation: Add, adding new product):
(Assuming a new product { "id": "P4", "name": "Monitor", "price": 300 } is added to the products list)

JSON

[
  {
    "json": {
      "orderId": "ORD-001",
      "products": [
        { "id": "P1", "name": "Laptop", "price": 1200 },
        { "id": "P2", "name": "Mouse", "price": 25 },
        { "id": "P4", "name": "Monitor", "price": 300 }
      ]
    }
  },
  {
    "json": {
      "orderId": "ORD-002",
      "products": [
        { "id": "P3", "name": "Keyboard", "price": 75 },
        { "id": "P4", "name": "Monitor", "price": 300 }
      ]
    }
  }
]
Potential Use Cases:
Shopping Carts: Adding or removing items from an order's lineItems list.
Dynamic Reports: Aggregating data into a list, then sorting or filtering it.
Processing Sub-items: Breaking down a single item with a list into multiple individual items for parallel processing (using Output Mode: Items).
Data Normalization: Ensuring all lists have a consistent number of elements or a specific order.
Managing Related Records: Handling lists of contacts, tasks, or associated documents within a main record.
Tips & Best Practices:
Expressions for Dynamic Control: Use expressions for Source Field, Index, Value, and Condition to make the node highly dynamic.
Output Mode: Items is Powerful: When you want to iterate over each element of a list and process it as a separate workflow item, this setting is crucial. It effectively "flattens" the list.
Understand Item vs. List: Be clear whether you're working with the overall n8n item or a specific list within that item. The Item Lists node works on the latter.
Test Each Operation: The behavior of Add, Remove, Get, and Set can vary subtly. Test with sample data.
Error Handling: Consider how to handle cases where the Source Field is missing or not an array.
<!-- end list -->