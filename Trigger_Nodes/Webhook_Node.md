# Webhook Node

**Source:** [n8n Docs - Webhook node documentation](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.webhook/)

## Overview
The Webhook node is a **trigger node** that starts a workflow when it receives an incoming HTTP request (a "webhook"). This makes n8n workflows highly reactive, allowing them to respond instantly to events from external services like payment gateways, CRM systems, email providers, or custom applications. Each webhook has a unique URL that you provide to the external service.

## Configurable Options:

* **HTTP Method:**
    * **Description:** The HTTP method(s) the webhook should respond to. You can choose `GET`, `POST`, `PUT`, `PATCH`, `DELETE`, or `All`. `POST` is most commonly used for webhooks that send data.
    * **Accepted values:** `GET`, `POST`, `PUT`, `PATCH`, `DELETE`, `All`.
* **Path:**
    * **Description:** The unique path that forms part of the webhook URL (e.g., `my-unique-path`). n8n will provide the full URL, including this path.
    * **Accepted values:** A string, often lowercased with hyphens.
* **Response Mode:**
    * **Description:** Controls how n8n responds to the incoming webhook request.
    * **Options:**
        * **None:** The webhook responds immediately with a generic "200 OK" status, even if the workflow is still processing.
        * **Immediately:** The webhook responds immediately with a generic "200 OK" status, but only after the node itself finishes executing (not the entire workflow).
        * **On "Respond to Webhook" Node:** The webhook request remains open until a separate "Respond to Webhook" node is encountered later in the workflow. This allows the workflow to send a custom response (status code, body, headers) back to the calling service after processing.
* **Authentication:**
    * **Description:** Adds security to your webhook by requiring specific credentials for access.
    * **Options:** `None`, `Basic Auth` (username/password), `Header Auth` (custom header/value).
* **Response Headers (when using "Respond to Webhook" node):**
    * **Description:** Allows you to define custom HTTP headers for the response when using the "On 'Respond to Webhook' Node" response mode.
* **Response Status Code (when using "Respond to Webhook" node):**
    * **Description:** Allows you to define the HTTP status code for the response (e.g., `200` for success, `400` for bad request, `500` for server error).

## Input Structure:
As a trigger node, the Webhook node does **not** have an input. It initiates the workflow based on an external HTTP request.

## Output Structure:
When the Webhook node triggers, it outputs a single item containing the data received from the incoming HTTP request. This typically includes:

* **`json.body`:** The main data payload sent in the request body (e.g., customer details from a CRM, payment information).
* **`json.query`:** Any query parameters from the URL (e.g., `?id=123`).
* **`json.headers`:** All HTTP headers sent with the request.
* **`json.method`:** The HTTP method used for the request (GET, POST, etc.).
* **`json.path`:** The path of the webhook URL that was hit.
* **`binary` data:** If a file was uploaded via the webhook (e.g., a form submission with an attachment), the binary data will be available here.

**Example JSON Output (for a POST request with JSON body):**
```json
[
  {
    "json": {
      "body": {
        "customer_id": "cust_abc",
        "event_type": "new_signup",
        "email": "newuser@example.com",
        "timestamp": "2025-06-10T16:00:00Z"
      },
      "query": {},
      "headers": {
        "content-type": "application/json",
        "user-agent": "Webhook-Sender/1.0"
      },
      "method": "POST",
      "path": "my-unique-path"
    },
    "binary": {}
  }
]
Potential Use Cases:
Instant Integrations: Connect n8n to services like Stripe (for new payments), Shopify (for new orders), Typeform (for new form submissions), or any platform that supports webhooks.
Custom API Endpoints: Create simple API endpoints for your own applications or scripts to send data to n8n.
Chatbot Integrations: Receive messages from chat platforms (e.g., Slack, Telegram) to trigger automated responses.
IoT Device Triggers: Receive data from smart devices when an event occurs.
Email Parsing: Integrate with email services that can send email content as a webhook.
Tips & Best Practices:
Security: Always use authentication (Basic Auth, Header Auth) for your webhooks, especially if they trigger sensitive operations.
Response Mode: On "Respond to Webhook" Node: Use this when the calling service expects an immediate and custom response back from your workflow. This is crucial for interactive services.
Test with Tools: Use tools like Postman, Insomnia, or online webhook testing services (e.g., webhook.site) to send test requests to your n8n webhook URL during development.
Payload Inspection: Always inspect the incoming webhook payload in n8n's "Execution List" to understand its exact structure, which helps in configuring subsequent nodes.
Idempotency: Design your workflows to be "idempotent" where possible, meaning if the same webhook triggers twice, it doesn't cause duplicate actions (e.g., check if an order already exists before creating it).
<!-- end list -->