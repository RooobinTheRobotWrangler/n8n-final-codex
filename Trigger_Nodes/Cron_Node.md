# Cron Node

**Source:** [n8n Docs - Cron node documentation](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.cron/)

## Overview
The Cron node is a **trigger node** that allows you to start a workflow automatically at specific, recurring intervals or times. It's ideal for scheduling tasks like daily reports, hourly data checks, or monthly cleanups. Unlike webhook triggers, it doesn't wait for an external event; it simply runs on a timer.

## Configurable Options:

* **Mode:** Defines how the schedule is set.
    * **Every X (Minutes/Hours/Days/Weeks/Months):** A simple way to set a recurring interval (e.g., "Every 15 minutes", "Every 1 day").
    * **Daily:** Runs once a day at a specific time.
    * **Weekly:** Runs on specific days of the week at a specific time.
    * **Monthly:** Runs on specific days of the month at a specific time.
    * **Custom (CRON Expression):** For advanced scheduling, allows you to use a standard CRON expression (e.g., `0 0 * * *` for midnight daily).
* **Timezone:** Allows you to specify the timezone for the schedule. This is crucial for ensuring the workflow runs at the intended local time, regardless of where the n8n server is located.
* **Start Date/Time:** (For specific modes) Allows you to set a future date/time for the cron job to begin.
* **End Date/Time:** (For specific modes) Allows you to set a future date/time for the cron job to stop.

## Input Structure:
As a trigger node, the Cron node does **not** have an input. It starts the workflow.

## Output Structure:
When the Cron node triggers, it outputs a single item containing information about the trigger event, primarily the current timestamp.

**Example JSON Output:**
```json
[
  {
    "json": {
      "mode": "everyHour",
      "interval": 1,
      "timezone": "Africa/Johannesburg",
      "processedAt": "2025-06-10T14:00:00.000Z"
    }
  }
]
(The processedAt timestamp indicates when the workflow was triggered.)

Potential Use Cases:
Scheduled Reporting: Generate and send daily, weekly, or monthly reports.
Data Synchronization: Periodically pull data from one system and update another (e.g., syncing customer lists).
Regular Checks: Monitor external services for changes (e.g., check website uptime, monitor stock levels).
Automated Cleanups: Run maintenance tasks like archiving old data or clearing temporary files.
Timed Notifications: Send reminders or alerts at specific times.
Tips & Best Practices:
Set Timezone Correctly: Always explicitly set the timezone to avoid confusion, especially if your n8n instance is in a different timezone than your intended schedule.
Use Descriptive Names: Give your Cron workflows clear names so you know their purpose at a glance.
Consider Error Handling: Even for scheduled tasks, ensure you have error handling in place in case external services are unavailable or data processing fails.
Test Thoroughly: When using "Custom (CRON Expression)", use online CRON validators to ensure your expression is correct before relying on it.
Avoid Overlapping Schedules: Be mindful of workflow execution times when setting short intervals to prevent workflows from overlapping or causing resource issues.
<!-- end list -->