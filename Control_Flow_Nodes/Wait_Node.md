# Wait Node

**Source:** [n8n Docs - Wait node documentation](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.wait/)

## Overview
The Wait node is a **control flow node** that pauses the execution of a workflow for a specified duration or until a particular date and time. It's essential for implementing delays, respecting API rate limits, scheduling follow-ups, or waiting for external events to occur before continuing a workflow.

## Configurable Options:

* **Mode:** Defines how the wait duration is determined.
    * **Wait for a specified amount of time:** Pauses the workflow for a fixed duration.
        * **Value:** The number of units to wait.
        * **Unit:** The unit of time (e.g., `seconds`, `minutes`, `hours`, `days`).
    * **Wait until a specified date & time:** Pauses the workflow until a precise future moment.
        * **Date & Time:** The exact date and time to wait until. This can be a fixed date/time or an [expression](https://docs.n8n.io/code/expressions/) (e.g., `{{ $json.eventDate }}`).
        * **Timezone:** The timezone associated with the specified date and time. Crucial for accurate scheduling.
    * **Wait for a webhook:** Pauses the workflow until an external webhook is received at a generated URL. This is a powerful feature for long-running processes that require an external trigger to continue.
        * **Wait Time (minutes):** Maximum time to wait for the webhook before timing out.
        * **Webhook URL:** The unique URL that needs to be called to resume the workflow.

## Input Structure:
The Wait node takes an array of JSON objects as input. It will pause the processing of these items according to its configuration. All incoming items are typically held by the Wait node until the condition is met (time elapses or webhook is received).

```json
/* Example Input: Items to be delayed */
[
  { "json": { "notificationId": "N123", "message": "Reminder for task" } },
  { "json": { "notificationId": "N124", "message": "Follow up email" } }
]
Output Structure:
When the wait condition is met, all the items that entered the Wait node will be released and passed to the next node in the workflow. The JSON structure of the items typically remains unchanged.

Example Output (after delay):

JSON

[
  { "json": { "notificationId": "N123", "message": "Reminder for task" } },
  { "json": { "notificationId": "N124", "message": "Follow up email" } }
]
Potential Use Cases:
API Rate Limit Management: Pausing briefly between API calls to avoid hitting rate limits.
Scheduled Follow-ups: Sending a follow-up email or message a set number of hours or days after an initial action (e.g., sending a welcome email immediately, then a follow-up 3 days later).
Timed Releases: Releasing new content or features at a specific date and time.
Asynchronous Processes: Waiting for an external system to complete a long-running task and signal back via a webhook before the workflow continues.
Drip Campaigns: Creating sequences of actions that happen at intervals.
Tips & Best Practices:
Match Timezone: When using "Wait until a specified date & time," always ensure the timezone matches the context of the date/time value to avoid unexpected delays or early triggers.
Webhook Timeout: For "Wait for a webhook" mode, set a realistic "Wait Time" to prevent workflows from hanging indefinitely if the webhook is never received.
Consider Error Handling: If a webhook is not received within the timeout, build subsequent logic to handle the timeout (e.g., log an error, send a notification).
Avoid Excessive Delays: Long wait times can consume n8n resources and increase the chance of issues. For very long, multi-day waits, consider breaking the workflow into smaller, chained workflows or using the "Wait for a webhook" mode if applicable.
Use Expressions: Dynamically calculate wait times or target dates using expressions to make your delays flexible.