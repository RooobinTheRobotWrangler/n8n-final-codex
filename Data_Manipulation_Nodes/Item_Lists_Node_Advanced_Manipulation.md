# Item Lists Node (Advanced Data Manipulation)

**Source:** [n8n Docs - Item Lists node documentation](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.itemlists/)

## Overview
Beyond basic list operations, the Item Lists node is a powerful tool for **advanced data manipulation** within n8n workflows. It excels at reshaping, filtering, and organizing data that is structured as arrays (lists) within your workflow items. Understanding its various `Operation` modes, especially `Output Mode`, allows for intricate data restructuring.

## Key Operations for Data Manipulation:

The `Item Lists` node's versatility comes from its `Operation` modes:

* **`Add` Operation:**
    * **Purpose:** Appending new elements to an existing array.
    * **Manipulation Use:** Dynamically adding new products to an order's `lineItems` array, or new attendees to an event's `attendees` list based on incoming data.
    * **Example:** If `products` list is `[A, B]`, adding `C` results in `[A, B, C]`.
* **`Remove` Operation:**
    * **Purpose:** Deleting specific elements from an array.
    * **Manipulation Use:** Filtering out certain items from a list based on an `Index` or a `Condition` (e.g., removing "out of stock" items from a product list, or expired entries from a log).
    * **Example:** Removing item at `Index 0` from `[A, B, C]` results in `[B, C]`.
* **`Get` Operation:**
    * **Purpose:** Extracting specific elements from an array without modifying the original.
    * **Manipulation Use:** Retrieving the first (or last, or specific indexed) element of a list for individual processing, or getting a range of items. This can be used to "peek" at data or process specific sub-sections.
    * **Example:** Getting item at `Index 1` from `[A, B, C]` returns `B`.
* **`Set` Operation:**
    * **Purpose:** Replacing an entire array or updating a specific element within an array.
    * **Manipulation Use:** Overwriting an old list with a newly generated one, or updating a specific item's details within a list without affecting others.
    * **Example:** Setting `products[0]` to `New Item` transforms `[A, B]` to `[New Item, B]`.
* **`Length` Operation:**
    * **Purpose:** Counting the number of elements in an array.
    * **Manipulation Use:** Checking if a list is empty, validating minimum/maximum list sizes, or dynamically adjusting workflow logic based on the count of items.
    * **Example:** `Length` of `[A, B, C]` outputs `3`.
* **`Reverse` Operation:**
    * **Purpose:** Reversing the order of elements in an array.
    * **Manipulation Use:** Processing items in a different order (e.g., newest to oldest, or oldest to newest), or preparing data for systems that expect reverse order.
    * **Example:** `Reverse` of `[A, B, C]` results in `[C, B, A]`.
* **`Sort` Operation:**
    * **Purpose:** Ordering elements within an array based on a specific property.
    * **Manipulation Use:** Organizing lists of products by price, tasks by due date, or users by name, making subsequent processing or reporting easier.
    * **Example:** Sorting `[{v:2},{v:1}]` by `v` ascending results in `[{v:1},{v:2}]`.

## Crucial for Advanced Manipulation: `Output Mode`

The `Output Mode` setting (available for most operations) profoundly impacts data structuring:

* **`Item List` (Default):**
    * **Description:** The modified list (array) remains nested within the original n8n item's `json` property.
    * **Use Case:** When you want to keep the list associated with its parent item (e.g., a customer item still contains its updated list of orders). The workflow continues to process the parent item.
* **`Items`:**
    * **Description:** Each element from the modified list is "flattened" and outputted as a *separate n8n item*.
    * **Use Case:** This is extremely powerful for **parallel processing** or when you need to run subsequent nodes independently for each sub-item in a list. For example, if an order has 5 line items and you want to process each line item individually (e.g., update stock, send a notification for that specific item).

## Advanced Use Cases for Data Manipulation:

* **Flattening Nested Data:** Use `Output Mode: Items` after a `Get` or `Set` operation on a list to convert an array of objects *within* a single n8n item into *multiple distinct n8n items*. This is a common pattern to break down complex inputs.
* **Conditional List Filtering:** Combine `Remove` operation with a `Condition` to precisely filter lists based on complex criteria without needing separate `If` nodes for every element.
* **Dynamic Data Structuring:** Using `Set` operation with expressions to build new arrays from scratch, or to inject dynamically generated data into existing lists.
* **Batching with List Operations:** While `Split In Batches` handles the overall workflow item flow, `Item Lists` can prepare the *contents* of a list for batching by extracting specific ranges or subsets using `Get` operations.

## Tips & Best Practices for Manipulation:
* **Visualize Input & Output:** Always trace how your data transforms. Use n8n's "Execution List" to inspect the `json` and `binary` data before and after the `Item Lists` node.
* **Test Each Operation Mode:** The `Operation` and `Output Mode` settings are critical. Test combinations with small datasets to understand their precise effect on your data structure.
* **Combine with Other Nodes:** The `Item Lists` node often works in conjunction with `Set` (to prepare data for lists), `Code`/`Function` (for complex list element transformations), or database nodes (to store/retrieve list data).
* **Expressions are Essential:** Nearly all advanced manipulation with `Item Lists` will rely on expressions for dynamically selecting fields, indices, or values.