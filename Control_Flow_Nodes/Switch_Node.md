# Switch Node

**Source:** [n8n Docs - Switch node documentation](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.switch/)

## Overview
The Switch node is a **control flow node** that allows you to direct incoming data items to different workflow branches based on matching a specific value against multiple predefined cases. Unlike the `If` node, which has two outputs (true/false), the Switch node can have multiple output branches, one for each "case" you define, plus an optional "default" branch. This makes it ideal for complex routing scenarios.

## Configurable Options:

* **Value:**
    * **Description:** The value from the incoming item that you want to evaluate against different cases. This is almost always an [expression](https://docs.n8n.io/code/expressions/) pointing to a specific property (e.g., `{{ $json.orderStatus }}`, `{{ $json.userRole }}`).
    * **Accepted values:** Any data type that can be compared.
* **Cases:**
    * **Description:** A list of specific values. If the `Value` from the input item matches one of these cases, the item will be sent out through the corresponding output branch.
    * **Adding Cases:** You can add multiple cases, each creating a new output branch for the node.
* **Case Options (for each case):**
    * **Value:** The specific value to match against the main `Value` field.
    * **Operator:** How to compare the values (e.g., `Equal`, `Not Equal`, `Contains`, `Starts With`, `Ends With`, `Is Empty`, etc.).
    * **Type:** The data type for comparison (`String`, `Number`, `Boolean`). Crucial for accurate matching (e.g., `10` as a number vs. `10` as a string).
* **Default Output:**
    * **Description:** If enabled, creates an additional output branch. Any items that do not match any of the defined `Cases` will be routed through this "Default" branch.
    * **Accepted values:** Boolean (checkbox).

## Input Structure:
The Switch node takes an array of JSON objects as input, with each object representing an item. It processes each item individually, evaluating the `Value` against its configured `Cases`.

```json
/* Example Input: Items with a 'status' property */
[
  { "json": { "orderId": "A1", "status": "pending" } },
  { "json": { "orderId": "B2", "status": "shipped" } },
  { "json": { "orderId": "C3", "status": "delivered" } },
  { "json": { "orderId": "D4", "status": "cancelled" } }
]
Output Structure:
The Switch node has multiple output branches. For each incoming item, it will be sent out through only one of the branches:

Case-specific Branches: An item exits through the branch corresponding to the Case it matches.
Default Branch: If enabled, items that don't match any defined Case exit through this branch.
The JSON structure of the items passed through remains unchanged; only their routing is affected.

Potential Use Cases:
Complex Routing: Directing different types of events or data records to specific processing workflows (e.g., "new user" goes to onboarding, "payment failed" goes to error handling, "password reset" goes to verification).
Status-based Actions: Performing different actions based on the status of an item (e.g., pending, approved, rejected).
User Role-Based Logic: Tailoring workflow behavior based on the role of a user (e.g., admin, editor, viewer).
API Response Handling: Processing different API response codes (e.g., 200, 400, 404) or specific error messages.
Categorization: Sorting items into predefined categories.
Tips & Best Practices:
Use Specific Values: The Switch node works best with discrete, specific values rather than broad ranges. For ranges or more complex "true/false" type conditions, the If node might be more suitable.
Order of Cases: In some scenarios, if an item could match multiple cases, the order of your cases in the Switch node might matter, though n8n typically evaluates them sequentially.
Always Consider a Default: Enabling the "Default Output" branch is a good practice for robust workflows, ensuring that items that don't fit any specific case are still handled (e.g., logged, sent to an error queue).
Combine with Merge Node: After a Switch node, you often use Merge nodes (especially in Append or Wait mode) to bring the various branches back together later in the workflow, if subsequent processing needs to happen to all items regardless of their path.
Inspect Data: Use test runs to ensure the Value you are evaluating (e.g., {{ $json.status }}) actually contains the expected data and type for accurate matching.