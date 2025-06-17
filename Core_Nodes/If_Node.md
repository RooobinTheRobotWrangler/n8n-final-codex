# If Node

**Source:** [n8n Docs - If node documentation](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.if/)

## Overview
The If node allows you to create conditional logic in your n8n workflows. It evaluates a given condition (or multiple conditions) and then directs incoming data items to one of two branches: a "true" branch (if the condition is met) or a "false" branch (if the condition is not met). This enables workflows to behave differently based on dynamic data.

## Configurable Options:

* **Value 1:** The first value for comparison. This can be static text/number or, most commonly, an [expression](https://docs.n8n.io/code/expressions/) (e.g., `{{ $json.orderTotal }}`).
* **Operation:** The comparison operator to use between Value 1 and Value 2. Common operations include:
    * `Equal` (`=`)
    * `Not Equal` (`!=`)
    * `Contains`
    * `Not Contains`
    * `Starts With`
    * `Ends With`
    * `Is Empty` / `Is Not Empty`
    * `Is True` / `Is False`
    * `Greater` (`>`)
    * `Greater or Equal` (`>=`)
    * `Less` (`<`)
    * `Less or Equal` (`<=`)
* **Value 2:** The second value for comparison. This can also be static or an [expression](https://docs.n8n.io/code/expressions/) (e.g., `{{ $json.minOrderValue }}`).
* **Case Sensitive:** (Boolean checkbox) If checked, text comparisons will differentiate between uppercase and lowercase letters (e.g., "Apple" is not equal to "apple"). If unchecked, comparisons are case-insensitive.
* **Type:** Specifies how the values should be compared (e.g., `String`, `Number`, `Boolean`, `DateTime`). Setting the correct type is crucial for accurate comparisons (e.g., ensuring "10" as a number is greater than "5", not just alphabetically).
* **Add Condition / Condition Group:** Allows you to add multiple conditions and combine them with `AND` or `OR` logic. Condition Groups let you build more complex logical structures (e.g., `(A AND B) OR C`).

## Input Structure:
The If node typically takes an array of JSON objects, where each object represents an item from a previous node. It processes each item individually, evaluating the conditions for each one.

```json
[
  {
    "json": {
      "itemStatus": "pending",
      "price": 150
    }
  },
  {
    "json": {
      "itemStatus": "completed",
      "price": 80
    }
  }
]
Output Structure:
The If node has two output branches:

True Branch: Outputs all items for which the condition(s) evaluated to true.
False Branch: Outputs all items for which the condition(s) evaluated to false.
The JSON structure of the items passed through remains unchanged, only their routing is affected.

Potential Use Cases:
Conditional Routing: Directing orders to different systems based on their status (e.g., "pending" orders go to an approval workflow, "completed" orders go to fulfillment).
Filtering Data: Only processing items that meet a certain criteria (e.g., price greater than 100).
Error Handling: Checking if an API response contains an error field and routing to an error logging system if it does.
Branching Workflows: Executing different actions based on a user's role or input.
Data Validation: Checking if a required field Is Not Empty before proceeding.
Tips & Best Practices:
Use the Correct Type: Always ensure the Type field (String, Number, Boolean, etc.) matches the type of data you're comparing to avoid unexpected results (e.g., 5 > 10 is true if compared as strings, but false if compared as numbers).
Expressions are Key: Most real-world uses of the If node involve expressions to compare dynamic data.
Test Thoroughly: Use test data to verify that your conditions are routing items as expected for all possible scenarios.
"Show Merged Conditions": Use this option (when adding multiple conditions) to visualize how n8n groups your AND/OR logic.
Combine with Merge Node: After an If node, you often use a Merge node (set to Merge By Matching ID or Merge By Position) to bring the branches back together later in the workflow.
<!-- end list -->