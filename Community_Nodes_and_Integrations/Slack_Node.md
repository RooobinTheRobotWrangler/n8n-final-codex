# Slack Node

**Source:** [n8n Docs - Slack node documentation](https://docs.n8n.io/integrations/builtin/app-nodes/n8n-nodes-base.slack/)

## Overview
The Slack node allows your n8n workflows to interact with Slack workspaces, channels, and users. It's an indispensable tool for automating team notifications, sending alerts, sharing files, managing channels, and integrating Slack into your broader automation ecosystem.

## Authentication: Slack OAuth

To use the Slack node, you need to authenticate n8n with your Slack workspace using **Slack OAuth**.

1.  **Create New Credential:** In n8n, go to **Credentials** (sidebar) > **New Credential**.
2.  Search for **"Slack"** and select **"Slack OAuth"**.
3.  **Choose Scopes:** This is crucial. You need to select the specific permissions (scopes) that your workflow requires. Common scopes include:
    * `chat:write`: To send messages.
    * `files:write`: To upload files.
    * `channels:read`: To get channel information.
    * `users:read`: To get user information.
    * `commands`: If you want to use Slack slash commands to trigger workflows.
    * *(Select only the minimum required scopes for security.)*
4.  **Connect Workspace:** Follow the prompts to sign into your Slack workspace and authorize n8n with the selected permissions.

## Configurable Options (Key Operations):

The Slack node offers various "Operations" to manage your Slack interactions, each with specific parameters:

* **Resource:** The type of Slack entity you want to interact with (e.g., `Message`, `File`, `Channel`, `User`).
* **Operation:** The specific action to perform on that resource (e.g., `Post`, `Upload`, `Create`, `Get`).

Here are some common Operations:

1.  **Resource: Message / Operation: Post:**
    * **Purpose:** To send a new message to a Slack channel or user.
    * **Parameters:** `Channel` (ID or name, e.g., `#general` or `C123ABC`), `Text` (the message content), `As User` (send as a bot or as a user).
    * **Advanced Options:** `Blocks` (for rich message layouts using Block Kit JSON), `Thread TS` (to reply in a thread).
    * **Input:** Items containing message text or Block Kit JSON.

2.  **Resource: File / Operation: Upload:**
    * **Purpose:** To upload a file to a Slack channel.
    * **Parameters:** `Channel`, `Binary Data` (select the property that contains the binary file), `File Name`, `Title` (for the file).
    * **Input:** Items containing binary data (e.g., from an `HTTP Request` node downloading a file, or a `Google Drive` node reading a file).

3.  **Resource: Channel / Operation: Create:**
    * **Purpose:** To create a new public or private Slack channel.
    * **Parameters:** `Name` (of the channel, e.g., `project-updates`), `Private` (boolean).

4.  **Resource: User / Operation: Get:**
    * **Purpose:** To retrieve information about a specific Slack user.
    * **Parameters:** `User ID` (e.g., `U123XYZ`) or `Email Address`.
    * **Output:** An n8n item containing the user's profile information.

## Input Structure:
The input structure depends on the `Resource` and `Operation`. For `Message: Post`, you'd typically have a `json.text` property. For `File: Upload`, you'd need binary data attached to the item.

```json
/* Example Input for Message: Post */
[
  {
    "json": {
      "targetChannel": "#alerts",
      "alertMessage": "New critical error detected in Workflow X!"
    }
  }
]
Output Structure:
The output structure varies by Operation. For Message: Post, it will confirm the message was sent (e.g., ok: true). For File: Upload, it provides file metadata. For User: Get, it provides the user's profile data.

Example Output (from Message: Post operation):

JSON

[
  {
    "json": {
      "ok": true,
      "channel": "C123ABC",
      "ts": "1678886400.123456",
      "message": { /* details of the sent message */ }
    },
    "binary": {}
  }
]
Potential Use Cases:
Notifications & Alerts: Sending immediate notifications for errors, new sign-ups, payment confirmations, or critical events.
Daily/Weekly Reports: Posting automated summaries of data or workflow activity to a designated channel.
File Sharing: Automatically uploading generated reports, invoices, or other documents to Slack channels.
Team Communication: Creating new project channels automatically or sending onboarding messages to new team members.
Interactive Workflows: (Advanced) Using Slack's interactive messages (Block Kit) or slash commands to trigger workflows and receive structured responses.
Tips & Best Practices:
Least Privilege: When setting up Slack OAuth scopes, choose only the minimum permissions necessary for your workflow's actions.
Channel IDs vs. Names: For robustness, it's often better to use Slack Channel IDs (e.g., C123ABC) instead of channel names (#general), especially if channel names might change. You can get IDs using the Channel: Get operation.
Block Kit for Rich Messages: For visually appealing and interactive messages, learn to use Slack's Block Kit. You can construct Block Kit JSON in a Set or Code node and then pass it to the Slack node.
Error Handling: Implement error handling for Slack API errors, such as invalid channels, missing permissions, or rate limits.
Rate Limits: Be aware of Slack's API rate limits. For high-volume messages, use Split In Batches with appropriate delays.
User Lookups: If you need to send direct messages to users but only have their email address, use the User: Get operation with their email to find their Slack User ID first.