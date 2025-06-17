# Community Nodes / Integrations Overview

**Source:** [n8n Docs - Third-Party Integrations](https://docs.n8n.io/integrations/builtin/third-party/), [n8n Docs - Managing Credentials](https://docs.n8n.io/integrations/managing-credentials/), [n8n Docs - Data Structure](https://docs.n8n.io/code/data-structure/)

## Overview
While n8n offers a robust set of [Core Nodes](https://docs.n8n.io/integrations/builtin/core-nodes/), its true power lies in its extensive collection of **Community Nodes (also known as Third-Party Integrations)**. These nodes allow you to connect your workflows directly to a vast ecosystem of external applications and services, enabling seamless data exchange and automation across your entire tech stack.

## What are Community Nodes?

* **Pre-built Connectors:** They are pre-built blocks that encapsulate the logic and API calls needed to interact with specific third-party services (e.g., Google Sheets, Salesforce, Slack, databases).
* **Simplified Integration:** They abstract away the complexity of making raw HTTP requests and handling authentication for each service, providing user-friendly configuration options.
* **Community-Driven & Official:** Many nodes are officially maintained by n8n, while others are contributed and maintained by the n8n community, expanding the platform's reach.

## Why are They Important?

* **Expand Capabilities:** Connect n8n to virtually any service you use, from CRM and marketing automation to databases and communication tools.
* **Accelerate Development:** Drastically reduce the time and effort required to integrate with external APIs.
* **Standardize Interactions:** Provide a consistent way to interact with different services, regardless of their underlying API design.

## Typical Authentication Methods

To use community nodes, you almost always need to authenticate n8n with the external service. n8n provides a secure [Credentials](https://docs.n8n.io/integrations/managing-credentials/) system to manage these connections. Common authentication methods include:

* **API Key:** A unique key provided by the service, typically sent in a header or query parameter.
* **OAuth2:** A secure, token-based authorization method where you grant n8n permission to access your data without sharing your actual username/password (common for Google, Slack, etc.).
* **Basic Authentication:** Username and password, typically sent encoded in the HTTP request header.
* **Bearer Token:** A token (often obtained via OAuth2 or an API key endpoint) sent in the `Authorization` header.
* **Service Account / JWT:** Often used for server-to-server communication, providing access based on a pre-configured service account.

**Best Practice:** Always use n8n's **Credentials** feature to store and manage your authentication details. Do not hardcode API keys or sensitive information directly into nodes.

## Common Data Formats

When interacting with external services via community nodes, you'll frequently encounter data in these formats:

* **JSON (JavaScript Object Notation):**
    * **Description:** The most common data format for web APIs. Human-readable and lightweight.
    * **How n8n handles:** n8n's internal data structure is JSON-based, making integration seamless. Most nodes expect and output JSON.
* **CSV (Comma Separated Values):**
    * **Description:** A plain text format for tabular data (spreadsheets).
    * **How n8n handles:** N8n has dedicated nodes (e.g., CSV node) to parse CSV input into JSON items and to convert JSON items into CSV output.
* **XML (Extensible Markup Language):**
    * **Description:** A markup language often used for data exchange, particularly in older systems or specific industries.
    * **How n8n handles:** N8n may have specific nodes or you might use the `Code` node with a parsing library to convert XML to JSON.
* **Plain Text:**
    * **Description:** Unformatted text.
    * **How n8n handles:** Can be read directly, but often needs parsing using `Code`/`Function` nodes or regular expressions to extract structured data.
* **Binary Data (Files):**
    * **Description:** Images, PDFs, documents, audio, video files.
    * **How n8n handles:** N8n stores binary data separately from JSON. Nodes like `HTTP Request` (for file uploads/downloads), `Google Drive`, `S3`, etc., are designed to handle binary data. The `_N8N_BINARY_DATA_` property in an item's JSON points to the actual binary data.

## Initial Setup for Integrations:

1.  **Add Credentials:** Before using most integration nodes, you'll first need to set up the corresponding credentials in n8n's "Credentials" section (usually accessed via the sidebar).
2.  **Drag and Drop Node:** Then, drag the specific community node onto your workflow canvas.
3.  **Select Credential:** In the node's settings, you'll select the credential you just created to authenticate.
4.  **Configure Operations:** Configure the node's operation (e.g., "Get a spreadsheet," "Send a message") and provide necessary parameters.