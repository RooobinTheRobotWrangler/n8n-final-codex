# HTTP Request Node

**Source:** [n8n Docs - HTTP Request node documentation](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.httprequest/)

## Overview
The HTTP Request node is a core n8n node used to make requests to external APIs or web services. It's highly versatile and can be used to connect n8n to virtually any service that exposes a REST API, even if n8n doesn't have a dedicated integration node for it.

## Configurable Options:

The HTTP Request node offers a wide range of configurable options to tailor your requests:

* **URL:** The full URL of the endpoint you want to send the request to (e.g., `https://api.example.com/v1/users`). This field supports [expressions](https://docs.n8n.io/code/expressions/) for dynamic URLs.
* **Method:** The HTTP method to be used for the request. Common options include:
    * `GET` (Retrieve data)
    * `POST` (Send data to create a new resource)
    * `PUT` (Update an existing resource, replacing it entirely)
    * `PATCH` (Partially update an existing resource)
    * `DELETE` (Remove a resource)
    * `HEAD` (Retrieve headers only, without the body)
    * `OPTIONS` (Retrieve communication options for the target resource)
* **Authentication:** Defines how to authenticate with the external service. Options vary widely but commonly include:
    * `None`
    * `Basic Auth` (Username and Password)
    * `OAuth2` (Complex, token-based authentication)
    * `API Key` (Key sent in headers or query parameters)
    * `Header Auth` (Custom headers for authentication)
    * *(Note: For robust security, n8n recommends using [Credentials](https://docs.n8n.io/integrations/managing-credentials/) where possible.)*
* **Headers:** Allows adding custom HTTP headers as key-value pairs (e.g., `Content-Type: application/json`, `X-Custom-Header: value`).
* **Query Parameters:** Key-value pairs that are appended to the URL as query strings (e.g., `?param1=value1&param2=value2`).
* **Body (for POST/PUT/PATCH):** Defines the data sent in the request body. Common types include:
    * `JSON` (Standard for most REST APIs)
    * `Form Data (multipart/form-data)` (Often used for file uploads)
    * `Form Data (application/x-www-form-urlencoded)`
    * `Raw` (For custom body content, e.g., XML or plain text)
* **Timeout:** The maximum time (in milliseconds) the node waits for a response before timing out.
* **Follow Redirects:** If `true`, the node automatically follows HTTP redirects (e.g., 301, 302 responses).
* **TLS/SSL:** Options for handling SSL certificate validation (e.g., `Accept unauthorized TLS/SSL certificates` for testing, generally not recommended for production).
* **Binary Data:** How the node handles binary data in inputs and outputs (e.g., expecting an image file as input, or receiving a PDF as output).

## Input Structure:
The HTTP Request node can take any number of items as input. The node processes each input item, meaning you can use [expressions](https://docs.n8n.io/code/expressions/) to dynamically construct parts of the HTTP request (like URL, headers, or body) based on the data of the current input item.

**General Input Format:**
n8n nodes typically expect input as an array of JSON objects, where each object represents an "item" in the workflow.

```json
[
  {
    "json": {
      "id": "item123",
      "dataToSend": {
        "name": "Alice",
        "email": "alice@example.com"
      }
    },
    "binary": {}
  },
  {
    "json": {
      "id": "item456",
      "dataToSend": {
        "name": "Bob",
        "email": "bob@example.com"
      }
    },
    "binary": {}
  }
]
(You would use expressions like {{ $json.dataToSend }} in the node's body field, or {{ $json.id }} in the URL path.)

Output Structure:
The output of the HTTP Request node is generally the response received from the external API. This is most commonly JSON data, but can also be plain text, XML, or binary data (e.g., files). The output will maintain the n8n item structure.

Example JSON Output (if API returns JSON):

JSON

[
  {
    "json": {
      "statusCode": 200,
      "headers": {
        "content-type": "application/json"
      },
      "body": {
        "userId": "123",
        "status": "created",
        "timestamp": "2025-06-10T12:00:00Z"
      }
    },
    "binary": {}
  }
]
(If the API returns binary data like a PDF, the binary object in the output item would contain details about that file.)

Potential Use Cases:
Integrating with custom APIs: Connect to any internal or external service that has a REST API, even without a pre-built n8n node.
Web Scraping: Fetching data from public web pages (though often combined with other nodes like HTML Extract).
Sending Notifications: Posting messages to chat platforms or custom notification services.
Data Synchronization: Pulling data from one system and pushing it to another.
Triggering Remote Actions: Initiating processes in other applications via their API endpoints.
Tips & Best Practices:
Always Refer to API Documentation: The behavior, required parameters, and expected responses are dictated by the external API you're calling.
Use Expressions: Dynamically build requests using data from previous nodes.
Handle Errors: Use Continue On Error settings on the node or dedicated Error Workflows to gracefully manage API failures.
Manage Credentials Securely: Store API keys and secrets as n8n Credentials rather than directly in the node's settings for security.
Consider Rate Limits: Be aware of the API's rate limits and use the Wait node if necessary to prevent hitting them.
<!-- end list -->