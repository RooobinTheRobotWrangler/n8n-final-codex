# Email Trigger Node

**Source:** [n8n Docs - Email Trigger node documentation](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.emailtrigger/)

## Overview
The Email Trigger node is a **trigger node** that initiates a workflow whenever a new email is received at a specified email address. This is a powerful way to automate tasks that rely on incoming email communication, allowing n8n to parse email content, attachments, and headers to drive subsequent workflow actions.

## Configurable Options:

* **Email ID:**
    * **Description:** A unique identifier that forms part of the email address your n8n instance listens to. For example, if your n8n receives emails at `[something]@yourn8n.com`, you'd put `something` here.
    * **Accepted values:** A string, often alphanumeric.
* **Response Mode:**
    * **Description:** Controls how n8n responds to the incoming email.
    * **Options:**
        * **None:** The workflow processes the email without sending an automatic reply.
        * **Standard Reply:** Sends a simple "Received" reply to the sender.
        * **Custom Reply:** Allows you to define a custom email subject and body for the reply.
* **Check interval (minutes):**
    * **Description:** How often (in minutes) the node should check for new emails.
    * **Accepted values:** A positive integer (e.g., `1` for every minute).

## Input Structure:
As a trigger node, the Email Trigger node does **not** have an input. It initiates the workflow when an email is received.

## Output Structure:
When the Email Trigger node receives a new email, it outputs a single item (or multiple if there are multiple parts/attachments) containing parsed information from the email. Key fields typically include:

* **`json.from`:** The sender's email address.
* **`json.to`:** The recipient's email address (the one the email was sent to).
* **`json.subject`:** The subject line of the email.
* **`json.text`:** The plain text body of the email.
* **`json.html`:** The HTML body of the email (if available).
* **`json.attachments`:** An array containing details about any attachments, with the actual attachment content available as binary data.
* **`json.messageId`:** A unique identifier for the email message.
* **`json.headers`:** All email headers as key-value pairs.

**Example JSON Output (for a simple incoming email):**

```json
[
  {
    "json": {
      "from": "sender@example.com",
      "to": "your-email-id@yourn8n.com",
      "subject": "New Order Confirmation #12345",
      "text": "Your order #12345 has been confirmed...",
      "html": "<p>Your order #12345 has been confirmed...</p>",
      "messageId": "<some.unique.id@mail.example>",
      "headers": {
        "Content-Type": "text/plain; charset=UTF-8",
        "X-Mailer": "MailerApp"
      },
      "attachments": []
    },
    "binary": {}
  }
]
Potential Use Cases:
Order Processing: Triggering actions based on order confirmation emails (e.g., updating a spreadsheet, sending a Slack notification).
Customer Support: Creating support tickets in a CRM when emails are received from customers.
Data Extraction: Parsing regular emailed reports and extracting specific data points for analysis.
File Handling: Automatically saving email attachments to cloud storage or processing them.
Automated Replies: Sending custom auto-replies based on the content or sender of an email.
Tips & Best Practices:
Dedicated Email Address: It's best practice to use a dedicated email address specifically for n8n triggers to avoid mixing with personal emails and to keep things organized.
Regular Expressions for Parsing: Use the Code or Function node with regular expressions to extract specific pieces of information from the subject, text, or html body of the email.
Handle Attachments: If expecting attachments, know how to access and process the binary data in subsequent nodes.
Error Handling: Implement robust error handling in case emails are malformed or expected data is missing.
Consider Security: Be cautious about what information is processed from untrusted email sources, especially if it leads to sensitive actions.
<!-- end list -->

